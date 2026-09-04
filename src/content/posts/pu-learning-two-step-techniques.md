---
title: 'PU learning: Two-Step Techniques'
description: '记录 PU Learning 两步法中可靠负样本、训练与模型选择的思路。'
publishedAt: '2021-05-14 16:41:26'
tags: [semi-supervised learning,综述学习,pu learning]
draft: false
---

之前的PU Learning初步中有介绍到，在满足可分假设和平滑假设时，Two-Step Techniques是优选且常用的方案。

在Two-Step方案中一般会包含如下几个步骤：
1. 识别出可信的负样本
2. 使用监督或半监督技术使用正样本和可信负样本进行训练
3. （可选）选择步骤2中效果最好的模型

综述中介绍方法是，常常使用比较传统的机器学习模型，比如朴素贝叶斯之类。在1202年深度学习继续称霸的今天，我觉得其中一些思想比具体的算法更有意义一些。

----

## **Step1: 识别可信负样本（和正样本）**
负样本的采集在很多领域都是非常必要的。在facebook的负样本为王的论文中 强负样本的加入效果显著，是一个很值得关注的方向。这部分往往和向量/特征匹配，meta-learning相关度比较高。
此外，之前提到过的PU learning 初步中提到的biased learning也可以在这一步负责提供负样本，比如bagging SVM。

### Spy
将部分正样本加入到无标签数据中做间谍，把无标签数据作为负样本训练分类器，然后使用分类器对这部分间谍计算后验概率$P(y=1|x)$, 使用最小的作为阈值，无标签数据中后验概率低于该阈值都被划分为可信负样本

### Rocchio 
Rocchio是一个基于tf-idf特征的prototype based method，方法希望构建一个正样本的prototype和负样本的prototype，通过样本到正负样本prototype的距离远近来判断类型。此外后续还引入了K-means clustering等方法进行优化。但思路上和目前常用的prototype network很相似。

### PGPU
这个方案建立在probabilistic gap假设下，一般这种假设下，我们会训练关于标注的判别式模型估计$P(s=1|x)$ 进而计算$\Delta \widetilde{P}(x) = P(s=1|x) - P(s=0|x)$来评估标注难度。
在标注数据上计算标注难度阈值，对于无标签数据中标注难度低于这个阈值的判断为负样本。

### k-Means
使用K-means 聚类，其中聚类结果离正样本聚类结果最远的几个标记为负样本

### kNN
使用数据与最近k个标注数据的距离排序，排序结果中最远的那些被认为是可信负样本

### C-CRNE
全称叫Clustering-based method for Collecting Reliable Negative Examples，思路是先做全部数据的聚类，然后在聚类结果中找不包含正样本的cluster作为可信负样本

### GPU
Generative Positive-Unlabeled，从标注数据中学一个生成模型。负样本是那些生成概率最低的无标签样本

----

## **Step2: 监督/半监督学习**
如果Step1做的够好，这一步完全可以直接做监督学习就好了。有些半监督的方案会在这一步通过迭代训练模型，并在每代使用上一轮训练的分类器重新做可信负样本采集。

### Iterative SVM
使用正样本和可信负样本训练SVM分类器，然后使用分类器从无标签数据中预测出负样本，补充到前一轮的可信负样本中，为下一轮训练构造可信负样本。

### DILCA-kNN
使用正样本和可信负样本训练一个距离度量。对于每个无标签样本，使用kNN进行标注，根据距离正样本和可信负样本的平均距离判断样本类型。

从综述的方法上看，这些方法和思路可以在深度学习的一些方案中找到表达能力更强的模型。

----

## **Step3: 模型选择**
一般基于半监督迭代的模型会涉及到模型选择的问题。
最常见的方式是默认保留最终模型，使用所有轮模型进行ensembling也是一种有效方法。
以上两种方法实际上规避了模型选择的问题。

综述中提供了以下有几个指标，帮助我们进行模型的选择。
- $\Delta E = P(y'_i \neq y ) - P(y'_{i-1} \neq y)$：一旦标签数据的错误率上升，也就是$\Delta E \gt 0$, 停止训练，选择上一轮的模型。 
- $\Delta F = \frac{F_i}{F_{i-1}}$： 选择$F_1$提升的最后一轮，也就是$\Delta F \gt 1$. 之前有提到，在PU数据集中很难直接计算$F_1$，可以使用其代理$\frac{(P(y'=1|s=1))^2}{P(y'=1)}$计算。
- $FNR \gt 0.05$：停止迭代直到某一轮模型对标签样本的识别错误率高于5%

----

## **思考**

两步法就可分和光滑假设而言，在NLP和CV领域通过深度学习获得特征空间一般也是满足要求。此外，两步法框架很容易根据应用场景做组合，灵活性很强，也很容易引入深度学习来处理问题。这点也是两步法在PU learning中获得巨大成功的原因。

----

## **References**
- **Learning From Positive and Unlabeled Data: A Survey** Jessa Bekker · Jesse Davis

Photo by <a href="https://unsplash.com/@viniciusamano?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Vinicius "amnx" Amano</a> on <a href="https://unsplash.com/s/photos/shadow-dark?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

