# 模型构建

## 主模型：解释变量为是否得分

Logit（或者Probit）的二分类离散选择模型。被解释变量为得分概率，解释变量包含一系列和这个球有关的**客观因素**以及势头。如果确实存在势头$M_i(t)$这么一回事，前面应该存在一个正的系数。其他客观因素，是否接发球，球的落点。

去对手化：通过控制住和这个球有关的客观因素（速度、角度）等，分别考虑接球时的情况和发球时的情况（引入一个交叉项即可）
$$
\begin{aligned}
P(\text{Player1(i) get score})=\Phi(\mathbf{x_i;\theta})&\sim \theta_0M_i(t)+\theta_1\cdot if\_server(i)*quality(i)+\theta_2\cdot quality(i)
\end{aligned}
$$

| 数据集内名称     | 含义                   | 备注                                                  |
| ---------------- | ---------------------- | ----------------------------------------------------- |
| speed_mph        | 发球速度               | 体现接球难度                                          |
| winner_shot_type | 击球方式               | F=正手，B=反手，体现接球难度                          |
| server           | 谁发球                 | 1=球手1发球，2=球手2发球（if_server_1=1 if server=1） |
| location         | 和位置、角度有关的信息 | 根据绿色标签指标补充                                  |

## 势头$M(i)$：主观因素

势头会受到很多方面影响，本质上就是一种心理状态。势头会因为事件产生正向或负向突变，同时会因为时间流逝等因素自然衰减。

### 事件（冲击）

产生正向冲击的事件包括：赢得一分($\delta_{score}^{+}$)、赢得一局、赢得一盘

产生负向冲击的事件包括：丢球($\delta_{score}^{-}$)、赛间休息、未成功破发($\delta_{break\_fail}^-$)、被破发($\delta_{broken}^-$)、发球两次/发球两次都失误、低级错误

难以确定效果的事件包括：到达局点($\delta_{gameball}$)、到达破发点($\delta_{breakpoint}$)、到达抢七点

### 衰减

随着时间流逝、回合数增加($\beta_t$)、中场休息会自然衰减。这些影响因素可以用回合数($\beta_{rallycount}$)、跑动距离、对应第几盘、本盘已经持续的时间来量化

$$
M(i+1)-M(i)=(\delta_{score}^+\cdot if\_score(i)+\delta_{score}^-\cdot if\_nscore(i))\cdot e^{-\gamma t_i}
$$

简化模型下，势头的原始差分模型为：
$$
\begin{align}
M(i+1)-M(i)=&\delta_{score}^+\cdot if\_score(i)+\delta_{score}^-\cdot if\_nscore(i)\\
&+ \delta_{break\_fail}^-\cdot if\_break\_fail(i)+\delta_{broken}^-\cdot if\_broken(i)\\
&+ \delta_{gameball}\cdot if\_gameball(i)+ \delta_{breakpoint}\cdot if\_breakpoint(i)\\
&+\beta_{rallycount}\cdot rallycount(i)
\end{align}
$$
对单场比赛历程加总处理：
$$
M(n) = \sum_{i=0}^{n-1} (\delta_{score}^+\cdot if\_score(i)+\delta_{score}^-\cdot if\_nscore(i))\cdot e^{-\gamma t_i}
$$
简化模型下，LASSO所需要使用的总回归式如下：
$$
\Phi(x;\theta)=\sum_{i=0}^{n-1} M(i)+\theta_1\cdot if\_server(i)*quality(i)+\theta_2\cdot quality(i)+\theta_3\cdot if\_server
$$


## $$quality(i)$$：客观因素，关于球速、角度的评分

使用决策树（论文可以按随机森林写）对球的落点、球速等指标进行打分，给出这个球的quality。对于发球方，quality越高，越容易得分；对于接球方，quality越低，越难以得分。数据集采用所有球的记录进行训练。

这部分

# 估计方案

简化模型下，势头的原始差分模型为：
$$
\begin{align}
M(i+1)-M(i)=&\delta_{score}^+\cdot if\_score(i)+\delta_{score}^-\cdot if\_nscore(i)\\
&+ \delta_{break\_fail}^-\cdot if\_break\_fail(i)+\delta_{broken}^-\cdot if\_broken(i)\\
&+ \delta_{gameball}\cdot if\_gameball(i)+ \delta_{breakpoint}\cdot if\_breakpoint(i)\\
&+\beta_{rallycount}\cdot rallycount(i)
\end{align}
$$
对单场比赛历程加总处理：
$$
M(n) = \sum_{i=0}^{n-1} (\delta_{score}^+\cdot if\_score(i)+\delta_{score}^-\cdot if\_nscore(i))\cdot e^{-\gamma t_i}
$$
简化模型下，LASSO所需要使用的总回归式如下：
$$
\Phi(x;\theta)=\sum_{i=0}^{n-1} M(i)+\theta_1\cdot if\_server(i)*quality(i)+\theta_2\cdot quality(i)
$$
