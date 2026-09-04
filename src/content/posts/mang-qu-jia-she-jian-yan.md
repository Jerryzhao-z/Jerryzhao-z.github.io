---
title: 'PU learning: 假设检验'
description: '从统计假设检验、效力分析到 PU Learning 的整理。'
publishedAt: '2021-05-17 14:03:52'
tags: [pu learning,semi-supervised learning,hypothesis testing]
draft: false
---

数理统计一直是我的一个知识盲区，上次在读PU learning文章时就被G-test搞得头昏脑胀。
其实日常读机器学习和深度学习相关论文时，做假设检验的工作并不多，大部分论文的实验部分都是由对比实验和消融实验组成。大亨最近在周会上分享了一些假设检验的概念，引发了我的兴趣，后续查阅资料时读到了Zhihong Deng关于假设检验的文章，很受启发。希望能结合PU learning和一些学界的进展继续延申一下。
此外在博文《数理统计讲义笔记：假设检验》中，根据何志坚教授的开源讲义做了一些假设检验的笔记供大家参考。

----

## **假设检验：What & Why & How**

在统计推断中，假设检验(statistical hypothesis testing)是一种假设命题的测度，结果是某种条件下接受/拒接 参数假设。
一般认为假设检验是一种带有概率性质的反证法，即为了证明命题$H_1$提出一个与之互补的命题$H_0$, 并测算$H_0$成立时产生的后果（统计量），如果产生不合理的现象，即在$H_0$下发生了极小概率事件，则拒绝$H_0$假设，接受$H_1$假设。反之，则接受假设$H_0$，拒绝$H_1$.

### Notions
在假设检验中有如下的常用符号：
- $H_0$: null hypothesis, 原假设，待证明假设的不相容假设
- $H_1$: alternative hypothesis, 备择假设，待证明假设
- $\alpha = P(H_0 \text{ rejected} | H_0=\text{True})$：significance level, 显著水平.
- $\gamma = 1 - \alpha = P(H_0 \text{ accepted} | H_0=\text{True})$: confidence level, 置信水平，需要于置信区间做区分。
- $T$：统计量，是我们做检验的一个目标
- $R$：拒绝域，如果被检测的T落在拒绝域中则$H_0$被拒绝，反之则接受
- $C$：临界值，区分拒绝域于接受域的临界值
- $p$: p值，一个条件概率，表示原假设成立下获得比样本观察结果更极端的结果出现概率(双侧p值与单侧p值)。如果p值小于$\alpha$，则意味着观测值进入拒绝域。此外p值越小，则以越强的判定结果拒绝$H_0$，接受$H_1$。

### 如何分类：假设与认知角度
从认知角度，统计推断又分为频率学派和贝叶斯学派两种观点。
- 频率学派认为随机变量背后存在着真实固定的分布，参数为$\theta$，样本是真实分布中采样获得(独立同分布)。我们的目标就是根据样本求取likelihood $L(\theta | x) = P(x|\theta)$，找到最可能复现采样结果的参数，完成对随机变量的建模。
- 贝叶斯学派认为推断应该看作一个认知过程，参数是随着认知(采样)增加不断变化的，由观察到的采样数据来指导参数更新。一般我们会基于经验或者假设条件，针对参数提出先验分布$P(\theta)$，进行后验推断计算参数概率$P(\theta|x) = \frac{P(x|\theta)P(\theta)}{P(x)}$

我们常说的假设检验一般指频率学派定义的假设检验，而贝叶斯学派中定义的假设检验被百度百科称之为贝叶斯检验，再很多教材里被称之为贝叶斯假设检验。
**下文中提到假设检验默认指频率学派定义下的检验假设。**

此外，根据假设强弱，统计推断模型可以分为参数模型和非参模型。
- 参数模型假设真实分布形态属于某个已知分布族，只需要调整其分布的几个未知参数就可以拟合采样数据。
- 非参模型的分布形式未知，也就无法确定具体需要多少参数描述分布。

这样根据假设强弱，假设检验可以进一步分为参数假设检验和非参假设检验。

### 常见假设类型
根据目的不同，可以针对不同统计量提出检验。
比较常见的参数假设检验，有用于在大样本下进行均值对比的Z-test和小样本下进行均值对比的T-test，这两种检验都假设真实样本服从高斯分布。对高斯分布假设下，我们能还可以用F-test进行方差对比。

而卡方假设，就是一种衡量两个随机变量频率关系的非参数假设检验。

### 有什么作用：分析视角和模型视角
流程管理理论6sigma，蕴含着假设检验在质检评估中的应用：我们可以根据业务需求设定一个故障率和方差，然后通过采样进行T-test和F-test来进行检验，如果关于目标故障率和方差的假设被接受则该批次产品合格，从降低产品的缺陷率。

如微调老师在知乎问题[为什么做机器学习的很少使用假设检验？](https://www.zhihu.com/question/55420602)所写，假设检验在作为变量分析工具，在社科研究中常用于评估回归模型中某个变量的显著性。
对于形如$y=w_0 + w_1 x$的回归函数，我们可以使用T-test，并构建原假设为“$w_0$ = 0”，这样$x, y$就不存在线性关系了。如果假设检查结果为拒绝，我们可以说$x$对$y$有意义。此外相应的p值也可以用于比较对应特征的重要性，进而引述为可解释性。
此外，假设检验也可以帮助我们比较机器学习算法，可以参考微调老师在问题[常用的机器学习算法比较？](https://www.zhihu.com/question/27306416/answer/372241948)中的回答。使用Wilcoxon signed rank sum test可以判断两个模型在多个数据集上的效果是否一致。如果显著不一致，则可以下判断确定其中一个模型效果更好或者更差。

### 如何做一个假设检验
根据统计量的选择不同，每个假设的验证方法上都有细节的差异。但是基本遵循相似框架：
- 确定实验数据和感兴趣统计性质
- 定义$H_0$和$H_1$，根据$H_0$判断单/双尾检验
    - 双尾: $H_0$是关于相等的假设
    - 单尾: $H_0$是关于大小关系的假设
- 根据数据量和统计性质选择检测方法和统计量
- 根据现实需要或者需求设定显著水平$\alpha$
- 使用实验数据计算统计量的观测值
- 如果p值小于$\alpha$或者观测值落在拒绝域，则拒绝原假设，接受备择假设。

----

## Statistical Power
在上文我们提到，在做命题显著性假设时我们关注的是p值, 即$P( \text{比观察值更强得拒绝} H_0 |H_0 = \text{True})$。
p值关注的这种假阳错误被称为 **第一类错误** type-1 error，显著水平$\alpha=P(\text{refuse } H_0|H_0=\text{True})$表示能容忍的最大type-1 error比例。
**第二类错误**是假阴错误，即错误地接受了$H_0$假设的概率。一般在做假设检测室设置参数$\beta=P(\text{accept } H_0|H_0=\text{False})$表示可容忍第二类错误的容忍程度。所谓效力，“Power of a test”表示正确地拒绝了原假设的概率，也就是$1-\beta$

统计效力，可以理解为对于某个真实命题，可以感知到该命题的概率。某种程度上说如果正确拒绝原假设-就是统计功效-不足，那p值也不能很确定的帮助我们就否定掉原假设。也许因为可能恰好采样太少，计算获得的信息不能反映整体分布情况，结果可靠性差，也就没有说服力。反之则假设检验有很强的说服力。

这也就引出了效力分析（power analysis）。
效力分析涉及到以下几个变量：
- $\alpha$：显著水平，一般都设定在0.05或更小
- ES: effect size, 效应量，衡量两个总体分布的重叠程度，效应量越大则重叠越小，在显著性假设验证中衡量于原假设的差异。根据统计方法不同，计算方法和结果范围都有差异。比较常用的是关于均值假设检验的Cohen's d。
- N: 样本数量
- $1-\beta$：power, 效力，一般被设定在0.8或以上。

这四个变量相互关联，一旦其中三个确定，就可以用于推算第四个。这样三缺一可以组合出一系列估计。
我们可以根据设定的显著水平，效力和效应量去估算合理的采样数。也可以用于估算效应量和评估假设检验的效力水平。根据使用场景和待求变量，效力分析可以加前缀名如下进一步分类：
- A priori 先验（用于设计假设检验）: compute N, given $\alpha$, $1-\beta$, ES  
- Post‐hoc 后验（确定假设检验结果）: compute $1-\beta$, given $\alpha$, N, ES  
- Criterion（极少使用）: compute $\alpha$, given $1-\beta$, ES, N 
- Sensitivity（敏感度）: compute ES, given $\alpha$,  $1-\beta$, N 

----

## G-test
G-test是一种广义似然比检测。其统计量似然比定义为$\lambda = \frac{L(x_{1:n};\widehat{\theta})}{L(x_{1:n}; \widehat{\theta}_0)}$ .
在交叉表分析中，G-test也被写为$G=2\sum_{x \in \mathcal{X}} \sum_{y \in \mathcal{Y}} O_{x,y} \ln \frac{O_{x,y}}{E_{x,y}}$, G-test原假设为两随机变量独立，由此可获得其期望$E_{x,y}=\widehat{P}(x) \widehat{P}(y) N = \frac{O_x}{N} \frac{O_y}{N} N = \frac{O_x O_y}{N}$，由此可推得
$$G=2\sum_{x \in \mathcal{X}, y \in \mathcal{Y}} O_{x,y} \ln \frac{O_{x,y}}{E_{x,y}} = 2\sum_{x \in \mathcal{X}, y \in \mathcal{Y}} N P(x,y) \ln \frac{N  P(x,y)}{N P(x)P(y)}$$
$$G = 2N \sum_{x \in \mathcal{X}, y \in \mathcal{Y}} \widehat{P}(x,y) \ln \frac{\widehat{P}(x,y)}{\widehat{P}(x) \widehat{P}(y)} = 2N \widehat{I}(x,y)$$
这里的互信息熵就是最大似然估计得到参数的互信息。
G-test近似于卡方检验（自由度=$(|\mathcal{X}| - 1)(|\mathcal{Y}| - 1)$），对于给定的采样，我们可以先计算G，然后再根据卡方分布的CDF表查表判断是否在拒绝域中。如果在拒绝域中，则两变量不独立，否则接受独立假设。

----

## PU learning中的假设检验
### 独立性假设
在PU场景和SCAR假设下，$X \perp \!\!\! \perp Y \iff X \perp \!\!\! \perp S$, 正样本标签与样本特征的分布独立性等价于真实标签和样本特征分布的独立性

### 检测效力
在PU场景和SCAR假设下，当真实标签分布和样本特征分布不独立时，$I(X;Y) \gt I(X;S)$

### 结合先验概率 $P(y^+)$ 进行效力分析
G-test统计量之间存在如下关系:
$$\lambda_{G(X;S)} = \kappa \lambda_{G(X;Y)} = \kappa 2 N I(X,Y)$$
其中$\kappa = \frac{1 - P(y^+)}{P(y^+)} \frac{P(s^+)}{1 - P(s^+)} =  \frac{1 - P(y^+)}{P(y^+)} \frac{N_{s^+}}{N - N_{s^+}}$
这样我们就可以用$G(X;S)$代替$G(X;Y)$进行效力分析

### 应用
PU场景中最常见的应用是在先验功效分析中判断采样集大小和达到设定指标的必要标注数据数量。
在先验概率$P(y^+)$已知时，可以通过自由度、显著水平、效力、先验概率、标注概率画出$N=f(I(X;Y))$图，然后根据需要的特征与标签的相关性$I(X;Y)$判断出所需的最少样本数量$N$的取值。
在先验概率未知时，可以假设先验符合广义二项分布进行参数估计，通过蒙特卡洛方法探索不同样本量的不确定性。


----

## **References**
- [薛定谔的假设检验——机器学习论文里到底要不要做检验？Zhihong Deng](https://zhuanlan.zhihu.com/p/108039322)
- Statistical Comparisons of Classifiers over Multiple Data Sets - Janez Demsar 
- Statistical Hypothesis Testing in Positive Unlabelled Data - Konstantinos Sechidis
- [A Gentle Introduction to Statistical Power and Power Analysis in Python](https://machinelearningmastery.com/statistical-power-and-power-analysis-in-python/)
- [Wiki: G-test](https://en.wikipedia.org/wiki/G-test)
- [A Researcher’s Guide to Power Analysis](https://research.usu.edu//irb/wp-content/uploads/sites/12/2015/08/A_Researchers_Guide_to_Power_Analysis_USU.pdf)
- [G.Power教程 | 样本量估计](https://zhuanlan.zhihu.com/p/62560195)


Photo by <a href="https://unsplash.com/@chrisliverani?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Chris Liverani</a> on <a href="https://unsplash.com/s/photos/statistics?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
