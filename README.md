# 模型构建

## 主模型：解释变量为是否得分

Logit（或者Probit）的二分类离散选择模型。被解释变量为得分概率，解释变量包含一系列和这个球有关的**客观因素**以及势头。如果确实存在势头$M_i(t)$这么一回事，前面应该存在一个正的系数。，是否接发球，球的落点，比赛双方之间的克制与被克制关系等（这个就设置一个固定效应系数c_ij）
$$
\begin{aligned}
P(\text{Player1(i) get score})=&\Phi(\mathbf{x_i;\theta})\\
&\sim \theta_0M_i(t)\\
&+\theta_1\cdot if\_server\_1\\
&+\theta_3\cdot p1\_net\_pt*if\_server\_2+\theta_4\cdot p1\_net\_pt*if\_server\_1\\
&+\theta_5\cdot speed\_mph\\
&+c_{ij}
\end{aligned}
$$

## 子模型：解释变量为势头$M_i(t)$

势头会受到很多方面影响，本质上就是一种心理状态。

## 影响因素

1. 比分差距
2. 破发、赛点是否会影响势头？
3. 体能因素



## 特征工程

1. Time：获得该得分的所需时间（做一个差分就行），考虑这个球打了几个来回
2. Game、Set：该得分所在的局（Game）- 盘（Set）- 赛（Match，这个数据没有，要找额外的数据），考虑耐力
3. Win_Game：**赢局数，考虑动量**
4. Win_Set：**赢盘数，考虑动量**