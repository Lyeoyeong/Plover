# Variance-adjusted Mahalanobis (VAM): a fast and accurate method for cell-specific gene set scoring

***

文章链接：https://academic.oup.com/nar/article/48/16/e94/5868339

发表时间：2020/07/07

发表期刊：Nucleic Acids Research

记录这篇文章主要是为了对这篇文章背景部分提到的利用不同原理做gene set scoring方法做一个笔记。

***

这篇文章主要讨论single sample gene set testing methods。主要可以分为三类：

* Random walk methods 
* Principal component analysis(PCA)-based methods
* Z-scoring methods

###### Random walk methods

随机行走的方法(e.g. GSVA, ssGSEA) ：基于对gene ranks计算的的Kolmogorov-Smirnov(KS)类似的随机行走统计量对每个样本生成sample-level pathway scores。AUCell也是基于gene rank来计算的，但是用比GSVA或ssGSEA更简单的算法，因为它不把gene set size以及基因表达在数据集中distribution的考虑进去。

###### PCA-based methods

基于PCA的方法(e.g. PAGODA, PLAGE)：对于每个pathway的基因表达进行PCA，然后取每个sample在第一个PC当中的投影为该sample的pathway score。

###### Z-scoring methods

Z-score方法（e.g. GSVA, ssGSEA)：基于标准化后的pathway genes的平均表达量来计算pathway score。

###### 讨论

这些都在bulk RNA-seq当中被广泛应用，以GSVA与ssGSEA最popular。GSVA与ssGSEA，PLAGE等方法是为了计算bulk RNA-seq数据而被开发的，所以是针对非稀疏的矩阵的。AUCell，scSVA与Vision是针对单细胞表达量数据的，但是没有针对单细胞数据做特殊的准备。PAGODA是专门针对单细胞数据分析而设计的，专门解决scRNA-seq的稀疏性高与technical noise多的特点，但PAGODA主要关注无监督分析以及population-level analysis，它的sample-level gene score缺乏推理支持。以及对于Z-score的方法，虽然当表达数据遵循不相关的多变量正态分布时，Z评分方法产生的路径得分应该具有标准的正态分布，但这种分布假设对于稀疏的scRNA-seq数据并不成立。