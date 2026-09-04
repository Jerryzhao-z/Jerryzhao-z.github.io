---
title: 'PU learning: 初步'
description: '梳理 PU Learning 的数据设定、假设、评价与常见方法。'
publishedAt: '2021-05-08 11:22:13'
tags: [semi-supervised learning,综述学习,pu learning]
draft: false
---

之前在LIC2020比赛中发现了PU learning的威力，这里借学习综述之际，重新梳理这一块的思路和概念。从我目前的业务出发，从问答场景中的拒识问题进行思考。
原综述论文足足有近五十页，这里更多还是关注核心理论。后续补一篇博客介绍具体的方案。

----

## **定义 & 应用场景**
PU learning，即 learning from positive and unlabeled data，也被称之为Partially Supervised Classification，是一种针对**只有正例和无标签**数据的场景提出的机器学习研究方向。这与常见的机器学习数据设定有所不同。
PU Learning的最终目标就是从这样一个数据设定下训练一个二分类模型，可以根据特征区分正样本和负样本。
这种情况在实际业务场景中很常见，在医疗数据中标注数据以病变数据为主，在信息抽取中，基于远程监督构建的语料中也只有正样本标签。此外，异常检测领域对PU learning也有应用。像开头提到的对话拒识，可以视作异常检测问题来建模。

----

## **符号和概念**
本文中我们做如下符号定义 
- $(x, y, s)$ 表示PU数据集，其中x表示样本特征， y表示真实类型(y=1为正样本，y=0为负样本), s表示是否被标注。
    - 如果s=1，根据PU learning设定，y就是正样本, $P(y=1|s=1) = 1$
    - 如果s=0，y是未知标签
- $\alpha = P(y=1)$ 正样本的先验概率， 因此负样本的先验概率为$1-\alpha$
- $c=P(s=1|y=1)$ label frequency 正样本被选如标注集的概率
- $e(x)=P(s=1|y=1,x)$ propensity score function 倾向得分函数。这个和标注策略相关，建模标注过程中的一种选择倾向。
- PDF: probability density function 概率密度
    - $f(x) = P(x)$ 样本概率密度
    - $f_+(x) = P(x|y=1)$ 正样本概率密度
    - $f_-(x) = P(x|y=0)$ 负样本概率密度
    - $f_l(x) = P(x|s=1)$ 标注样本概率密度
    - $f_u(x) = P(x|s=0)$ 未标注样本概率密度

### 标注策略/机制
在PU learning中，标注策略/机制是很重要的一个描述信息，决定了那些样本会被选中进行标注。
上文符号列表中的$e(x)$倾向得分函数表示一个特征为x的正样本被选中标注的概率，而正样本被标注的比例为其期望 $c=E_x[e(x)] = P(s=1|y=1)$
由此借助PU learning假设与贝叶斯公式可推得 
$$
f_l(x) = P(x|s=1) 
 = P(x|s=1, y=1)   
 = \frac{P(s=1|x, y=1)P(x|y=1)}{P(s=1|y=1)}  
$$

$$
f_l(x) = \frac{e(x)f_+(x)}{c}
$$

### single-training-set VS case-control
这两个词在PU问题中描述标注集和未标注集的来源/分布差异。
single-training-set表示我们是先i.i.d从真实世界进行采样，然后从采样结果中选择一部分构建标注集。标注集和无标签数据集可以看作“精选日志标注”语料集和“日志”语料集。
这里可以对single-training-set数据集有三种看法
- 一个对真实分布的采样：$x \sim f(x)$
- 正样本与负样本的集合: $x \sim \alpha f_+(x) + (1 - \alpha) f_-(x)$
- 针对标注数据和无标签数据的采样集合: $x \sim \alpha c f_l(x) + (1 - \alpha c) f_u(x)$

case-control表示标注集和无标签数据来源不同，其中无标签数据是来自真实实际的i.i.d采样。标注集和无标签数据集可以近似看作“人工编写”的语料集和“日志”语料集。
- 在case-control数据集中，只能分析无标签数据：$x \mid s=0 \sim f_u(x)$
- 而无标签数据正是真实分布的采样: $x|s = 0 \sim f(x)$
- 数据集可以看作正负样本采样: $\alpha f_+(x) + (1 - \alpha) f_-(x)$

从这个角度看，标注策略关注的是标注数据集的$e(x)$，而single-training-set与case-control更关注数据集的构成。在single-training-set场景下，因为标注数据和无标签数据都是从真实世界采样，在PU数据集设定下，标注策略作为参数用于描述真实分布。而在case-control场景中，标注数据和无标签数据来源不同，标注数据不来自于真实分布，也就无法去描述真实分布了。

### 先验分布 $\alpha$ 和标签频率 $c$ 的关系
先验概率$\alpha = P(y=1)$和标签频率$c=P(s=1|y=1)$紧密相关，
考虑到$P(s=1) = \frac{P(s=1,y=1)}{P(y=1|s=1)} = P(s=1,y=1)$, 标签频率可以化简为
$$
c
= P(s=1|y=1) 
= \frac{P(s=1, y=1)}{P(y=1)} 
= \frac{P(s=1)}{P(y=1)}
$$

这里面$P(s=1)$可以通过计数的方式从PU数据集中进行统计。而$P(y=1)$需要分情况讨论：
在single-training-set中，

$$
P(y=1) = \alpha
$$

$$
c=\frac{P(s=1)}{\alpha}
$$

在case-control中，

$$
P(y=1) 
= \sum P(y=1, s) 
=  P(y=1|s=0)P(s=0) + P(y=1|s=1)P(s=1)
$$

$$
P(y=1) = \alpha P(s=0) + P(s=1)
$$

$$
\alpha = \frac{1-c}{c} \frac{P(s=1)}{1-P(s=1)}
$$

这个角度看，先验概率与标签概率是PU learning中可以转换的。需要时，求取其中一个即可。

----

## **假设**
PU learning设定了数据框架，但是这仍然是一个比较宽泛的定义。为了针对应用场景抽象简化问题，学界围绕标签策略、数据和先验分布做出一系列假设来降低其建模的难度。
### 关于标签策略
围绕“如何采集待标签的正样本”做假设。
#### SCAR: Selected Completely At Random
采用**完全随机**的方式在样本空间中采样，不受样本特征x影响。
在问答拒识标注中，正样本集相当于从日志中随机抽取然后逐个标注获得。
$$
P(s=1|x,y=1) = P(s=1|y=1)
$$

$$
e(x) = c
$$

$$
P(s=1|x)
= P(s=1, y=1|x) 
= e(x)P(y=1|x) 
= cP(y=1|x)
$$

![SCAR 假设下标注概率与真实正样本概率的关系示意图](/post-images/1620448043161.png)

#### SAR: Selected At Random
SAR是一个更通用的标签策略假设，针对样本特征x的选择策略导致标注集分布与真实标签分布存在采样偏差，是一种有偏采样。以propensity定义 $e(x) = P(s=1|x,y=1)$
$$
P(s=1|x)
= e(x)P(y=1|x)
$$

问答拒识标注中，问答日志有长有短，但是在标注数据前常根据句长等特征筛选去重等预处理。这样采集到的标注数据标签分布，和真实正样本标签分布就存在一定偏差bias。这也就引出了一个细分方向，学习一个unbiased classifier.
![SAR 假设下倾向得分与分类概率的关系示意图](/post-images/1620474783017.png)

#### PG: Probabilistic Gap
PG是一种SAR假设，在SAR假设基础上，PG假设真实正样本中的数据，如果与真实负样本数据越相似，被采样出来标注的概率越低。因此PG通过概率差定义一个标注难度 $\Delta P(x) = P(y=1|x) - P(y=0|x)=2P(y=1|x) - 1$，值越大说明越容易标注，越小说明约难以判断区分，更难标注。
从这个标注策略看，我们可以把倾向得分函数$e(x)$看作一个关于标注难度的单调递增函数，越大越容易获得标注机会。

分析这个标注难度$\Delta P(x)$计算式，其中的概率是一个判别模型输出，正是我们的一个输出目标，因为缺乏负样本，不方便训练。
更方便的做法是在可观察的标注数据上训练一个模型预测$P(s=1|x)$， 估算其变种 $\Delta \widetilde{P}(x)=P(s=1|x) - P(s=0|x)=2P(s=1|x) - 1$ 
通过贝叶斯变换可以整理为$\Delta \widetilde{P}(x) =e(x)(\Delta P(x) + 1) -1$

这种假设下可以证明出以下关于两者关系的表述：
- $\Delta \widetilde{P}(x) \leq \Delta P(x)$
- $\Delta \widetilde{P}(x)$ 与 $\Delta P(x)$ 存在排序关系上的一致性
    - $\Delta \widetilde{P}(x_1) = \Delta \widetilde{P}(x_2) \iff \Delta P(x_1)=\Delta P(x_2)$
    - $\Delta \widetilde{P}(x_1)<\Delta \widetilde{P}(x_2) \iff \Delta P(x_1)<\Delta P(x_2)$
![Probabilistic Gap 标注难度与概率差关系示意图](/post-images/1620474817388.png)

### 关于数据
#### Negativity: 无标签数据都是负样本
这个假设成立的场景比较少，但是如果成立，就使用一些流行的机器学习方法做有监督二分类
#### Separability: 可分性假设
可分性假设认为存在一个function $f(x) : \R ^n \rightarrow \R$，和阈值$\tau$, 使下式成立：
$$
f(x_i) \geq \tau, y_i = 1
$$

$$
f(x_i) < \tau, y_i = 0
$$
这个假设常常在two-step技术中使用。
#### Smoothness: 平滑假设
对于相似的两个样本$x_1$、$x_2$，其正样本概率$P(y=1|x_1)$、$P(y=1|x_2)$也相似
这个假设对于two-step也非常重要。

### 关于先验分布（😟这块关于正样本锚集和正样本函数的区分上，我还有些疑惑）
先验分布$\alpha = P(y=1)$对于SCAR假设场景很有用，但是在PU learning的数据场景中估计先验是不可能的。所以有必要针对先验分布做假设。以下的四种假设中，假设强度逐渐降低。
#### Separable classes: 正负样本分布无交集
正样本与负样本的分布没有overlap。
#### positive subdomain/anchor set: 正样本锚集
正负样本分布有交集，但是根据部分特征值条件可以划分一个只有正样本分布的区域。
#### positive function/separability: 正样本函数
正样本锚集的扩展，可以使用一个函数分割出一个只有正样本分布的区域
#### irreducibility: 不可精简假设
最基本的假设：负样本分布不是一个包含正样本分布的混合概率分布。 

## **如何在PU learning中评价模型**
在大部分PU learning场景中，因为数据上缺乏负样本，很难使用常用评价方法构建test set对分类结果进行评价。当然像LIC2020关系抽取任务中使用PU learning时，因为百度提供了高质量的测试集，我们可以直接使用标准F1 score来评价。
常用二分类评价指标 $F_1= \frac{2pr}{p+r}$，其中$p=P(y=1|y'=1)$, $r=P(y'=1|y=1)$。$y'$表示模型预测标签。
如果SCAR假设成立，我们可以直接在正样本集上评价模型召回率 $r=P(y'=1|s=1)$。有学者以此假设为基础借助排序方案取topN并计算其中标注正样本比例来估算Precision和$F_1$，这个具体做法还要翻翻论文细节。
此外, 求解PU learning的数据限制下直接计算$F_1$很难，学界提出了以下PU learning可计算的评价函数作为$F_1$的代理，其属性与$F_1$相同。
$$
\frac{pr}{P(y=1)}
= \frac{pr^2}{rP(y=1)} 
= \frac{P(y=1|y'=1)r^2}{P(y'=1, y=1)} 
= \frac{r^2}{P(y'=1)}
$$

感慨一下自己的数理统计基础不好，😢论文中的假设验证G-test不太懂，等我后续研究下。(update：可以参考后续补充的博文**PU learning: 假设检验**)

----

## **如何估计先验**
在SCAR假设下，估计先验可以大大简化PU learning问题，在很多方法中发挥重要作用。
但在PU数据集中估计先验一个比较难的任务，很多时候我们会把它做一个超参数，通过调参和最终的模型效果进行选择。
综述在先验估计部分也介绍了不少方法，这里挑几个思路简洁的介绍下。

### 基于分类器 $P(s=1|x)$
正负样本分布无交集时，可以从以下两种思路入手：
思路1：
从标注数据中切出一部分为validation set，然后用训练分类器$P(s=1|x)$，然后在validation set上计算$P(s=1|y=1) = c$

思路2：   
在SCAR假设下，前文提到可以推出等式 $P(s=1|x) = c P(y=1|x)$.
样本锚集假设下，存在一个只有正样本分布的区域$P(y=1|x) = 1$，这样我们可以分类器$P(s=1|x)$在标注集上进行推断，并进行估算$c=\max g(x)$

这种严格的分布无交集假设让这两种思路在实际场景中可应用性并不太强。

### ROC方法
ROC曲线在二分类问题中，以假阳率为x轴，真阳率为y轴根据不同的阈值在坐标上画点，连为曲线。
其中：
$$
TPR
= \frac{TP}{TP + FN} 
= \frac{P(y'=1, y=1)}{P(y'=1, y=1) + P(y'=0, y=1)} 
= P(y'=1|y=1)
$$

$$
FPR
= \frac{FP}{FP + TN}
= P(y'=1|y=0)
$$
优化的目标是最大化$TPR$和最小化$FPR$, 其中第一个$TPR$可以在标注数据上计算$P(y'=1|s=1)$，但$FPR$无法计算。
从预测概率$P(y'=1) = \alpha P(y'=1|y=1) + (1 - \alpha) P(y'=1|y=0)$可以推导出如下等式:
$$
\frac{P(y'=1)}{P(y'=1|s=1)} = \alpha + (1 - \alpha) \frac{P(y'=1|y=0)}{P(y'=1|y=1)}
$$
对于这样一个等式，从优化目标角度看，我们希望右边的第二部分趋近于0，使左边逐渐趋近于$\alpha$
这样我们的优化目标可以重新写为 $max_f \frac{P(f=1)}{P(f=1|s=1)}$
这个优化结果在不可精简假设这个比较松的假设下就可以成立，但是很难收敛。实践中，一般会在正样本锚集假设下设计这种类型的估算算法。

----

## **PU learning方法**
介绍下方法思路😏，其实综述里介绍的不少方法都有些过时了，但思想还值得借鉴。
### Two-step方法
Two-step方法主要是基于可分性假设和平滑假设，即所有正样本和标注样本都比较像，而负样本区别较大。
其方法框架如下：
- 识别可信负样本
- 使用半监督/监督学习技术学习正样本和可信负样本
- (optional)选择训练生成的最佳分类器 
![PU Learning Two-step 方法流程图](/post-images/1620634666975.png)

### biased learning
这种方法把未标注数据看作带噪负样本进行优化。比较常见的方案是biased-SVM。
 如果不考虑正样本带噪问题，可以进行如下soft-SVM建模：

$$
Minimize: \frac{1}{2} w^T w + C \sum_{i=k}^{n} \zeta_i
$$

$$
\text{subject to} :w^T x_i + b \geq 1, i = 1,2,.., k-1
$$

$$
-1(w^T x_i + b) \geq 1 - \zeta_i, i = k, k+1, ..., n
$$

$$
\zeta_i \geq 0, i=k, k+1, ... n
$$

如果考虑正样本中可能也带噪音的情况，可以给positive errors与negative errors分别添加权重系数$C_+$和$C_-$:

$$
Minimize: \frac{1}{2} w^T w + C_+ \sum_{i=1}^{k-1} \zeta_i + C_- \sum_{i=k}^{n} \zeta_i
$$

$$
\text{subject to} : y_i (w^T x_i + b) \geq 1 - \zeta_i, i = 1,2,.., n
$$

$$
\zeta_i \geq 0, i=1,2, ... n
$$

一般会使用一个更大的$C_+$和一个更小的$C_-$，参数选择上一般是通过validation set来最终确定。

此外常用的PU bagging在此类之列，PU bagging滥觞于论文*A bagging SVM to learn from positive and unlabeled examples*。后续有很多其变种，在深度学习中也可以方便实现。
- 从标注集和无标签数据中通过bootstrap构建出多个包含正负样本的训练集
- 使用上一步获得的训练集训练分类器对不属于对应训练集的无标签数据做打分$P(y=1|x)$， 这样每个无标签样本都可以获得多个分类器打分
- 通过vote或者averaging的方法把分数做融合，确定最终样本标签

biased learning很多时候并不需要很强的假设做支持，可以在Two-step方案中**识别可信负样本**中发挥作用。

### 结合先验
基于SCAR假设，我们可以结合先验分布。上文中提到标签比例和先验分布是密切相关的，我们在使用这类方式时，一般需要先估计先验或者标签比例。
此外基于SAR假设时，使用最小化经验风险风险需要额外估计$e(x)$来计算权重.

这类方法从模型训练过程角度可以分为：post-processing、preprocessing和method modification三个子类

#### post-processing
post-processing 受$P(s=1|x) = cP(y=1|x)$启发，认为分类概率$P(y=1|x)=\frac{P(s=1|x)}{c}$，可以由样本标注概率$P(s=1|x)$和label frequency进行估算。
这里就先用PU数据训练模型预测被标注概率$P(s=1|x)$，然后调整常用的阈值0.5进行判断$P(s=1|x) > 0.5c$

#### pre-processing
这种方案的目标是构建一个可用于监督训练的语料。这里面还可以细分出三种做法：

##### rebalancing methods
post-processing的预处理视角。从post-processing做法中发现：如果阈值合适，训练一个标注/无标签分类器可以给出正负样本分类器一样的结果。Rebalancing方法希望能使用同一种阈值，通过调整样本权重来进行实现与post-processing相同的效果。

#####  无标签数据打软标签
08年一篇论文提出的方法
还是先用PU数据训练一个模型拟合$P(s=1|x)$。然后使用如下公式打标, 最后训练模型预测$P(y=1|x)$
$$
P(y=1|s=0, x) = \frac{1-c}{c} \frac{P(s=1|x)}{1-P(s=1|x)}
$$

##### Empirical-Risk-minimization based: 基于最小化经验风险
这个方法打算在最小化经验风险的框架内训练一个分类器解决PU问题。
首先常用的二分类模型中，模型$g$的最小化风险写为
$$
R(g)=\alpha \mathbb{E}_{f_+}[L^+(g(x))] + (1 - \alpha) \mathbb{E}_{f_-}[L^-(g(x))]
$$

借助$\mathbb{E}_f(L) = \alpha \mathbb{E}_{f_+}(L) + (1 - \alpha) \mathbb{E}_{f_-}(L)$

$$
R(g) = \alpha \mathbb{E}_{f_+}[L^+(g(x))] + \mathbb{E}_f[L^-(g(x))] - \alpha \mathbb{E}_{f_+}[L^-(g(x))]
$$

上文标签机制中提到PU leaning场景下$f_l(x) = \frac{e(x)f_+(x)}{c}$，由此推得

$$
R(g) = \alpha \mathbb{E}_{f_l}[\frac{c}{e(x)}(L^+(g(x)) - L^-(g(x)))] + \mathbb{E}_f[L^-(g(x))]
$$
借助这个函数，我们可以在case-control与single-training-set两个场景下构造新的带权重数据集。

在case-control类型的数据集中，我们可以自然得套用上式构建数据集：
- 负样本：
    - 无标签数据，权重为$\frac{1}{|s=0|}$, 
    - 标注数据，权重为$-\frac{\alpha c}{|s=1| e(x)}$。
- 正样本:
    - 标注数据，权重为$\frac{\alpha c}{|s=1| e(x)}$

在single-trainig-set数据集中，引入上文提到可以将对真实数据得采样展开为标注数据采样和无标签数据采样 $x \sim \alpha c f_l(x) + (1 - \alpha c) f_u(x)$, 加入上之前得Risk函数中可得：
$$
R(g|x,s) = \frac{1}{|s|} \mathbb{E}_{f_l}(\frac{1}{e(x)}L^+(g(x)) + (1 - \frac{1}{e(x)}) L^-(g(x))) + \mathbb{E}_{f_u} L^-(g(x)))
$$

这样把最外面得常数去掉，构造出一个带权重数据集：
- 负样本：
    - 无标注数据，权重为1
    - 标注数据，权重为 $1-\frac{1}{e(x)}$
- 正样本:
    - 标注数据，权重为 $\frac{1}{e(x)}$

这种方案在SAR和SCAR假设下都有学者提出，属于适应范围比较广的一种算法。

#### method modification
这块方法都比较老了，有兴趣可以翻翻综述中提到的几个机器学习方法。

### 关系型方法
这类方法中，把标注数据看作一个部分完成标注的知识库，待补充的知识是无标签数据。
大部分该类方法使用closed-world假设，也就是Negativity. 这样可以直接用常见二分类器处理。
对于缺失严重的知识库时，有些方法会使用open-world假设。

### 其他方法
- GAN: 拟合正样本和负样本分布
- Co-training
- Data stream classification
- EM

### 如何选择PU learning 算法：主要是判断哪个假设更可能成立
- 如果可分性假设成立，优先考虑two-step方法
- 如果SCAR成立，考虑biased learning和结合先验的方法
- 可分性和SCAR都成立时
    - 如果可分性成立，但是区分难度高：从SCAR假设角度考虑
    - 否则，选择two-step方法
    - 一般情况可分性好的分布使用two-step方法比基于SCAR假设的方法更鲁棒
- 对SCAR/SAR假设下的$P(y=1|x)$无偏估计感兴趣，可以考虑emprical-risk estimation method

----

## **Further more**
UIC大学的Bing Liu教授是这个领域的开山鼻祖，东京大学的Masashi Sugiyama教授也在做这个方向的工作，值得持续关注。

----

## **References**
- **Learning From Positive and Unlabeled Data: A Survey** Jessa Bekker · Jesse Davis

Photo by <a href="https://unsplash.com/@zuizuii?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Duy Hoang</a> on <a href="https://unsplash.com/s/photos/shadow-dark?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
