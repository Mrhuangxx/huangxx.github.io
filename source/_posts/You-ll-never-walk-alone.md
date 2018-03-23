---
title: You'll never walk alone
mathjax: true
date: 2018-03-23 19:45:00
tags: paper
---
# [You'll Never Walk Alone: Modeling Social Behavior for Multi-target Tracking](http://vision.cse.psu.edu/courses/Tracking/vlpr12/PellegriniNeverWalkAlone.pdf)
* 论文发表在 ICCV2009上，引用558次。

* 文章提出了一个LTA(Linear Trajectory Avoidance)模型，来建模短期行人的行为。(The model is designed for walking people with short-term prediction in mind).

##Modeling Social Behavior:

1. 使用$s_i = (p_i^t, v_i^t)$ 表示第$i$个行人的状态。其中，$p_i^t$表示二维平面上的位置，$v_i^t$表示第$i$个时刻的速度(矢量)。模型根据当前时刻所有对象的位置及速度预测下一个时刻每个人的速度。====
2. 假设每个行人都知道其他行人此刻的位置及速度。并且，每个行人都预测其他行人会保持当前的运动状态。假设行人$s_i$在当前时刻的速度为$\tilde{v}_i$，则他与行人$s_j$在时刻$t$的距离平方为$d_{ij}^2(t)$为： $$d_{ij}^2(t,\tilde{v_i})=\|p_i+t\tilde{v_i}-p_j-tv_j\|$$
这里$d_{ij}$依赖于$\tilde{v_i}$，表明正在使用$s_i$的视角。
3. 定义 $k_{ij}^t = p_i^t-p_j^t$, $q_{ij}^t =\tilde{v}_i-v_j^t$, 则可以将上式重写为：$$d_{ij}^2(t,\tilde{v_i})=\|k+  tq\|^2$$
4. 由于$s_i$对$s_j$的速度有一个估计，因而$s_i$会调整他的速度使得两人的最小距离${d_{ij}^*}^2$大于某个阈值(comfortable distance)。假设在时刻$t^*$，$s_i$和$s_j$的距离达到这个阈值:$$t^*= \underset{t>0}{\arg\min}\ d_{ij}^2(t,\tilde{v}_i)$$
5. 由于$d_{ij}^2(t,\tilde{v_i})$是$t$的二次函数，将其对$t$求偏导,并令其等于$0$得到：$$\frac{\partial{d_{ij}^2(t,\tilde{v}_i)}}{\partial{t}}=2(k+ {tq})q^{\top}=0 \rightarrow t^*=-\frac{k \cdot {q}}{\|q\|^2}$$
6. 将$t^*$带入到第三步中，即可得到最近距离: $${d_{ij}^*}^2(\tilde{v_i})=\|k-\frac{k\cdot{q}}{\|q\|^2}q^{\top}\|^2$$

    (${d_{ij}^*}^2(\tilde{v_i})$ is the expected minimum distance between pedestrian $i$ and $j$ under a constant-velocity assumption)，由于是以$s_i$的视角来看待这个问题，因而假定$s_j$的各个属性是已知的。${d_{ij}^*}^2(\tilde{v_i})$是一个关于$s_i$当前速度$\tilde{v_i}$的函数。
7. 为了确保$s_i$和$s_j$能够避开，可以给上式指定一个值。但是当存在多个行人时，这种做法不太方便。因而为了建模多个行人之间的互相作用，可以使用能量函数$E_{ij}$来表示行人$s_i$和$s_j$之间的相互作用。这个能量函数是${d_{ij}^*}^2$的函数：$$E_{ij}(\tilde{v}_i)=e^{-\frac{{d_{ij}^*}^2(\tilde{v_i})}{2\sigma_d^2}}$$
上式中$\sigma_d$控制到要避免的对象的距离(是一个要从数据中学习的参数)。当两个对象将要相撞时，$E_{ij}$变大;当${d_{ij}^*}^2$越来越大是，$E_{ij}$很小。
8. 通过上式，多个行人对$s_i$的影响可以建模为加权和。对每个对象$s_r(r\neq i)$,基于他当前的位置和与$s_i$的夹角$\phi$，分配一个权重$w_r(i)$: $$w_r(i)=w_r^d(i)w_r^\phi(i)$$ $$w_r^d(i)=e^{-\frac{\|k_{ir}\|^2}{2\sigma_w ^2}}$$ $$w_r^{\phi}=((1+cos(\phi))/2)^{\beta}$$ 其中，$\sigma_w$定义其他对象的影响半径。$\beta$ 控制视野权重项的峰值。
9. 对于$s_i$来说，其所有的相互作用能量(overall interaction energy)表示为: $$I_i(\tilde{v_i})=\sum_{r\neq i} {w_r(i)E_{ir}(\tilde{v_i})}$$
10. 以上只讨论了行人之间的相互影响，没有考虑环境信息。因此，假设每一个行人都有一个目的地 $z_i$, 并且行人倾向于保持一个速度$u_i$(desired speed)。这两个因素可以被表示为两个energy potentials.$$S_i(\tilde{v_i})=(u_i-\|\tilde{v_i}\|)^2$$ $$D_i(\tilde{v_i})=-\frac{(z_i-p_i)\cdot\tilde{v_i}}{\|z_i-p_i\|\cdot \|\tilde{v_i}\|}$$
11. $s_i$所有的能量(energy)可以被表示为: $$E_i(\tilde{v_i})=I_i(\tilde{v_i})+\lambda_1S_i(\tilde{v_i})+\lambda_2D_i(\tilde{v_i})$$
$\lambda_1$和$\lambda_2$为权重。要解决的问题就是求出当$E_i(\tilde{v_i})$取最小值时，对应的速度$\tilde{v_i}$.(求解方法是梯度下降法)
![](15214261731616.jpg)
上图为能量的示意图。黑色虚线代表当前的方向，$c_1$和$c_2$代表下一时刻可能的位置。品红色点代表当前的速度(矢量),白色点表示能量最少的速度(矢量)。
12. 下图示意的是当$s_1$遇到两个行人$s_2$和$s_3$，其能量图。中间的图纵轴$d_{23}$表示两个行人$s_2$和$s_3$之间的距离。从图的下方可以看到，当$S_2$和$s_3$相聚较远的时候，中间的能量较低，表明$s_1$可以从两人中间走过。在图片上方，$s_1$只能绕开$s_2$和$s_3$。
![](15214282365054.jpg)
13. 通过使能量函数最小化，可以计算下一个期望速度$\tilde{v_i^*}$。由于惯性的作用，对象必须经过一个转换过程。对象下一时刻的位置为: $$p_i^{t_N}=p_i+(\alpha_N v_i+(1-\alpha_N)\tilde{v_i^*})t_N$$ 
N为预测的间隔。$\alpha$是一个混合系数。
14. 对于静止的障碍。将它们视为速度为0，并且以此刻障碍上距离行人最近的点作为障碍的位置。
15. 模型的应用：给定$t$时刻的环境,通过最小化11式的能量函数，依次推测每    个行人$t+1$时刻的最优速度。然后将结果带入到13式中。一旦所有人的速度都已经确定后，同时更新。

## Training
模型总共包含了6个参数需要学习，${\sigma}_d$定义了comfortable distance; ${\sigma}_w$定义了radius of interest; 另外还有参数$\beta$，权重$\lambda_1$、$\lambda_2$.系数$\alpha$。在每一轮迭代中，每个对象依次模拟，同时保持其他对象ground-truth. 得出的参数表明，大约1m 是comfortable 的分界，6m 会影响行人的运动。

##Prediction
在每一个步中，使用预测地点和ground truth 之间的平均欧拉距离作为衡量标准。作为对比的模型是：匀速模型LIN；社会力模型；完整的LTA模型；以及在训练过程中，不使用行人之间相互作用，得到的一个模型DEST(仅包含目的地项的模型)
![](15214628479966.jpg)
上图中，比SF和DEST 好6%,比LIN好24%.为了更清楚的比较模型之间的差异，可以观察错误的分布。为此，作如下定义: 若预测出来的位置和ground-truth 之间的距离小于一个阈值，就认为正确。左图横轴表示阈值。可以看到，在阈值为1m的时候，预测的准确率接近75%。右图是预测的示意图。


