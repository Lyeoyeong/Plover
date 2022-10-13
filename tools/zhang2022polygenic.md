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

分析一：作者把scDRS应用于TMS FACS数据（包含120个细胞类型） + 74个GWAS diseases/traits。

scDRS command:

    scdrs compute-score \
    --h5ad_file $H5AD_FILE\
    --h5ad_species mouse\
    --cov_file $COV_FILE\
    --gs_file $GS_FILE\
    --gs_species human\
    --flag_filter_data True\
    --flag_raw_count True\
    --n_ctrl 1000\
    --flag_return_ctrl_raw_score False\
    --flag_return_ctrl_norm_score False\
    --out_folder $OUT_FOLDER

    revision:
    python3 /n/home11/mjzhang/gwas_informed_scRNAseq/scDRS/compute_score.py \
    --h5ad_file $H5AD_FILE\
    --h5ad_species mouse\
    --cov_file $COV_FILE\
    --gs_file $GS_FILE\
    --gs_species human\
    --flag_filter True\
    --flag_raw_count True\
    --n_ctrl 1000\
    --flag_return_ctrl_raw_score False\
    --flag_return_ctrl_norm_score True\
    --out_folder $OUT_FOLDER

TS single-cell file process:

    # Get raw cell counts
    adata_full = sc.read_h5ad(DATA_FILE) 
    adata_full.X = adata_full.raw.X 
    ...

    # Add conbination metadata
    adata_full.obs['tissue'] = adata_full.obs['organ_tissue']
    adata_full.obs['tissue_celltype'] = ['%s.%s'%(x,y) for x,y in zip(adata_full.obs['tissue'], adata_full.obs['cell_ontology_class'])] 
    ...

    # Basic QC
    sc.pp.filter_cells(adata, min_genes=250)
    sc.pp.filter_genes(adata, min_cells=50)
    ...

    # Make cov file
    df_cov = pd.DataFrame(index=adata_raw.obs.index)
    df_cov['const'] = 1
    df_cov['n_genes'] = (adata_raw.X>0).sum(axis=1)
    df_cov.to_csv(DATA_PATH+'/ts_10X.cov', sep='\t')
    ...


[Figure 3: Disease associations at the cell-type level](https://www.nature.com/articles/s41588-022-01167-z/figures/3)

上面的图片展示了其中有代表性的19个细胞类型与22个disease。图中方块表示与disease有关连的celltype(FDR < 0.05), 叉号表示disease相关的异质性显著的celltype(FDR < 0.05)。

分析二：作者分析了TMS FACS数据中T细胞（3,769个细胞，15个组织）的异质性，使用了10个autoimmune disease的GWAS数据，并以Heighe做阴性对照的trait。

[Figure 4: Associations of T cells with autoimmune diseases](https://www.nature.com/articles/s41588-022-01167-z/figures/4)

（单细胞层面的分析）

作者关注了单个细胞与IBD的关联，确定了387个与IBD相关联(FDR < 0.1)的T细胞，分布在4个T细胞cluster中(out of 11)。其中有123个与IBD相关联的细胞是在cluster3（Treg-like）的T细胞中，这些细胞高表达Treg的marker gene(FOXP3, CTLA4, LAG3)，同时他们的marker基因与Treg signature有显著的overlap，并且非随机地分布在UMAP图上。

 衡量gene signature的相似性：

    MsigDB signatures and effector-like Treg program, two-sided Fisher's exact test。

衡量disease score 在UMAP图的随机分布与否：

    P < 0.001, MC test;

作者提取了这些与IBD相关联细胞富集的基因（与相同cluster中非IBD关联的细胞做差异分析），发现腹肌了NFkB signaling以及Th cell分化，TNF-mediated signaling等通路。

T细胞特征基因集的搜集：

    We used MSigDB71 (v7.1) to curate T cell signature gene sets, including naive CD4, memory CD4, effector CD4, naive CD8, memory CD8, effector CD8, Treg, Th1 (T helper 1), Th2 (T helper 2) and Th17 (T helper 17) signatures. For each T cell signature gene set, we identified a set of relevant MSigDB gene sets (22–34 gene sets; Supplementary Table 17), followed by selecting the top 100 most frequent genes in these MSigDB gene sets as the T cell signature genes; a gene was required to appear at least twice and genes appearing the same number of times were all included, resulting in 62 to 513 genes for the 10 T cell signature gene sets (Supplementary Table 24). 

小细胞亚群富集的pathway：

    Pathways enriched in the top 300 specifically expressed genes of the IBD-associated cells and non-associated cells in cluster 3 respectively. The color and number in each cell represent the log10 enrichment p-values (based on Enrichr implemented in GSEAPY124, 125; the implementation used an one-sided Fisher’s exact test).

同样，对于其他cluster中发现的与IBD相关联的细胞做了相同的分析。

作者比较了T cell effectorness gradient（利用pseudotime计算CD4与CD8的effectorness gradient）与disease score，发现effectorness与某些自身免疫病是相关联的（P < 0.005, MC test）.

（基因层面的分析）
最后，作者prioritize了与疾病相关联的基因。针对所有的TMS FACS细胞，作者计算了特定基因的表达与某个疾病的disease score的correlation，从而得到了与GWAS的基因共表达的基因。与已知的Mendelian form of disease基因与可能的药物靶点与做比较，作者发现相比于MAGMA，通过scDRS能更好的发现这些基因。

分析三：作者分析了神经细胞的异质性与脑部相关的疾病的关系。

[Figure 5: Associations of neurons with brain-related disease/traits and hepatocytes with metabolic traits](https://www.nature.com/articles/s41588-022-01167-z/figures/5)

衡量一个cell-level指标与disease score的关系：

    图：
      横轴为该score quantile bins, 纵轴为disease score。
    计算：
      Pearson’s correlation between the cell-level variable and the disease score。
      For jointly associating multiple cell-level variables with disease, we use the regression t-statistic as the test statistic, obtained from jointly regressing the disease score against the cell-level variables.


分析四：干细胞亚群异质性与代谢相关traits的关系

讨论TMS FACS的肝细胞(hepatocytes)与metabolic traits的关系。

metabolic traits include:

    Total cholesterol (TG), high-density lipoprotein (HDL), low-density lipoprotein (LDL), total cholesterol (TC), testosterone (TST), alanine aminotransferase (ALT), alkaline phosphatase (ALP), sex hormone-binding globulin (SHBG) and total bilirubin (TBIL)


***

[1]  Type I error：false-positive, rejects a null hypothesis that is actually true. Type II error is false-negative, if the investigator fails to reject a null hypothesis that is actually false in the population.
