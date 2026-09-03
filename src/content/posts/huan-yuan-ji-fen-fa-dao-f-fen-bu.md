---
title: '换元积分法到F分布'
description: '从换元积分、二维连续随机变量一路推导到 F 分布。'
publishedAt: '2021-06-05 14:14:47'
tags: [概率统计]
draft: false
---


## 从换元积分讲起

### basic
换元积分，是一种尝试通过变量替换和重写对积分进行简化的计算方法。
对于一元的换元积分，例如处理问题：
$$\int \sin(x^2) xdx $$
我们一般是通过凑出$u = x^2, u'=2x$，将问题解为为 $\int \frac{\sin(u)}{2} du = -\frac{\cos(u)}{2} + C = - \frac{\cos(x^2)}{2} + C$
这里是不定积分没有写变量取值，但在定积分时一般需要注意范围的变化。

对于换元积分，我们可以形式化得描述为：
- $g: [a,b] \rightarrow I, I \subseteq \Reals$
- g is a differentiable function with continuous derivative
- $f: I \rightarrow \Reals$
- f is a continuous function
$$\int_a^b f(g(x))g'(x)dx = \int_{g(a)}^{g(b)} f(u)du$$

反过来，我们对于一个积分$\int_a^b f(x)dx$，我们可以通过构建函数$x = g(u)$，把积分变换为$\int_{g^{-1}(a)}^{g^{-1}(b)} f(g(u))g'(u)du$，这里就要求函数g的反函数能满足在$[g^{-1}(a), g^{-1}(b)]$上保证differentiable with coutinous derivative。

换元法几何意义上是不同坐标系的切换，也可以视为量纲的切换，这一点尤其体现在微分含义的映射上，
比如变量$x$缩小100倍时，$x = g(u) = 100u$, $dx = g'(u)du = 100du$，即将原有的微分映射到新的量纲上，并进行大小上的修正。



### 二重积分
多元积分中的换元法和二重积分中的基本上是等价的，这里我们就只谈二重积分就可以了。即：
$$\int\int_R f(x,y) dxdy = \int\int_G f(g(u,v), h(u,v)) \det(J)dudv$$
其中定义：
- $x=g(u,v), y=h(u,v)$
- $J = \frac{\partial (x,y)} {\partial (u,v)} = \begin{pmatrix} \frac{\partial x}{\partial u} & \frac{\partial x}{\partial v} \\ \frac{\partial y}{\partial u} & \frac{\partial y}{\partial v} \end{pmatrix}$

加入更多变量，意味着Jacobian的维度和变量之间映射函数数量相应增加。
从这个视角上，可以很容易得把换元法推广到多元积分中。在多元积分中，变量数量的增加使坐标和量纲间的变换更为复杂，这种变换比在多元积分中使用jacobian determinant表示。
这个思路很自然
- 行列式本身即表示两种变换之间的量级的放大倍数。
- 而多元微分量上的变化可以写为
$$\begin{pmatrix} \partial x\\ \partial y \end{pmatrix} = \begin{pmatrix} \frac{\partial x}{\partial u} & \frac{\partial x}{\partial v} \\ \frac{\partial y}{\partial u} & \frac{\partial y}{\partial v} \end{pmatrix} \begin{pmatrix} \partial u\\ \partial v \end{pmatrix}$$

----

## 二维连续随机变量的分布
从积分角度看连续随机变量，概率密度函数可以认为是在定义域上积分为1的函数。
这样我们就可以把换元积分方法直接映射到随机变量的概率密度函数的转换上。在茆诗松老师统稿编写的概率论和数理统计教科书中，提到用于求取二维连续随机分布的“变量变换法”就是这种做法。

>
>设二维随机变量(X,Y)的联合密度函数为$p(x,y)$, 
>- 函数$u=g_1(x,y), v=g_2(x,y)$, 有连续偏导且存在唯一的反函数$x=x(u,v), y=y(u,v)$. 
>- 上式函数的jacobian determinant $J=\big| \frac{\partial (x,y)} {\partial (u,v)} \big| = \big| \frac{\partial (u,v)} {\partial (x,y)} \big|^{-1} \neq 0$
> - 如果$U=g_1(X,Y), V=g_2(X,Y)$，则(U,V)联合密度函数为
> $$p_{UV}(u,v)=p_{XY}(x(u,v),y(u,v)) |J|$$

对于更高维度连续随机变脸的求解也可以参考多重积分的换元法，使用相应维度的jacobian matrix与映射函数。

### 增补变量法
增补变量法是变量变化法的应用，可以用于求解关于二维连续随机变量(X,Y)函数$U=g(X,Y)$的概率分布。
映射到上节中的变量变化法中，可以设$u=g(x,y)$并令$v=x$或者$v=y$，这里我们就选$v=y$.
可以推得jacobian determinant
$$J = \frac{\partial x}{\partial u}$$
由此可以的$U=g(X,Y)$的概率分布为：
$$p_U(u) = \int_{-\infin}^{+\infin} p_{XY}(x(u,v), y(u,v)) \frac{\partial x}{\partial u}dv$$
如果X,Y独立，则可以拆开为：$\int_{-\infin}^{+\infin} p_X(x(u,v))p_Y(y(u,v)) \frac{\partial x}{\partial u}dv$

----

## F分布

F分布是抽样三大分布之一。定义如下：
设两个互相独立，且服从卡方分布的随机变量 $X_1 \sim \mathcal{X}^2(m), X_2 \sim \mathcal{X}^2(n)$，定义统计量$F=\frac{X_1/m}{X_2/n}$的分布为F分布，其中m为分子自由度，n为分母自由度。
对于其概率密度函数，可以用增补变量法进行推导。
### 导出$Z=\frac{X_1}{X_2}$
参考增补变量法，将u设定为此处的Z: 
- $u=\frac{x_1}{x_2}, v=x_2$
- 反函数$x_1=uv, x_2=v$
- Jacobian determinant: $\frac{\partial x_1}{\partial u} = v$
- 则随机变量U,也就是导出的目标Z的概率分布是：
$$p_U(u) = \int_{-\infin}^{+\infin} p_X(x(u,v))p_Y(y(u,v)) \frac{\partial x}{\partial u}dv = \int_{-\infin}^{+\infin} p_{X_1}(uv)P_{X_2}(v) vdv$$

因为$X_1 \sim \mathcal{X}^2(m), X_2 \sim \mathcal{X}^2(n)$，而自由度为n的卡方分布的概率密度写为$p(y) = \frac{(1/2)^{n/2}}{\Gamma(n/2)}y^{\frac{n}{2} - 1}e^{-\frac{y}{2}}, y > 0$。对于上面的概率分布，带入卡方分布的概率密度，展开为：
$$p_U(u) = \int_{0}^{+\infin} \frac{(1/2)^{m/2}}{\Gamma(m/2)}(uv)^{\frac{m}{2} - 1}e^{-\frac{uv}{2}} \frac{(1/2)^{n/2}}{\Gamma(n/2)}v^{\frac{n}{2} - 1}e^{-\frac{v}{2}} vdv$$
$$p_U(u) = \frac{u^{m/2 -1}}{\Gamma(m/2)\Gamma(n/2)2^{\frac{m+n}{2}}}\int_0^{+\infin} e^{-\frac{v}{2}(1+u)}v^{\frac{m+n}{2} - 1}dv$$

后续的推导需要先回顾下Gamma函数的公式，这可以认为是在复数空间上阶乘计算的推广。
$$\Gamma(z) = \int_0^{\infin} t^{z-1}e^{-t}dt$$

这里我们想把Gamma函数带入上式，凑一下z和t的定义。
- $z = \frac{m + n}{2}$
- $t = \frac{v(1+u)}{2}$

最终整理出以下表达式：
$$p_Z(z) = p_U(u) = \frac{\Gamma(\frac{m+n}{2})}{\Gamma(m/2)\Gamma(n/2)}u^{\frac{m}{2}-1}(1+u)^{-\frac{m+n}{2}}$$

### 导出$F=\frac{1/m}{1/n} Z$
最后在这里的推导上，使用一维的变量变换法：
> 关于连续随机变量X的函数$Y=g(X)$，且为严格单调函数（连续）
> 其反函数$X=h(Y)$有连续导函数，则Y的概率密度函数为：
>$$p_Y(y)=\begin{cases}p_X(h(y))|h'(y)|&\min(g(-\infin), g(\infin)) \lt y \lt \max(g(-\infin), g(\infin))\\0\end{cases}$$

由此带入$F=\frac{n}{m} Z$, 其反函数$Z=\frac{m}{n}F$:
$$p_F(y) = p_Z(\frac{m}{n}y) \frac{m}{n} = \frac{\Gamma(\frac{m+n}{2})(m/n)^{m/2}}{\Gamma(m/2)\Gamma(n/2)}y^{\frac{m}{2} - 1}(1+\frac{m}{n}y)^{-\frac{m+n}{2}}$$




----

## References
- 概率论与数理统计课程（茆诗松）- 第三版


Photo by <a href="https://unsplash.com/@tvschaitanya?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Chaitanya Tvs</a> on <a href="https://unsplash.com/s/photos/circle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

