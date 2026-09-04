---
title: '数理统计讲义笔记：假设检验'
description: '数理统计讲义中参数假设检验部分的学习笔记。'
publishedAt: '2021-05-28 10:46:35'
tags: [概率统计,hypothesis testing]
draft: false
---

假设检验使统计推断的两大类方法之一。当关心的问题不需要给出具体的数字或者区间，而是做判断，这类问题被称之为假设检验问题 Hypothesis Tests。这里讲义中仅讨论参数假设检验。

----

## 概念

### 形式化
- $H_0$ 原假设
- $H_1$ 备择假设，检测的目的是判断原假设与备择假设中哪一个是成立的。
- 参数假设检验基本形式： 
    - 设总体来自与某参数分布族$\{F(x, \theta), \theta \in \Theta \}$, 其中$\Theta$为参数空间，包含所有可能参数。
    - 假设检验定义为 $H_0: \theta \in \Theta_0 \text{ vs. }  H_1: \theta \in \Theta_1$, 其中 $\Theta_0 \neq \empty, \Theta_1 \subset \Theta, \Theta_0 \cap \Theta_1 = \empty$, 最常见的$\Theta_1=\Theta - \Theta_0$

### 两种错误
- 基于样本数据做出接受/拒绝原假设的判断。由此把样本空间划分为互为补集的两部分：
    - 拒绝域：如果样本数据判断出的参数落在拒绝域则拒绝原假设
    - 接受域：反之，接受原假设
- 两种错误：
    - 第一类错误：拒真, 概率记为 $\alpha = P(X \in 拒绝域 | H_0)$
    - 第二类错误：纳伪, 概率记为 $\beta = P(X \notin 拒绝域 | H_1)$
- 假设检验的核心问题：如果控制犯两类错误的概率
- 参数假设检验中可以使用功效函数$pw(\theta)$定义错误概率为：
    - $\alpha = pw(\theta), \theta \in \Theta_0$
    - $\beta = 1 - pw(\theta), \theta \in \Theta_1$
    - 大多数情况下两种错误概率是背道而驰的，样本量不变的情况下，"按下葫芦浮起瓢"：拒绝域选取上，在保证第一类错误概率不超过一定水平(显著性水平)下，选择第二类错误尽可能小的拒绝域



### UMP
uniformly most powerful 一致最大功效，一个关于拒绝域的描述性定语
- 定义$W$为检验水平$\alpha$的UMP拒绝域，则一切水平小于$\alpha$的拒绝域$W'$，其功效均小于等于UMP拒绝域: $pw(\theta) \geq pw'(\theta), \text{for any } \theta \in \Theta_1$
- 定义$W$为检验水平$\alpha$的无偏(unbiased)拒绝域：$pw(\theta) \geq \alpha, \text{for any } \theta \in \Theta_1$
- 定义$W$为检验水平$\alpha$的一致最大功效无偏(UMPU)拒绝域，如果$W$同时使$alpha$的无偏拒绝域和UMP拒绝域。


### 似然比检验
- 似然比：设$L(x_{1:n}; \theta)$为似然函数，$\theta_1, \theta_2 \in \Theta$. 两参数的似然函数比为似然比：
$$LR = \frac{L(x_{1:n}; \theta_2)}{L(x_{1:n}; \theta_1)}$$
- Neyman-Pearson定理：对于**简单假设检验**，似然比检验得到的拒绝域是UMP
- 似然比检验得到的拒绝域是无偏的
- 广义似然比检验：似然比假设推广至**复合假设检验**
    - 设$H_0: \theta \in \Theta_0$, $H_1: \theta \ \notin \Theta_0$, 定义广义似然比为$\lambda(x_{1:n}) = \frac{\sup_{\theta \in \Theta}L(x_{1:n}; \theta)}{\sup_{\theta \in \Theta_0}L(x_{1:n}; \theta)} = \frac{L(x_{1:n};\widehat{\theta})}{L(x_{1:n}; \widehat{\theta}_0)}$, 
        - 其中分子上的参数$\widehat{\theta}$表示在整个参数空间上最大似然估计求得的参数， 
        - 而分母上的参数$\widehat{\theta}_0$表示在原假设参数空间上最大似然估计求得的参数
    - 广义似然比的拒绝域定义为：$W = \{ x_{1:n}: \lambda(x_{1:n}) > \lambda_0 \}， \lambda_0 \geq 1$
    - 思想：如果原假设成立，则似然函数在原假设参数范围内的最大值应与全局最大值接近，如果相差很大，则有理由拒绝原假设

### 单参数指数型分布族
为了方便得获得UMP拒绝域得一般形式，尝试用单参数指数型分布族进行建模。
对于$x \in \mathcal{X}$, 称$X$服从单参数指数型分布，如果其概率密度函数可以写为：
$$f(x;\theta) = S(\theta)h(x)\exp\{Q(\theta)V(x)\}$$
其中
- 变量$\theta$定义在变量空间$\Theta=(a,b), -\infin \leq a \lt b \leq \infin$
- $S(\theta) \gt 0$
- $x \in \mathcal{X}, h(x) \gt 0$
- 函数$Q(\theta)$是变量的严格单调递增函数

很多常见的分布都属于这个范畴：
- 指数分布： $f(x;\lambda) = \lambda e^{- \lambda x}, S(\lambda) = \lambda, h(x) = 1, Q(\lambda), V(x) = -x$
- 正态分布（$\sigma$已知）: $f(x; \mu) = \frac{1}{\sqrt{2\pi} \sigma} e^{-\frac{(x - \mu)^2}{2 \sigma^2}} = \frac{1}{\sqrt{2\pi} \sigma} e^{-\frac{x^2 - 2\mu x + \mu^2}{2 \sigma^2}} = \frac{1}{\sqrt{2\pi} \sigma} e^{- \frac{x^2}{2\sigma^2}} e^{\frac{\mu}{\sigma^2} (x - \mu/2)}$ 
- 正态分布（$\mu$已知）: $Q(\sigma) = - \frac{1}{2\sigma^2}, V(x)=(x - \mu)^2$

我们可以在这种分布下给出常见的假设检验UMP/UMPU, 步骤如下：
- 根据分布写出检验统计量: $T(x_{1:n}) = \sum_{i=1}^n V(X_i)$, 
- 根据假设检验写出拒绝域$W$形式，比如 $T(x_{1:n}) \gt C, T(x_{1:n}) \lt C, T(x_{1:n}) \in (C_1, C_2)$
- 根据拒绝域的待定参数C，根据设定的检验水平$\alpha$获得C的取值: $P_{\theta_0}(T(X_{1:n}) \in W) = \alpha$

正态总体：
- 关于期望的检验统计量为$n\bar{X}$或者$\bar{X}$, 称为U检验
- 关于方差的检验统计量为$\sum_{i=1}^n (X_i - \mu)^2$，成为卡方检验

### 置信区间
假设参数$\theta$的置信区间为$[L(X_{1:n}), U(X_{1:n})]$，由此可以说明$P_\theta (\theta \in [L, U]) = 1 - \alpha, \text{for any } \theta \ in \Theta$
这相当于如下假设检验:
$$H_0: \theta = \theta_0 \text{vs. } H_1: \theta \neq \theta_0$$
其中对于拒绝域$W(\theta_0)$可以定义概率$P_{\theta_0}(X_{1:n} \in W(\theta_0)) = \alpha, \text{for any } \theta_0 \in \Theta$

### p值
显著水平的重新选择需要重新计算参数空间中的拒绝域，引入p值就是根据样本把问题直接映射到显著水平的概率空间上来，直接与设定的显著水平做比较进行判断。
对于固定的样本集，我们可以计算出一个临界值p值，当$p \lt \alpha$ 时拒绝原假设，当$p \geq \alpha$ 时接受原假设。
$$p(x_{1:n}) = \mathop{}_{\theta \in \Theta_0}^{\sup} P_\theta(T(X_{1:n}) \gt T(x_{1:n}))$$
在原假设下，获得比目前样本集更极端采样结果的概率。
- p值可以视为样本与原假设的相容程度。当p值小于$\alpha$时，则认为两者不相容，拒绝原假设
- 检验时，可以先看p值，如果很小可以直接拒绝原假设，如果较大，则接受。这样避免考虑$\alpha$取值

----

## References
- [数理统计讲义 - 何志坚](http://www.hezhijian.com.cn/stat/index.html)


Photo by <a href="https://unsplash.com/@nika_benedictova?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Nika Benedictova</a> on <a href="https://unsplash.com/s/photos/dices?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

