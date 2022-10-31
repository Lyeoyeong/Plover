# Co-varying neighborhood analysis identifies cell populations associated with phenotypes of interest from single-cell transcriptomics

***

##### Main

文章链接：https://www.nature.com/articles/s41587-021-01066-4

发表时间：2021/10/21

发表期刊：Nature Biotechnology

作者与单位：
Yakir A. Reshef, Laurie Rumker, ... Soumya Raychaudhuri.
Center for Data Sciences, Brigham and Women’s Hospital, Boston, MA, USA

Code Availability：
An open-source repository containing code for running CNA is available at https://github.com/immunogenomics/cna; 

An open-source repository containing code underlying all figures and tables is available at https://github.com/immunogenomics/cna-display;

An open-source repository containing code underlying all simulations is available at https://github.com/immunogenomics/cna-sim. 

---

##### 算法介绍


![原理示意图](https://media.springernature.com/full/springer-static/image/art%3A10.1038%2Fs41587-021-01066-4/MediaObjects/41587_2021_1066_Fig1_HTML.png?as=webp)

**图a**: 

给定一个单细胞数据集，来自4个样本（Sample1 - Sample4）。

CNA为数据集中的每个细胞定义一个转录neighborhood。

这个neighborhood是根据其他细胞在cell-cell similarity graph (nearest neighbor network, SNN, 可以通过Scanpy或Seurat得到)经过random walk从该细胞到走固定步数到anchor cell的概率来确定的。

A-E是neighborhood的例子。

**图b**: 

当我们把样本分开，我们可以看到这些样本的neighborhood的abundance有co-variation：比如在Sample1,2,3中，B,D,E的丰度变高，则A,C的丰度变低。

**图c**: 

把abundance量化以后我们可以得到neighborhood abundance matrix (NAM), 行为样本，列为neighborhood，值为样本在该neighborhood中的abundance fraction。

**图d**: 

对NAM矩阵进行因子化(factorization)，如使用PCA。此时对于NAM-PC，per-neighborhood的loadings表示neighborhood共变化pattern，per-sample的loadings表示对应共变化的pattern在sample中的程度。

---

##### 应用1:CNA发现Rheumatoid arthritis中的Notch activation gradient

作者对于27,216个来自6个RA患者与6个OA患者的synovial joint tissue的fibroblast的单细胞数据使用了CNA分析。

CNA发现NAM-PC1是在这个数据集中的主要signal：NAM-PC1解释了NAM中39%的variance，而其他的PC没有超过12%的。NAM-PC1与Notch通路中的重要基因PRG4，FN1有很强的相关性，而且与NAM-PC1相关的基因中有两个Notch基因集明显富集。

---
##### 应用2:CNA reines sepsis-associated blooc cell populations

为了评估CNA在有更多细胞类型的数据中识别case-control的关联的能力，作者接下来对于来自29个sepsis患者与36个非sepsis患者的102,814个scRNA-seq数据进行了CNA的分析。

CNA确定了在sepsis中扩大的群体。其中44%来自MS1，其他来自MS2，MS3与MS4。

与per-cell neighborhood coefficients相关联的基因富集了RAC1激活的基因组（一个已知与sepsis相关的途径）。

CNA在这个数据集中发现了很大的 within-cluster heterogeneity，比如MS4包含一个明显扩大的亚群和明显减少的亚群。