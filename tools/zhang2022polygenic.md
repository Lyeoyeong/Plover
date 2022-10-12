### Polygenic enrichment distinguishes disease associations of individual cells in single-cell RNA-seq data

***

##### Main

文章链接：https://www.nature.com/articles/s41588-022-01167-z

发表时间：2022/09/01

发表期刊：Nature Genetics

作者与单位：Zhang, M.J., Hou, K., Dey, K.K. et al. from Department of Epidemiology, Harvard T.H. Chan School of Public Health, Boston, MA, USA

Code Availability：https://github.com/martinjzhang/scDRS_paper 

Data Availability：https://figshare.com/projects/Single-cell_Disease_Relevance_Score_scDRS_/118902

Instructions： https://github.com/martinjzhang/scDRS

简介：

文章中提到的scDRS可以利用GWAS的数据计算scRNA-seq数据中单个细胞的polygenic disease risk。

其原理是计算单个细胞是否过表达GWAS给定的与疾病相关的基因集，*using an appropriately matched empirical null distribution to estimate well-calibrated P values*. 

作者在74个疾病/traits与16个单细胞RNA-seq数据上使用了scDRS，衡量了细胞类型与疾病的关系以及细胞类型内的heterogeneity。同时也应用在了不同的species（小鼠与人类）以及不同的测序平台。


***

##### Results

###### 1. 算法介绍

首先，scDRS利用MAGMA从GWAS的sumamry statistics文件中构建一个与疾病相关的基因集（top 1000 MAGMA genes）。

之后，scDRS量化这些基因在每个细胞当中的表达，从而计算出一个**raw disease score**。在这个过程中，每个基因根据MAGMA的z-score被分配不同的**权重**，同时根据它在单细胞数据中的gene-specific technical noise也分配一个**相反的权重**（这个计算是根据对mean-variance relationship across genes建模来实现的）。

为了衡量统计学上的显著性，scDRS同时还计算了1000组对应的对照组基因集（与要计算的gene set有相同的大小，表达均值以及方差）单个细胞的**raw control score** at Monte Carlo (MC) samples。

最后，scDRS对刚才计算的raw disease score与raw control score做一个normalization，再计算单个细胞的P-values based on the empirical distribution.

scDRS的output为：单个细胞的与疾病相关的p值，标准化后的disease scores，以及1000组的control scores用于下游的分析

###### 2. 下游分析

scDRS的下游分析包括：

细胞类型层面的分析 - 引入之前分析中定义好的细胞类型信息，计算每个细胞类型中与疾病相关的异质性。

单细胞层面的分析 - 联系单个细胞的变量与它的disease score。

基因层面的分析 - prioritize与disease score相关联的基因。

###### 3. 数据集介绍

在这篇文章中，作者使用了74个disease/traits (average N=346K) 的GWAS summary statistic文件，16个scRNA-seq/snRNA-seq的数据，一共包括来自人或者鼠的1.3 million (1,300,000)细胞以及31个组织/器官。

共包括以下几个数据集：
* TMS (Tabula Muris Senis) mouse cell atlas
  * FACS (fluorescence-activated cell sorting) 数据用在初期的分析中，它包括23种组织，120种细胞类型，并利用SMART-seq2建库。
  * 其他 TMS 数据
* TS (Tabul Sapiens) human cell atlas
* 其他特定组织的，有细胞类型注释的cell atlas
   
   
###### 3. 稳定性与功效(power)评估

作者将scDRS与其他三个衡量基因集在单细胞的score的方法比较：Seurat cell-scoring function, Vision and VAM，并且展示了null simulations与causal siulations的结果。

* Null simulation
  * 方法：从TMS FACS数据中选择了10,000个细胞，并随机选择了1,000个可能的disease genes。利用MAGMA的z-score，作者给予各个基因权重并计算了scDRS的值。
  * 结果：scDRS与Seurat展示了well-calibrated P values，Vision有轻微上涨的type I error[1]，VAM有严重的type I error。

* Causal simulation
  * 方法：随机选择1,000个causal genes，随机选择500（from 10,000）个细胞，手动将这群细胞的causal genes的表达量提高。之后随机选择1,000 可能的dsease genes（与实际的causal genes有25%的overlap）来当作scDRS的input。在利用三种方法计算disease score的时候没有包含权重，因为做测试数据集的时候没有涉及到它。
  * 结果：scDRS的功效比Seurat与Vision更强，at FDR<0.1。作者说，这可能是因为scDRS在计算背景的时候还计算了gene-specific weights，即对于technical noise高的基因给予更低的权重。


###### 4. 实际数据中的应用例子

分析一：作者把scDRS应用于TMS FACS数据（包含120个细胞类型） + 74个GWAS summary stats。

[Figure 3: Disease associations at the cell-type level](https://www.nature.com/articles/s41588-022-01167-z/figures/3)

上面的图片展示了其中有代表性的19个细胞类型与22个disease。图中方块表示与disease有关连的celltype(FDR < 0.05), 叉号表示disease相关的异质性显著的celltype(FDR < 0.05)。

分析二：作者分析了TMS FACS数据中T细胞（3,769个细胞，15个组织）的异质性，使用了10个autoimmune disease的GWAS数据，并以Heighe做阴性对照的trait。

[Figure 4: Associations of T cells with autoimmune diseases](https://www.nature.com/articles/s41588-022-01167-z/figures/4)



***

[1]  Type I error：false-positive, rejects a null hypothesis that is actually true. Type II error is false-negative, if the investigator fails to reject a null hypothesis that is actually false in the population.

