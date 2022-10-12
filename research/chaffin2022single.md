### Single-nucleus profiling of human dilated and hypertrophic cardiomyopathy

***

##### Main

文章链接：https://doi.org/10.1038/s41586-022-04817-8

发表时间：2022/06/22

发表期刊：Nature

作者与单位：Mark Chaffin ... Patrick T.Ellinor from The Broad Institute, Cambridge, MA, USA


***

##### 研究背景，目标与实验设计

心衰的病因复杂，常见原因之一是左心室（LV）的扩张收缩功能障碍导致扩张型心肌病的发生（DCM），也有可能是以LV增厚为特征的肥厚性心肌病（HCM）导致。

为了在单细胞转录组水平研究心肌病的进程，作者收集了来自44个人的左心室样本并做了snRNA-seq（有技术重复），其中包括12个DCM，16个HCM，16个非心衰（NF）的志愿者。 

***

##### 样本收集与建库

非心衰样本来自没有心衰史的器官捐献志愿者，DCM与HCM的样本来自接受心脏移植手术的心衰患者的心脏。

建库使用10x Genomics的平台，试剂为Single cell 3′ solution, v3。基于推荐的实验流程有一些小改动。测序平台为Illumina Nextseq550，一个Flowcell平均有4个文库。

***

##### 数据预处理过程

1. 【cutadapt v1.18 】修剪同一个核苷酸的聚合物以及TSO [1]。
   
        max_error_rate = 0.1, min_overlap = 20 以及 max_error_rate = 0.07, min_overlap = 10。

2. 【Cellranger 4.0.0】 使用cellranger mkfastq命令转换为FASTQ文件，并使用cellranger count命令将fastq文件映射到GRCh38 pre-mRNA human reference (v2020-A) 。
   
        --expect-cells 5000。

3. 第一轮质控：质量控制是每个样本单独进行的。首先排除了6个低质量的样本，其标准为： <50% of reads in cells, <65% of reads confidently mapping to transcriptome, <90% valid barcodes, 以及非常低的Q30。再排除了2个UMI曲线中没有明显的ambient平台的样本。
   
4. 【Cellbender v0.2】使用cellbender remove-background来去除游离RNA对于细胞的影响。最终得到885,944个细胞核用于下游分析。
   
        --expected-cells 5000, --z-dim 100, --total-droplets-included 25000, --epochs 150, --learning_rate 1e-4, and --fpr 0.01. 对于LV_1539_1, LV_1549_1, and LV_1735_1), --learning_rate 设置为 5e-5 来达到训练的收敛。

5. 【scR-Invex】使用该软件将每个样本的reads对应到外显子，内含子以及或者横跨两者的地方。对于每一个barcode，作者计算了外显子在整个read当中的百分比。因为是单核RNA测序，外显子比例高表示该droplet中来自细胞质的东西的比例高。

6. 【scanpy v1.6.0】使用sc.pp.highly_variable_genes(flavor = ‘seurat_v3’)命令选择2000个最差异表达的基因。使用sc.pp.normalize(1e4) 与 sc.pp.log1p()对count数据做标准化处理。使用sc.pp.scale()对这些基因的表达进行归一化：使每维特征均值为0且方差为1。利用sc.tl.pca()做主成分分析。

7. 【Harmony-pytorch v0.1.4】针对不同患者以及不同实验造成的转录组的巨大差异，作者应用了Harmony来调整上述方式计算出的PCA。
   
        patient as batch indicator

8. 【scanpy v1.6.0】基于50个调整后的PCA，利用umap method来计算连通性，以及构建近邻图。使用umap对数据作可视化，使用Leiden方法进行聚类得到47个细胞核组。
   
        sc.pp.neighbors(n_neighbors = 15); sc.tl.umap(min_dist = 0.2); sc.tl.leiden(resolution = 1.5)

9.  【ndd python library】利用Bayesian entropy estimation计算每个细胞核的entropy。

10. 【Scrublet】对于各个样本，利用Scrublet计算每个细胞核的双细胞率。

11. 第二轮质控：根据上述的结果，移除了7个高线粒体百分比/高外显子百分比/低熵值的cluster（63,068 droplets），移除了11个被认定为双细胞群的cluster（77,534 droplets）。

12. 第三轮质控：考虑到样本准备过程以及不同建库过程出现的差异，在每个（样本x聚类）组合间进行第三轮的质控。在每个（样本x聚类）的组合中，设置上限为以75个百分位 + 1.5倍四分位距（针对total UMI、基因数、熵值、log(n_gene) × entropy、线粒体比例，双细胞比例，外显子比例），下限为25个百分位 - 1.5倍四分位距（针对total UMI、基因数、熵值、log(n_gene) × entropy）。
    
    对于少于30个细胞核的sample x cluster，过滤阈值为total UMI > 15,000, n_gene > 6,000, entropy < 8, proportion of exonic reads > 0.18, doublet score > 0.30, and log(n_gene) × entropy > 75。最终的过滤阈值设定为 total_UMI < 150, n_gene < 150, pct_mito > 5%。此过程一共过滤了280,630个droplets。 

13. 第四轮质控：为了移除剩下的低质量细胞以及在每个细胞类型中被错误分类的细胞，对于剩下的605,314个细胞核利用cosine distance进行重聚类，此过程使用100个PCA，Leiden聚类的resolution设置为0.6。在主要的细胞类型重新计算近邻图（基于用Harmony调整过的PCA），并迭代进行聚类，resoution参数从0.05以0.05的步长提高到1，停止条件为：如果产生一个分群，且该分群没有基因的AUC > 0.6 in predicting that class [2]。
    
    错误分群的寻找：利用sc.tl.score_genes()计算前50个特征基因（用AUC排序，针对每个细胞类型），聚集其他细胞类型的小cluster被认定为是被错误分类的细胞，并予以剔除。此过程一共过滤了12,625个droplets。

经过预处理，最终得到被分类为21个cluster的592,689个droplets继续下游的分析。 

***

##### Results

第一部分结果：心肌病的转录组研究

未完。

***

[1]  TSO：https://kb.10xgenomics.com/hc/en-us/articles/360001493051-What-is-a-template-switch-oligo-TSO-

[2] AUC：https://en.wikipedia.org/wiki/Receiver_operating_characteristic 