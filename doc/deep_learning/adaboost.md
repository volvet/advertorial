# 特征选择 - AdaBoost
特征选择问题: $X$有$N$个特征值
$$
X = 
\begin{bmatrix}
x_1 \\\\
x_2 \\\\
. \\\\
. \\\\
. \\\\
x_N
\end{bmatrix}
$$
在这$N$个特征值中如何选取$M$个特征值， 使识别率最高.

## AdaBoost 算法流程
数据集$T = \\{ (x_1, y_1), (x_2, y_2), \cdots, （x_N, y_N） \\}$  
二分类问题： $y_i = \\{ -1, 1\\}$  
* 初始化采样权重 $D_1 = (\omega_{11}, \omega_{12}, \cdots, \omega_{1N}), \omega_{1i} = \frac{1}{N}, i = 1, 2, \cdots, N$
* 对$m = 1, 2, \cdots, M$, $M$为弱分类器的个数， 用$D_m$采集$N$个训练样本， 在训练样本上获得弱分类器
$$
G_m(x) = \begin{cases} 1 \\\\ 
-1
\end{cases}
$$
* 计算加权错误率, $\alpha_m$为分类器$G_m$的权重
$$
e_m = P(G_m(x_i) \neq y_i) = \sum_{i=1}^{N}\omega_iI(G_m(x_i) \neq y_i) \\\\
\alpha_m = \frac{1}{2} \log \frac{1 - e_m}{e_m}
$$
* 更新权值分布
$$
D_{m+1} = (\omega_{m+1, 1}, \omega_{m+1, 2}, \cdots, \omega_{m+1, N}) \\\\
\omega_{m+1, i} = \frac{\omega_{m, i}}{Z_m} e^{-\alpha_my_iG_m(x_i)} \\\\ 
Z_m = \sum_{i=1}^{N}\omega_{m, i} e^{-\alpha_my_iG_m(x_i)}
$$
* 回到step 2, 循环 $m$次
* 最终识别器为$G(x)$
$$
f(x) = \sum_{m=1}^{M}\alpha_mG_m(x) \\\\
g(x) = \mathop{sign}(f(x))
$$

## AdaBoost 定理
随着$M$增加， AdaBoost最终的分类器$G(x)$在训练集上的错误率将会越来越小.  
错误率:
$$
\begin{align}
E &= \frac{1}{N}\sum_{i=1}^{N}I(G(x_i) \neq y_i) \leq \frac{1}{N}\sum_{i=1}^{N}e^{-y_if(x_i)} \\\\
&= \prod_{i=1}^{M} Z_m
\end{align}
$$
证明:
$$
\begin{align}
E & \leq \frac{1}{N} \sum_{i=1}^{N} e^{- \sum_{m=1}^{M}\alpha_m y_i G_m(x_i)} \\\\
& = \sum_{i=1}^{N}\omega_{1,i} \prod_{m=1}^{M} e^{- \alpha_m y_i G_m(x_i)} \\\\
& = \sum_{i=1}^{N}(\omega_{1,i}e^{-\alpha_1 y_1 G_1(x_i)})(\prod_{m=2}^{M}e^{-\alpha_m y_m G_m(x_i)}) \\\\
& = \sum_{i=1}^{N}(\omega_{2,i} Z_1) (\prod_{m=2}^{M}e^{-\alpha_m y_m G_m(x_i)} )  \\\\
& = \cdots \\\\
& = \prod_{i=1}^{M} Z_i
\end{align}
$$

来看$Z_m$:
$$
\begin{align}
Z_m & = \sum_{i=1}^{N}\omega_{m, i}e^{-\alpha_m y_i G_m(x_i)} \\\\
& = \sum_{i=1, y_i = G_m(x_i)}^{N}\omega_{m, i}e^{-\alpha_m} + \sum_{i=1, y_i \neq G_m(x_i)}^{N}\omega_{m, i}e^{\alpha_m} \\\\
& = (1 - e_m)e^{-\alpha_m} + e_me^{\alpha_m} \\\\
\end{align}
$$
将$\alpha_m = \frac{1}{2} \log \frac{1 - e_m}{e_m}$代入上式:
$$
Z_m = 2\sqrt{e_m(1-e_m)}
$$
易得: 若$e_m < \frac{1}{2}$, 则$Z_m < 1$, 所以$M$越大, $E$便越小， 得证.



## Reference
* 浙江大学机器学习课程 39 - 胡浩基
