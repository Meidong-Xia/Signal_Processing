# 卡尔曼滤波器

## 模型和假设

- 设**状态（预测）模型**为：

$$
\underline{x}_k=\Phi_{k-1} \underline{x}_{k-1}+\underline{w}_{k-1} \tag{1}\label{1}
$$

其中$\underline{x}_k$是$k$时刻的状态变量，$$\underline{w}_{k-1}$$是零均值、协方差矩阵为$Q_{k}$的过程高斯噪声。

- 设**测量模型**为：

$$
\underline{z}_k=H_k \underline{x}_k+\underline{v}_k \tag{2}\label{2}
$$

$\underline{z}_k$是$k$时刻$\ell$个测量值的向量集合，$H_{k}$是测量矩阵，$\underline{v}_k$是零均值、协方差矩阵为$R_{k}$的测量高斯噪声。

- 设系统$k$时刻的先验估计为$\hat{\underline{x}}_k(-)$，基于$\underline{z}_{\mathbf{k}}$更新先验估计，更新后的估计设为$\underline{\hat{x}}_{\mathbf{k}}(+)$，该估计可以表示成**线性、递归**的形式：


$$
\hat{\underline{x}}_k(+)=K_k^{\prime} \underline{\hat{x}}_k(-)+K_k \underline{{z}}_k \tag{3}\label{3}
$$
其中$K_k^{\prime}$ 和 $K_k$是时变权重矩阵。

## 离散卡尔曼滤波器

首先将更新估计和先验估计表示成：
$$
\begin{aligned}
& \underline{\hat{x}}_k(+)=\underline{x}_k+\underline{\tilde{x}}_k(+) \\
& \underline{\hat{x}}_k(-)=\underline{x}_k+\underline{\tilde{x}}_k(-)
\end{aligned} \tag{4}\label{4}
$$
其中波浪线表示估计误差。联立式$\eqref{2}$~$\eqref{4}$易得：
$$
\underline{\tilde{x}}_k(+)=\left[K_k^{\prime}+K_k H_k-I\right] \underline{x}_k+K_k^{\prime} \underline{\tilde{x}}_k(-)+K_k \underline{v}_k \tag{5}\label{5}
$$
假设$E\left[\underline{v}_k\right]=\underline{0}$，$\mathrm{E}\left[\underline{\bar{x}}_{\mathbf{k}}(-)\right]=\underline{0}$。为了保证该估计器是无偏的，要求$\mathrm{E}\left[\tilde{\underline{x}}_{\mathrm{k}}(+)\right]=\underline{0}$，这就要求：
$$
\mathrm{K}_{\mathrm{k}}^{\prime}=\mathrm{I}-\mathrm{K}_{\mathbf{k}} \mathrm{H}_{\mathbf{k}} \tag{6}\label{6}
$$
将式$\eqref{6}$代入式$\eqref{3}$，估计器可以表示为：
$$
\underline{\hat{x}}_k(+)=\left(I-K_k H_k\right) \underline{\hat{x}}_k(-)+K_k \underline{z}_k \tag{7}\label{7}
$$
由式$\eqref{5}$，估计器误差可以表示为：
$$
\underline{\tilde{x}}_k(+)=\left(I-K_k H_k\right) \underline{x}_k(-)+K_k \underline{{v}}_k \tag{8}\label{8}
$$

### 误差协方差矩阵的更新

误差协方差矩阵可以表示为：
$$
P_k(+)=E\left[\tilde{\underline{x}}_k(+) \tilde{\underline{{x}}}_k(+)^T\right] \tag{9}\label{9}
$$
进一步推导，将式$\eqref{8}$代入式$\eqref{9}$：
$$
\begin{aligned}
P_k(+)= & E\left\{\left(I-K_k H_k\right) \underline{\tilde{x}}_k(-)\left[\underline{\tilde{x}}_k(-)^T\left(I-K_k H_k\right)^T+\underline{v}_k^T K_k^T\right]\right. \\
& \left.+K_k \underline{v}_k\left[\underline{\tilde{x}}_k(-)^T\left(I-K_k H_k\right)^T+\underline{v}_k^T K_k^T\right]\right\}
\end{aligned} \tag{10}\label{10}
$$
令：
$$
\begin{aligned}
& E\left[\underline{\tilde{x}}_k(-) \underline{\tilde{x}}_k(-)^T\right]=P_k(-) \\
& E\left[\underline{v}_k \underline{v}_k^T\right]=R_k
\end{aligned} \tag{11}\label{11}
$$
假设噪声和先验估计不相关：
$$
E\left[\underline{\tilde{x}}_k(-) \underline{v}_k^T\right]=E\left[\underline{v}_k \tilde{\underline{x}}_k(-)^T\right]=0 \tag{12}\label{12}
$$
将式$\eqref{11},\eqref{12}$代入式$\eqref{10}$，整理得到：
$$
P_k(+)=\left(I-K_k H_k\right) P_k(-)\left(I-K_k H_k\right)^T+K_k R_k K_k^ T \tag{13}\label{13}
$$

### $K_k$的最佳选择

选择$K_k$要遵照**最小均方误差准则**，即最小化：
$$
J_k=E\left[\underline{\tilde{x}}_k(+)^T S \underline{\tilde{x}}_k(+)\right] \tag{14}\label{14}
$$
其中$S$可以是任意半正定矩阵，注意**最优估计和$S$的选择无关**（见最小方差Bayes估计）。因此，可以选择$S=I$，这样：
$$
J_k=\operatorname{trace}\left[P_k(+)\right] \tag{15}\label{15}
$$
为了最小化均方误差，需要令$J_{k}$关于$K_{k}$的导数为0，即：
$$
-2\left(I-K_k H_k\right) P_k(-) H_k^T+2 K_k R_k=0 \tag{16}\label{16}
$$
求出：
$$
K_k=P_k(-) H_k^T\left[H_k P_k(-) H_k^T+R_k\right]^{-1} \tag{17}\label{17}
$$
$K_k$就是**卡尔曼增益矩阵**。

### 离散卡尔曼滤波器方程总结

将求出的最佳$K_k$代入误差协方差矩阵式$\eqref{13}$，得到：
$$
\begin{aligned}
P_k(+) & =P_k(-)-P_k(-) H_k T\left[H_k P_k(-) H_k^ T+R_k\right]^{-1} H_k P_k(-) \\
& =\left[I-K_k H_k\right] P_k(-)
\end{aligned} \tag{18}\label{18}
$$
联立式$\eqref{1},\eqref{4},\eqref{11}$，$n$时刻的先验估计$(-)$和$n-1$时刻的更新估计$(+)$的关系是：
$$
\begin{aligned}
& \underline{\hat{x}}_k(-)=\Phi_{k-1} \hat{\underline{x}}_{k-1}(+) \\
& \mathbf{P}_k(-)=\Phi_{k-1} P_{k-1}(+) \Phi_{k-1}^ T+Q_{k-1}
\end{aligned} \tag{19}\label{19}
$$
$\underline{x}_k \sim \mathcal{N}\left(\hat{\underline{x}}_{k}(+), P_{k}(+)\right)$，$\underline{x}_k \sim \mathcal{ N}\left(\Phi_{k-1}\hat{\underline{x}}_{k-1}(+), \Phi_{k-1} P_{k-1}(+) \Phi_{k-1}^T+Q_{k-1}\right)$。

综上所述，总结离散卡尔曼滤波器的所有公式为：

![image-20230909214901445](C:\Users\xiame\Desktop\Signal Processing\Kalman Filter.assets\image-20230909214901445.png)

### $K_k$的另一种表达

$P_k^{-1}(+)$可以被表示为：
$$
\begin{aligned}
P_k^{-1}(+)&=P_k^{-1}(-)+H_k^T R_k^{-1} H_k \\
&=(\Phi_{k-1} P_{k-1}{ }(+) \Phi_{k-1} T+Q_{k-1})^{-1}+H_k^T R_k^{-1} H_k
\end{aligned}\tag{20}\label{20}
$$
将其代入$K_k$的表达式，可得到：
$$
\begin{aligned}
K_k & =\left\{P_k(+) P_k^{-1}(+)\right] P_k(-) H_k^T\left[H_k P_k(-) H_k^T+R_k\right]^{-1} \\
& =P_k(+)\left[P_k^{-1}(-)+H_k^T R_k^{-1} H_k\right] P_k(-) H_k^T\left[H_k P_k(-) H_k^T+R_k\right]^{-1}\\
& =P_k(+) H_k^T\left[I+R_k^{-1} H_k P_k(-) H_k^T\right]\left[H_k P_k(-) H_k^T+R_k\right]^{-1} \\
& =P_k(+) H_k^T R_k^{-1}
\end{aligned} \tag{21}\label{21}
$$

### 最优估计器的性质

最优估计和误差是不相关的，即：
$$
E\left[\hat{\mathbf{x}}_{\mathrm{k}}(+) \tilde{\mathbf{x}}_{\mathrm{k}}(+)^{\mathrm{T}}\right]=0 \tag{22}\label{22}
$$

## 连续卡尔曼滤波器

![image-20230910144821986](C:\Users\xiame\Desktop\Signal Processing\Kalman Filter.assets\image-20230910144821986.png)

## 误差协方差矩阵与`CRB`

对于一个确定非线性系统，当没有预测误差（即状态变量是确定的$\underline{w}_{k-1}=0$），并且测量方程的误差是高斯噪声时，满足：
$$
\left(P_K^*\right)^{-1}=\left(\Phi_{K-1} P_{K-1}^* \Phi_{K-1}^T\right)^{-1}+H_K^T R_K^{-1} H_K \tag{23}\label{23}
$$
其中$P^* \triangleq J^{-1}$是`Fisher`信息矩阵的逆矩阵，注意式$\eqref{23}$和式$\eqref{20}$具有相同的形式（此时$Q_{k}=0$）。

## 扩展卡尔曼滤波器

当状态模型和测量模型是非线性模型时，无法使用通常的卡尔曼滤波器。而**扩展卡尔曼滤波器是针对非线性系统的、将当前均值和协方差线性化的卡尔曼滤波器**。

非线性随机微分方程为：
$$
\dot{\underline{x}}(t)=\underline{f}(\underline{x}(t), t)+\underline{w}(t) \tag{24} \label{24}
$$
非线性测量模型为：
$$
\underline{z}_k=\underline{h}_k\left(\underline{x}\left(t_k\right)\right)+\underline{v}_k, \quad k=1,2, \ldots \tag{25} \label{25}
$$
其中$\dot{()}$表示求导，$w_{t}$表示零均值、谱密度矩阵为$\mathrm{Q}(\mathrm{t})$的高斯噪声，$v_k$是零均值、协方差矩阵为$\mathrm{R}_{\mathrm{k}}$的高斯随机噪声。

**不管是什么概率密度函数，最小方差估计总是状态向量的条件均值**。

假设初始情况下：
$$
E\left[\underline{x}\left(t_{k-1}\right)\right]=\hat{\underline{x}}\left(t_{k-1}\right) \tag{26} \label{26}
$$
$\hat{\underline{x}}\left(t_{k-1}\right)$已知。

对式$\eqref{24}$做积分，可得：
$$
\underline{x}(t)=\underline{x}\left(t_{k-1}\right)+\int_{t_{k-1}}^t \underline{f}(\underline{x}(\tau), \tau) d \tau+\int_{t_{k-1}}^t \underline{w}(\tau) d \tau \tag{27} \label{27}
$$
两边做期望和求导，可得：
$$
\frac{d}{d t} E[\underline{x}(t)]=E[\underline{f}(\underline{x}(t), t)], \quad t_{k-1} \leqslant t<t_k \tag{28} \label{28}
$$
将其表示为：
$$
\dot{\hat{\underline{x}}}(\mathrm{t})=\hat{\underline{f}}(\underline{x}(\mathrm{t}), \mathrm{t}), \quad \mathrm{t}_{\mathrm{k}-1} \leqslant \mathrm{t}<\mathrm{t}_{\mathrm{k}} \tag{29} \label{29}
$$
其中$\hat{()}$表示期望操作。误差协方差矩阵可以表示为：
$$
P(t) \triangleq E\left[[\hat{\underline{x}}(t)-\underline{x}(t)][\hat{\underline{x}}(t)-\underline{x}(t)]^T\right] \tag{30} \label{30}
$$
将式$\eqref{27}$代入式$\eqref{30}$，同时求导，可得：
$$
\dot{P}(t)=\hat{\underline{x} \underline{f}^T}-\underline{\hat{x}} \underline{\hat{f}}^T+\hat{\underline{f} \underline{x}^T}-\underline{\hat{f}} \underline{\hat{x}}^T+Q(t), \quad t_{k-1} \leqslant t<t_k \tag{31} \label{31}
$$
为了求取$\hat{\underline{f}}(\mathrm{x}, \mathrm{t})$，对$\underline{f}(\mathrm{x}, \mathrm{t})$做泰勒级数展开：
$$
\underline{f}(\underline{x}, t)=\underline{f}(\hat{\underline{x}}, t)+\left.\frac{\partial \underline{f}}{\partial \underline{x}}\right|_{\underline{x}=\hat{\underline{x}}}(\underline{x}-\hat{\underline{x}})+\ldots \tag{32} \label{32}
$$
两边取期望：
$$
\hat{\underline{f}}(\underline{x}, t)=\underline{f}(\hat{\underline{x}}, t)+\underline{0}+\ldots = \underline{\dot{\hat{{x}}}}(\mathrm{t}) \tag{33} \label{33}
$$
将式$\eqref{33}$代入式$\eqref{31}$，得到：
$$
\dot{ {P}}(\mathrm{t})= {F}(\hat{\underline{x}}(\mathrm{t}), \mathrm{t})  {P}(\mathrm{t})+ {P}(\mathrm{t})  {F}^{\mathrm{T}}(\hat{\underline{x}}(\mathrm{t}), \mathrm{t})+ {Q}(\mathrm{t}), \quad \mathrm{t}_{\mathrm{k}-1} \leqslant \mathrm{t}<\mathrm{t}_{\mathrm{k}} \tag{34} \label{34}
$$
其中$ {F}(\hat{\underline{x}}(\mathrm{t}), \mathrm{t})$的$(i,j)$元素为：
$$
\left.f_{i j}(\hat{\underline{x}}(t), t) \triangleq \frac{\partial f_i(\underline{x}(t), t)}{\partial \underline{x}_j(t)}\right|_{\underline{x}(t)=\hat{\underline{x}}(t)} \tag{35} \label{35}
$$
**下面构建状态变量和误差协方差矩阵的更新公式**。首先将估计向量表示为：
$$
\hat{\underline{x}}_{ {k}}(+)=\underline{a}_{ {k}}+ {K}_{ {k}} \underline{z}_{ {k}} \tag{36} \label{36}
$$
下一步工作是确定$\underline{a}_{ {k}}, {K}_{ {k}}$。将先验估计误差和更新估计误差分别表示为：
$$
\begin{aligned}
& \tilde{\underline{x}}_{ {k}}(+) \triangleq \hat{\underline{x}}_{ {k}}(+)-\underline{x}_{ {k}} \\
& \tilde{\underline{x}}_{ {k}}(-) \triangleq \hat{\underline{x}}_{ {k}}(-)-\underline{x}_{ {k}}
\end{aligned} \tag{37} \label{37}
$$
联立$\eqref{25},\eqref{36},\eqref{37}$，得到：
$$
\tilde{\underline{x}}_k(+)=\underline{a}_k+ {K}_k h_k\left(\underline{x}_k\right)+ {K}_k \underline{v}_k+\tilde{\underline{x}}_k(-)-\hat{\underline{x}}_k(-) \tag{38} \label{38}
$$
要求估计是无偏的，即$\mathrm{E}\left[\tilde{\underline{x}}_{{k}}(+)\right]=0$，并且$\mathrm{E}\left[\tilde{\underline{x}}_{ {k}}(-)\right]=\mathrm{E}\left[\underline{v}_{ {k}}\right]=\underline{0}$，得到：
$$
\underline{a}_k+ {K}_k \hat{h}_k\left(\underline{x}_k\right)-\hat{\underline{x}}_k(-)=\underline{0} \\
\hat{\underline{x}}_{ {k}}(+)=\hat{\underline{x}}_{ {k}}(-)+ {K}_{ {k}}\left[{z}_{ {k}}-\underline{\hat{h}}_{ {k}}\left(\underline{x}_{ {k}}\right)\right]\tag{39} \label{39}
$$
写出误差协方差矩阵：
$$
 {P}_k(+)=E\left[\tilde{\underline{x}}_k(+) \tilde{\underline{x}}_k(+)^T\right] \tag{40} \label{40}
$$
![image-20230911171225718](C:\Users\xiame\Desktop\Signal Processing\Kalman Filter.assets\image-20230911171225718.png)

同时，定义：
$$
\begin{aligned}
&  {P}_{ {k}}(-)=E\left[\tilde{\underline{x}}_{ {k}}(-) \tilde{\underline{x}}_{ {k}}(-)^{\mathrm{T}}\right] \\
&  {R}_{ {k}}= {E}\left[\underline{v}_{ {k}} \underline{v}_{ {k}}{ }^T\right]
\end{aligned} \tag{41} \label{41}
$$
通过最小化均方误差，可得：
$$
\begin{aligned}
 {K}_k= & -E\left[\underline{\tilde{x}}_k(-)\left[\underline{h}_k\left(\underline{x}_k\right)-\underline{\hat{h}}_k\left(\underline{x}_k\right)\right]^T\right] \\
& \times\left\{E\left[\left[\underline{h}_k\left(x_k\right)-\underline{\hat{h}}_k\left(\underline{x}_k\right)\right]\left[\underline{h}_k\left(x_k\right)-\underline{\hat{h}}_k\left(\underline{x}_k\right)\right]^T\right]+R_k\right\}^{-1}
\end{aligned} \tag{42} \label{42}
$$
将式$\eqref{42}$代入式$\eqref{40}$，可得：

$$
P_k(+)=P_k(-)+K_k E\left[\left[\underline{h}_k\left(\underline{x}_k\right)-\hat{\underline{h}}_k\left(\underline{x}_k\right)\right] \underline{\tilde{x}}_k(-)^T\right] \tag{43} \label{43}
$$
但是式$\eqref{43}$是物理不可实现的，因为$\hat{\underline{h}}_k$难以计算（依赖于$\underline{x}(t)$的概率密度函数）。为了解决这一问题，同样使用泰勒计算展开：
$$
\underline{h}_k\left(\underline{x}_k\right)=\underline{h}_k\left(\underline{\hat{x}}_k(-)\right)+H_k\left(\hat{\underline{x}}_k(-)\right)\left(\underline{x}_k-\hat{\underline{x}}_k(-)\right)+\ldots \tag{44} \label{44}
$$
其中：
$$
H_k\left(\hat{\underline{x}}_k(-)\right)=\left.\frac{\partial \underline{{h}}_k(\underline{x})}{\partial \underline{{x}}}\right|_{\underline{{x}}=\hat{\underline{x}}_k(-)} \tag{45} \label{45}
$$
将式$\eqref{44}$代入式$\eqref{39},\eqref{42},\eqref{43}$，得到：

![image-20230911172914012](C:\Users\xiame\Desktop\Signal Processing\Kalman Filter.assets\image-20230911172914012.png)

最后，扩展卡尔曼滤波器总结如下：

![image-20230911174521894](C:\Users\xiame\Desktop\Signal Processing\Kalman Filter.assets\image-20230911174521894.png)

 如果状态方程不是微分方程，那么可以将状态估计传播和误差矩阵传播方程改为：
$$
\begin{aligned}
& \hat{\underline{x}}_k(-)=\underline{f}(\hat{\underline{x}}_{k-1}(+)) \\
& P_{k}(-)=F(\hat{\underline{x}}_{k-1}(+))P_{k-1}(+)F(\hat{\underline{x}}_{k-1}(+))+Q_{k-1} 
\end{aligned} \tag{46} \label{46}
$$













































