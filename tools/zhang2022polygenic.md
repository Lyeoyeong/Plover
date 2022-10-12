### Polygenic enrichment distinguishes disease associations of individual cells in single-cell RNA-seq data

***
文章链接：https://www.nature.com/articles/s41588-022-01167-z

发表时间：2022/09/01

发表期刊：Nature Genetics

作者与单位：Zhang, M.J., Hou, K., Dey, K.K. et al. from Department of Epidemiology, Harvard T.H. Chan School of Public Health, Boston, MA, USA

简介：scDRS是一个可以利用单细胞RNA-seq数据利用GWAS的结果计算单个细胞的polygenic disease risk的工具，与细胞的注释无关。

Code Availability：https://github.com/martinjzhang/scDRS_paper 

Data Availability：https://figshare.com/projects/Single-cell_Disease_Relevance_Score_scDRS_/118902

Instructions： https://github.com/martinjzhang/scDRS

***

##### Main

scDRS衡量单个细胞是否过表达GWAS给定的与疾病相关的基因集，*using an appropriately matched empirical null distribution to estimate well-calibrated P values*. 
作者在74个疾病/traits与16个单细胞RNA-seq数据上使用了scDRS，衡量了细胞类型与疾病的关系以及细胞类型内的heterogeneity。同时也应用在了不同的species（小鼠与人类）以及不同的测序平台。

***

##### Results


