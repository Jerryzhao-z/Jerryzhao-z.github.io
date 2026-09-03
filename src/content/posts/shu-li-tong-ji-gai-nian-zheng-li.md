---
title: '数理统计讲义笔记：绪论'
description: '数理统计中总体、样本、统计量与抽样分布的课堂笔记。'
publishedAt: '2021-05-24 11:25:50'
tags: [概率统计]
draft: false
---

知识体系对于后续的学习研究还是很重要的。INSA的通信工程的课程不包含数理统计，也让我后续在理解一些机器学习工作时感受到不小的困难。这篇博客是在阅读何志坚老师的开源讲义时整理的，感谢他的无私分享。

数理统计的目的是采集带有误差的数据，在设定的统计模型下，对数据分析并对关心的问题进行推断。总而言之：由局部信息推断整体性质

----

## basic definitions

- 数据：带随机性的数据
    - 真实数据：采集自真实世界
    - 模拟数据：计算机模拟生成，即蒙特卡罗抽样获取
- 模型：产生随机数据的机制
- 统计推断：从数据到模型的推理
    - 参数估计：描述模型的函数形式已知，根据数据求解具体参数
    - 假设检验：对具体参数值不关心，只关心命题真假，比如“抛硬币获得正面的概率是0.5? (抛硬币是否公平?)”

### 学派之争
统计推断的学派之争本质是定义概率的视角不同，一个尝试寻找客观存在，一个辅助主观判断。
- 频率学派：总体概率分布是固定且未知的，数据可能是带噪的。我们可以通过采样推断寻找这个概率分布，推断最佳参数
- 贝叶斯学派：总体概率分布是无法确定的，但数据是真实确信的。我们只能从数据中推断参数的概率分布。

### 总体&样本
我们在讨论分布时谈到的数据对象可以分为总体和样本两种。
- 总体：研究对象的全体，其中每个成员是个体。常用随机变量$X$表示整体，随机变量$x$表示某个个体。推断统计的目标就是推断总体$X$的分布$F(x)$
- 样本：从总体中抽取($X_1$,$X_2$,...$X_n$) n个个体观察， n是样本量
- 自由度：在计算时不受限制变化数值的数量。
    - 比如对于n个随机采样点，我们可以写为n维向量，也是表示n个可自由变化的数字，其自由度也就是n
    - 而在后文对n个采样点计算样本方差时，因为采样数据受限于函数$\sum_i^n (x_i - \bar{x}) = 0$，其自由度就变成了 n-1

- 简单随机抽样：
    - 满足随机性、独立性
    - 独立同分布 iid
    - 样本在抽取前是n维随机变量 ($X_1$,$X_2$,...$X_n$)
    - 样本在抽取后是具体的n个观测值 ($x_1$,$x_2$,...$x_n$) 

----

## 概率分布和抽样分布

### 概率分布族
根据一些信息，假设随机变量$X$的分布$F(x)$属于某个分布族$\mathcal{F}$
- 参数族：数学形式已知，但包含m个未知参数, 形式化表示为$\mathcal{F} := \{F_\theta, \theta \in \Theta \}$ 其中$\Theta \subset \Re^m$为参数空间
- 非参数族：分布$\mathcal{F}$无法用有限个参数描述
### 统计量与估计量
- 统计量：依赖于样本$(X_1, X_2...X_n)$的函数，形式化为$T=T(X_1,X_2,...X_n): \Re^n \rightarrow \Re$
- 估计量：用于估计参数的统计量
- 常用统计量：
    - 样本均值: $\bar{X} = \frac{1}{n} \sum_{i=1}^n X_i$
    - 样本方差: $S_n^2 = \frac{1}{n} \sum_{i=1}^n (X_i - \bar{X})^2$
    - 无偏样本方差: ${S'}_n^2 = \frac{1}{n-1} \sum_{i=1}^n (X_i - \bar{X})^2$
    - 样本标准差: $S_n = \sqrt{S_n^2}$
    - 样本k阶原点矩: $\bar{X^k} = \frac{1}{n} \sum_{i=1}^n X_i^k$
    - 样本k阶中心矩: $\frac{1}{n} \sum_{i=1}^n (X_i - \bar{X})^k$
    - 顺序统计量: $X(k)$ 递增排序第k位
- 充分统计量：一种特殊的统计量，可以简化数据同时不损失样本信息。某种意义上的降维
    - 不损失样本信息：在已知该充分统计量时，模型参数变化对采样分布不会产生影响，也就是条件概率$P(X_1,X_2,...X_n | T)$ 不是参数$\theta$的函数。注意：这里的不损失不意味着采样结果完全一致，而是样本分布一致。


### 抽样分布
统计量的概率分布，可细分为
- 精确抽样分布：总体$X$分布已知，直接可推导$T(X_1, X_2...X_n)$的分布
- 渐近抽样分布：寻求样本量n无限大时统计量$T(X_1, X_2...X_n)$的极限分布. 常用中心极限定理
- 近似抽样分布：寻找一种分布近似统计量$T(X_1, X_2...X_n)$的分布


#### 样本均值$\bar{X}$的抽样分布 
- 精确抽样分布：如果$X \sim N(\mu, \sigma^2)$，则$\bar{X} \sim N(\mu, \frac{\sigma^2}{n})$
- 渐近抽样分布：不确定总体是否遵循正态分布，但可以估算$E(X) = \mu$, $Var(X)=\sigma^2$, 则采样均值渐近分布为$\bar{X} \sim N(\mu, \frac{\sigma^2}{n})$
- 卡方分布：对于$X \stackrel{iid}{\sim} N(0,1), i=1,...n$，称随机变量$X = X_1^2+X_2^2...+X_n^2$ 的分布为自由度为n的卡方分布，标记为$\mathcal{X}^2(n)$
- 概率密度函数：$f_n(x)=\frac{1\{x>0\}}{2^{n/2}\Gamma(n/2)} x^{\frac{n}{2} - 1} e^{-\frac{x}{2}}$
- 与Gamma分布概率密度函数的关系：$Ga(\frac{n}{2}, \frac{1}{2}) = \mathcal{X^2}(n)$
- 可加性: 如果 $X \sim \mathcal{X}^2(n)$, $Y \sim \mathcal{X}^2(m)$, 且两随机变量独立，则 $X+Y \sim \mathcal{X}^2(n+m)$
- 期望和方差: $E(X) = n$, $Var(X) = 2n$
- 中心极限定理可得统计量依分布收敛为标准正态分布： $\frac{X - n}{\sqrt{2n}} \xrightarrow{d} N(0,1)$


#### 正态总体抽样分布定理
关于总体分布是正态分布的数据的抽样分布定理，即($X_1$,$X_2$,..$X_n$) 采样自总体分布 $X \sim N(\mu, \sigma^2)$
- 均值分布$\bar{X} \sim N(\mu, \frac{\sigma^2}{n})$
- $\frac{nS_n^2}{\sigma^2} = \frac{\sum_{i=1}^n (X_i - \bar{X})^2}{\sigma^2} \sim \mathcal{X}^2(n-1)$
- 样本均值$\bar{X}$与样本方差$S_n^2$相互独立
- “研究发现，只有正态总体才有“样本均值与方差独立”这一性质。”


#### t分布
设$X \sim N(0,1)$, $Y \sim \mathcal{X}^2(n)$且两随机变量独立，则定义随机变量 $T = \frac{X}{\sqrt{Y/n}}$ 为t分布，记为$T \sim t(n)$
- 概率密度函数： $f_n(x) = \frac{\Gamma(\frac{n+1}{2})}{\sqrt{n\pi}\Gamma(\frac{n}{2})}(1 + \frac{x^2}{n})^{-\frac{n+1}{2}}$
- 性质1: n-1时，t分布为柯西分布
- 性质2: $\lim_{n\rightarrow\infin} f(x) = \frac{e^{-\frac{x^2}{2}}}{\sqrt{2\pi}}$, 当自由度n增大，t分布逐渐接近标准正态分布。 $n \geq 25$时，t分布接近$N(0,1)$
- 样本均值与标准差之比的抽样分布： 设样本($X_1$,$X_2$,...$X_n$) 来自 总体$X \sim N(\mu, \sigma^2)$, 则$\frac{\bar{X} - \mu}{S_n / \sqrt{n - 1}} = \frac{\bar{X} - \mu}{{S'}_n / \sqrt{n}} \sim t(n-1)$
- 对比：$\frac{\bar{X} - \mu}{\sigma / \sqrt{n}} \sim N(0,1)$， t分布的密度函数是多项式衰减，而正态分布是指数阶衰减，远比t分布衰减快。所以t分布发生极端时间概率比正态分布更大。


#### F分布
设随机变量 $X \sim \mathcal{X}^2(m)$, $Y \sim \mathcal{X}^2(n)$且两随机变量互相独立, 定义随机变量 $Z = \frac{X/m}{Y/n}$的分布为第一自由度为m，第二自由度为n的F分布，记为$Z \sim F(m, n)$
- 概率密度函数：$f_{mn}(x) = 1\{x>0\} \frac{\Gamma(\frac{m+n}{2})}{\Gamma(m/2)\Gamma(n/2)} (\frac{m}{n})^{m/2}x^{\frac{m}{2} - 1}(1 + mx/n)^{-(m+n)/2}$
- 性质1: $Z \sim F(m,n)$ 则 $1/Z \sim F(n, m)$
- 性质2: $T \sim t(n)$ 则 $T^2 \sim F(1, n)$
- 两个独立正态分布的抽样分布: 设两独立总体 $X \sim N(\mu_1, \sigma_1)$, $Y \sim N(\mu_2, \sigma_2)$，从中采样获得($X_1$,$X_2$,...$X_n$), ($Y_1$,$Y_2$,...$Y_n$)，样本方差为$S_{1m}^2$, $S_{2n}^2$, 则定义 
    - $\frac{(X-Y) - (\mu_1 - \mu_2)}{\sqrt{\sigma_1^2/m + \sigma_2^2/n}} \sim N(0,1)$
    - $\frac{{S'}_{1m}^2 \sigma_2^2}{{S'}_{2n}^2 \sigma_1^2} \sim F(m-1, n-1)$
    - 如果 $\sigma_1 = \sigma_2 = \sigma$, $\frac{(\bar{X} - \bar{Y}) - (\mu_1 - \mu_2)}{\sqrt{(1/m + 1/n)(mS_{1m}^2 + nS_{2n}^2)}/(m+n-2)} \sim t(m+n-2)$

### 分位数
由累计分布函数可计算$\alpha$分位数$x_\alpha = F^{-1}(\alpha)$
常用分位数符号：
- 标准正态分布N(0,1)分位数 $u_\alpha$
- t分布分位数 $t_\alpha(n)$: n趋近无穷时, t分布极限分布为N(0,1), 所以在n较大时， $t_\alpha(n) \approx u_\alpha$
- 卡方分布分位数 $\mathcal{X}_\alpha^2(n)$: 因为统计量依分布收敛于标准正态分布 $\frac{X-n}{\sqrt{2n}} \xrightarrow{d} N(0,1)$, 因此自由度n较大时, $\mathcal{X}_\alpha^2(n) \approx \mu_\alpha \sqrt{2n} + n$
- F分布分位数 $F_\alpha(m,n)$

对称性：常用于简化计算
- $\mu_\alpha = - \mu_{1-\alpha}$
- $t_\alpha(n) = - t_{1 - \alpha}(n)$
- $F_\alpha(m,n) = \frac{1}{F_{1-\alpha}(n, m)}$


----

## References
- [数理统计讲义 - 何志坚](http://www.hezhijian.com.cn/stat/index.html)
- [数理统计|笔记整理（3）——充分统计量 - 学弱猹](https://zhuanlan.zhihu.com/p/87520809)

Photo by <a href="https://unsplash.com/@aloisk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Alois Komenda</a> on <a href="https://unsplash.com/s/photos/gamble?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

