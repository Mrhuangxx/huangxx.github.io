---
title: Social LSTM
mathjax: true
date: 2018-03-10 19:43:02
tags: paper
---
# [Social LSTM:Human Trajectory Prediction in Crowded Space](http://cvgl.stanford.edu/papers/CVPR16_Social_LSTM.pdf)
1. 论文发表在CVPR2016上(spotlight)。(已经引用121次)

2. 论文的核心思想： 
![](/Social-LSTM/15203542098731.jpg)


    将行人的轨迹预测问题看作是序列生成问题，对每一个行人使用一个LSTM网络。并且对于空间上临近的行人，引入了一个“Social pooling”层。通过这个"Social pooling"层，使得临近的行人能够共享他们的隐含状态 (这里的隐含状态指每个轨迹的LSTM中的隐含状态)。
3. 论文的创新点：完全的 data-driven 方式，没有人工提取的特征，没有设计的吸引力和排斥力(social force model)；同时考虑了相关的多个序列；建模了不同序列之间的相关性。
4. 论文的方法：
    
    问题规约： 对于时刻 $t$，$i^{th}$ 的坐标为$(x_t^i ,y_t^i )$。已经知道了行人时刻 1 到时刻 $T_{obs}$的坐标，希望知道$T_{obs+1}$ 到 $T_{pred}$ 的坐标。
    ![](/Social-LSTM/15197217698948.jpg)

如上图所示，在每一个time-step，LSTM cell 从临近的LSTM cell接受   共享的隐含状态信息(pooled hidden-state information),得到一个三维的tensor(前两维为平面坐标，第三个维度是隐含状态)。
1.  LSTM 的隐含状态  $h$$_{i}^t$捕获在时刻 t，第 i 个行人的隐含状态(latent representation)。
2. 通过构建隐含状态张量("Social" hidden-state tensor) $H$$_{i}^t$与邻居共享行人隐含状态：
> 给定隐含状态维度为$D$，邻居大小(即考虑范围)为$N_0$，对第 $i$ 个行人的轨迹构建一个 $N_0\times N_0 \times D$ 的tensor:
![](/Social-LSTM/15197416749439.jpg)
上式中：
* $h$$_{t-1}^j$ 是第 j 个行人在时刻 t-1 对应的LSTM 隐含状态。
* $1_{mn}[x, y]$是指示函数，检查 $(x, y)$是否在$(m, n)$表示的方格内部。
* $\mathscr{N}$$_i$ 是第 $i$ 个行人的邻居集合。
如下图所示为黑色点代表的行人的Social-pooling
![](/Social-LSTM/15197406060633.jpg)
上图中表示在某个特定的空间距离内(即上式中表示的N<sub>0</sub>)的存在三个邻居，分别用黄色、蓝色、橘色表示。
结合上图，可以理解方程(1)的含义： 首先，确定第 $i$ 个行人周围某个范围内的行人集合，然后以当前考虑行人为中心，确定各个邻居落在那个方格内，对于落在相同格子里的人，将其隐含状态相加。(上图中的示例 $N_0$等于$2$，而实验中实际$N_0$取的是32),得到tensor.

3.将张量$H_t^i$映射(embed)到向量 $a_t^i$, 将坐标映射到$e_t^i$ 。然后将这些向量连接起来，作为LSTM的输入。(这里需要注意的是在构建tensort时，当前考虑的行人的隐含状态是不包括在内的，此部分状态单独作为一部分输入到LSTM中，如图一所示)
![](/Social-LSTM/15197428867541.jpg)
上式中$\phi$为ReLU映射函数，$W_e$ 和 $W_a$ 为映射权重。

位置估计：
使用 $t$ 时刻的隐含状态预测 $t+1$ 时刻位置坐标的分布($\hat{x}$, $\hat{y}$)$_{t+1}^i$。论文中使用的概率模型为二维高斯分布, ($\hat{x}$, $\hat{y}$)$_{t}^i$ ~ $\mathscr{N}(\mu^i_t,\sigma^i_t,\rho^i_t)$ 。其参数如下所示：

> 均值为 $\mu^i_{t+1}=(\mu_x,\mu_y)^i_{t+1}$

> 标准差为 $\sigma^i_{t+1}=(\sigma_x,\sigma_y)^i_{t+1}$

> 相关系数为 $\rho^i_{t+1}$

这些参数通过一个$5 \times D$的权重矩阵$W_p$线性得到： 
    <center>$$[\mu_t^i,\sigma^i_t,\rho^i_t] = W_p \times h_i^{t-1} $$ </center>
LSTM 的参数通过最小化负数对数似然损失学得($L^i$表示第 $i$ 个人的轨迹):
![](/Social-LSTM/15197479345650.jpg)
（这里左边括号中应该还包括$W_a$, 原文中应该是漏掉了）
通过对训练集中所有的轨迹最小化这个损失学得参数。(论文中关于实现细节讲的很少，但是这篇文章公开了code.)

实验部分：选用的两个数据集分别是[ETH](http://www.vision.ee.ethz.ch/en/datasets/) 和 [UCY](https://graphics.cs.ucy.ac.cy/research/downloads/crowd-data), 使用三个不同的度量准则：
> 1. 平均偏移错误(Average displacement error)。所有预测的坐标和真实的坐标之间的MSE。

> 2. 最终偏移错误(Final displacement error)。模型预测的终点坐标和真实的终点坐标之间的差距。

> 3. 非线性区域平均偏移错误(Average non-linear displacement error)。由于大部分错误都发生在转弯时，因此，文章专门衡量了在这些区域的错误率。

> 实验结果如下图所示(均为错误率)(本文的对比实验也比较充分)：

![](/Social-LSTM/15197514964871.jpg)




    

 

