---
paper: https://doi.org/10.48550/arXiv.2403.19332
code: https://github.com/tayalmanan28/Stochastic-NCBF
---
## 引言

### 相关研究

- 由于神经网络在逼近通用函数的优越性，NCBF备受关注。
- 已有方法：
	- 从专家演示中学习、混合整数规划、非线性规划等。**缺点**在于训练好的CBF需要作为后处理步骤进行验证，可能造成较大的试错成本
	- **基于数据的NCBF训练方法**：通过界定NCBF的Lipschitz常数，使用离散样本点集合来验证连续状态空间的安全性。**缺点**在于仅考虑了离散时间下的确定性系统，在连续时间下还需要考虑CBF的李导数性质；且不适用于随机系统
### 本文工作

- 提出了一个适用于**连续时间随机系统**的、用于构建可证明可行的NCBF的训练框架
- 通过推导保证有效性的条件，建立了**仅使用有限个数据样本点**即可确保CBF在整个连续状态空间中有效性的完备性保证
- 使用倒摆和自动驾驶系统的障碍物避让两个系统来评估本文方法的性能，且与基线方法相比确保了更大的安全区域

---
## 问题表述

### 问题1

考虑一个连续时间随机系统
$$ dx(t) = f (x(t)) + g(x(t))u(t) dt + \sigma dW (t) $$
系统的状态集合为$\mathcal{X}$
使用一个前馈神经网络 $\widetilde{h}(x \mid \theta)$ 作为随机系统的SNCBF，其中$\theta$为网络的可训练参数
定义系统的初始安全集 $\mathcal{X}_s \subseteq \mathcal{C}$ 和初始非安全集 $\mathcal{X}_u \subseteq \mathcal{X} - \mathcal{C}$
#### 统一原始约束要求

要求该CBF满足以下三个约束要求：
$$
\begin{align*}
&\widetilde{h}(x \mid \theta) \geq 0,\forall x \in \mathcal{X}_s \\
&\widetilde{h}(x \mid \theta) < 0,\forall x \in \mathcal{X}_u \\
&\nabla{\widetilde{h}}(x \mid \theta) (f(x) + g(x)u(x)) + \frac{1}{2} \text{tr} \left( \sigma^\tau \frac{\partial^2 \widetilde{h}(x \mid \theta)}{\partial x^2} \sigma \right) + \kappa \left( \widetilde{h}(x \mid \theta) \right) \geq 0, \forall x \in \mathcal{X}.
\end{align*}
$$
为统一三个约束的形式，定义：
$$

\begin{align*}
q_1(x) &= \big(-\widetilde{h}(x \mid \theta)\big)\mathbf{1}_{X_s}, \\
q_2(x) &= \big(\widetilde{h}(x \mid \theta) + \delta\big)\mathbf{1}_{X_u}, \\
q_3(x) &= -\nabla \widetilde{h}(x \mid \theta)(f(x) + g(x)u(x)) - \frac{1}{2} \operatorname{tr}\Big(\sigma^\tau \frac{\partial^2 \widetilde{h}(x \mid \theta)}{\partial x^2}\sigma \Big) - \kappa(\tilde{h}(x \mid \theta))
\end{align*}

$$
**其中$\delta > 0$为一小量，确保严格不等式**
则约束可写为统一形式 $q_k(x) \leq 0, k = 1,2,3.$

#### 转化为鲁棒优化问题（ROP）

$$
ROP:
\begin{align*}
\begin{cases}
\min_{\psi} & \psi \\
s.t. & \max\{q_k(x)\} \leq \psi, k \in \{1, 2, 3\} \\
& \forall x \in \mathcal{X}, \psi \in \mathbb{R},
\end{cases}
\end{align*}
$$
若优化的最终最优解 $\psi^* \leq 0$，则CBF能满足原来的约束要求，CBF有效。

**难点**：该ROP实际上包含了无穷个约束（$\forall x \in \mathcal{X}$，$\mathcal{X}$为连续的状态空间）该ROP不可直接求解。故考虑继续转化为数据驱动的**场景优化问题（SOP**），即从$\mathcal{X}$中采样有限个点$\{x_i\}_{i=1}^N$，以此近似原始的无限约束。

#### 转化为场景优化问题（SOP）

定义三个采样集合 
- $\mathcal{S}$为来自初始安全集$\mathcal{X_s}$的样本集合
- $\mathcal{U}$为来自初始不安全集$\mathcal{X_u}$的样本集合
- $\mathcal{D}$为来自整个状态集$\mathcal{X}$的样本集合

**采样的近似方式**：对于$\forall x \in \mathcal{X}$，确保存在$x_i$使 $|| x - x_i || \leq \tilde{\epsilon}$，$\tilde{\epsilon}$称为采样近似的**覆盖半径**，表征采样覆盖的精细程度

则原ROP可转化为SOP：
$$
\begin{align*}
SOP:
\begin{cases}
\min_{\psi} & \psi \\
s.t. & q_1(x_i) \leq \psi, \forall x_i \in S, \\
& q_2(x_i) \leq \psi, \forall x_i \in \mathcal{U}, \\
& q_3(x_i) \leq \psi, \forall x_i \in \mathcal{D}, \\
& \psi \in \mathbb{R}, i \in \{1, \ldots, N\},
\end{cases}
\end{align*}

$$

记SOP的最终最优解为$\psi^*$，$q_k(x), k=1,2,3$ 的Lipschitz常数分别为$L_k(x)$，三者最大值为$L_{max}$
则由
$$
\begin{align*}
q_k(x) &= q_k(x) - q_k(x_i) + q_k(x_i) \\
&\leq L_k \|x - x_i \| + \psi^* \\
&\leq L_k \bar{\epsilon} + \psi^* \leq L_{\max} \bar{\epsilon} + \psi^* \leq 0.
\end{align*}
$$
**可以得到确保CBF有效性的一个充分条件：若$q_k(x), k=1,2,3$ 满足$L_{max} \tilde{\epsilon} + \psi^* \leq 0$，则满足原始约束的函数 $\widetilde{h}(x \mid \theta)$ 是一个可行的CBF。**

---
### 问题2

给定连续时间随机控制系统
$$ dx(t) = f (x(t)) + g(x(t))u(t) dt + \sigma dW (t) $$
以及**系统状态的数据集$\mathcal{S}, \mathcal{U}, \mathcal{D}$**，目标是设计一个算法来构造SNCBF $\widetilde{h}(x \mid \theta)$ 以及相应的基于SNCBF-QP的控制器 $u$，要求其满足上述SOP中的条件以及 $L_{max} \tilde{\epsilon} + \psi^* \leq 0$

---

## 构造SNCBF的算法

提出了一个解决问题2的方法
### SNCBF损失函数的设计
#### 考虑SOP中优化目标的子损失函数

针对上述SOP中的要求，考虑以下分别在训练集$\mathcal{S}, \mathcal{U}, \mathcal{D}$上的三个损失函数
$$
\begin{align*}
\mathcal{L}_1(\theta) &= \frac{1}{N} \sum_{x_i \in S} \max \left( 0, q_1(x_i) - \psi \right), \\
\mathcal{L}_2(\theta) &= \frac{1}{N} \sum_{x_i \in \mathcal{U}} \max \left( 0, q_2(x_i) - \psi \right), \\
\mathcal{L}_3(\theta) &= \frac{1}{N} \sum_{x_i \in \mathcal{D}} \max \left( 0, q_3(x_i) - \psi \right).
\end{align*}
$$
(该损失函数的最小值为0，即在$q(x) \leq \psi$时可达到最小，故可以确保SOP的优化目标)
则可以给出**考虑SOP中优化目标的子损失函数**
$$
\begin{align*}
\mathcal{L}_{\theta}(\theta) = \mathcal{L}_1 + \lambda_1 \mathcal{L}_2 + \lambda_2 \mathcal{L}_3, \lambda_1,\lambda_2 \in \mathbb{R_+}
\end{align*}
$$
$\lambda_1, \lambda_2$为相应权重

#### 考虑约束中函数的Lipschitz连续性的子损失函数

要使用上述SOP中提及的定理来证明SNCBF的有效性，需要**确保$q_k(x), k=1,2,3$是Lipschitz连续的**，故需要确保以下三项的Lipschitz连续性
$$
\begin{align*}
&\widetilde{h}(x_i \mid \theta) \\
&\frac{\partial \tilde{h}(x_i|\theta)}{\partial x} \\
&tr(\sigma^{\tau} \frac{\partial^2 \tilde{h}(x_i | \theta)}{\partial x^2} \sigma)
\end{align*}
$$
若这三项为Lipschitz连续的，记其Lipschitz常数分别为$L_h$，$L_{dh}$和$L_{d2h}$

**对于$\widetilde{h}(x \mid \theta)$的Lipschitz连续性**，有以下引理：
[26] P. Pauli, A. Koch, J. Berberich, P. Kohler, and F. Allgo ̈wer, “Training robust neural networks using lipschitz bounds,” IEEE Control Systems Letters, vol. 6, pp. 121–126, 2021.

$f_{\theta}$为一 $l$ 层的前馈神经网络，可训练参数为$\theta$。则对$f_\theta$的$L$-Lipschitz连续性的判别可由**半正定约束**$M(\theta, \Lambda)$给出。
即：若存在$\Lambda \in \mathbb{D}_+^n$满足以下不等式，则$f_\theta$为Lipschitz连续的，且其Lipschitz常数为$L$。
$\mathbb{D}_+^n$为n阶正对角矩阵集，$\theta_0,...\theta_l$为网络各层的权重矩阵，$\alpha$和$\beta$是激活函数的最小和最大斜率。
$$
\begin{gathered}
M(\theta, \Lambda) :=
\begin{bmatrix}
A \\
B
\end{bmatrix}^T
\begin{bmatrix}
2\alpha\beta\Lambda & -(\alpha+\beta)\Lambda \\
-(\alpha+\beta)\Lambda & 2\Lambda
\end{bmatrix}
\begin{bmatrix}
A \\
B
\end{bmatrix} \\
+
\begin{bmatrix}
L^2\boldsymbol{I} & 0 & 0 & 0 \\
0 & 0 & 0 & 0 \\
0 & 0 & 0 & -\theta_l^T \\
0 & 0 & -\theta_l & \boldsymbol{I}
\end{bmatrix}\succeq0, \\
\mathrm{where} \\
A=
\begin{bmatrix}
{\theta_{0}} & {\ldots} & {0} & {0} \\
{\vdots} & {\ddots} & {\vdots} & {\vdots} \\
{0} & {\ldots} & {\theta_{l-1}} & {0}
\end{bmatrix},B=
\begin{bmatrix}
{0} & {I}
\end{bmatrix},
\end{gathered}
$$
**当网络只有一层时**（本文代码实现中使用的$M$矩阵）：

$$
M(\theta, \Lambda)=\left[\begin{array}{ccc}
L^2 \boldsymbol{I}+2 \alpha \beta \theta_0^T \Lambda \theta_0 & -(\alpha+\beta) \theta_0^T \Lambda & 0 \\
-(\alpha+\beta) \Lambda \theta_0 & 2 \Lambda & -\theta_1 \\
0 & -\theta_1 & \boldsymbol{I}
\end{array}\right] \succeq 0
$$

**对于$\frac{\partial \tilde{h}(x_i|\theta)}{\partial x}$的Lipschitz连续性**，有以下定理：

考虑一个单层的前馈神经网络$f_\theta$，输出层维度为$1 \times 1$，$x$为输入，$y$为输出
$\theta_i, i \in 0,1$为网络的权重参数，$\theta = \{ \theta_0, \theta_1 \}$，$\phi$为激活函数
则$\frac{\partial y} {\partial x}$的$L$-Lipschitz连续性的判别可由$M_\hat{\phi}(\hat{\phi}, \Lambda) \succeq 0$给出
其中$\hat{\phi} = \phi^{\prime}$，$\hat{\theta} = (\theta_0, \hat{\theta_1})$，$\theta_1 := \theta^\tau diag(\theta_1)$

**相似地，$tr(\sigma^{\tau} \frac{\partial^2 \tilde{h}(x_i | \theta)}{\partial x^2} \sigma)$的Lipschitz连续性的判别可以由$M_\bar{\phi}(\bar{\theta}, \Lambda)$给出**，
其中$\bar{\phi} = \phi''$，$\bar{\theta} = (\theta_0, \bar{\theta_1})$，且
$$
\overline{\theta}_1 = \begin{bmatrix} \sum_{j=0}^r \sigma_j^2 \theta_1^{j1} \theta_0^{1j} & \ldots & \sum_{j=0}^r \sigma_j^2 \theta_1^{jn} \theta_0^{pj} \end{bmatrix}
$$

处理一个受约束的优化问题：在$M_j(\theta, \Lambda) \succeq 0, j=0,...p$ 的约束条件下最小化损失函数$\mathcal{L}(f_\theta)$。
通过**log-det障碍函数**将约束条件统一到目标函数中，转换为无约束的优化问题，其目标函数为：
$$
\begin{align*}
\min_{\theta, \Lambda} \mathcal{L}(f_{\theta}) + \mathcal{L}_M(\theta, \Lambda)
\end{align*}
$$
其中$\mathcal{L}_M(\theta, \Lambda) = -\sum_{j=0}^{q} \rho_j \log \det \left( M_j (\theta, \Lambda) \right), \rho_j > 0$。
确保损失函数$\mathcal{L}_M(\theta, \Lambda) \leq 0$可以保证线性矩阵不等式$M_j(\theta, \Lambda) \succeq 0, j=1,...p$成立。

由此可以给出**考虑了Lipschitz连续性约束的子损失函数**
$$
\begin{align*}
{\cal L}_M(\theta, \Lambda, \bar{\Lambda}, \bar{\Lambda}) = 
&-c_{l_1} \log \det(M_1(\theta, \Lambda) )\\
&-c_{l_2} \log \det(M_2(\hat{\theta}, \hat{\Lambda})) \\
&-c_{l_3} \log \det(M_3(\bar{\theta}, \bar{\Lambda}))
\end{align*}
$$
其中$c_{l_i}, i=1,2,3$为各项的权重系数，$M_i,i=1,2,3$为三个Lipschitz常数$L_h, L_{dh},L_{d2h}$对应的半正定矩阵，$\Lambda, \hat{\Lambda}, \bar{\Lambda}$为可训练参数。

#### 满足$L_{max} \tilde{\epsilon} + \psi^* \leq 0$的子损失函数

$$
\begin{align*}
\mathcal{L}_v(\psi) = \max \left( 0, L_{\max} \bar{\epsilon} + \psi \right)
\end{align*}
$$
其中$L_{max}$为$q_k(x), k=1,2,3$的Lipschitz常数的最大值
或：$L_{max} = max(L_h,L_h+L_{dh}L_x+L_{d2h})$
$L_x$为$f(x) + g(x)u(x)$的Lipschitz常数

___
### 训练过程

1. **初始化**：初始化神经网络参数$\theta$、优化变量 $\psi$、Lipschitz 约束参数 $\Lambda, \Lambda', \Lambda''$。设置超参数，包括采样误差 $\bar{\epsilon}$ 、Lipschitz 常数 $L_h, L_{dh}, L_{d2h}$​、损失权重 $\lambda_1, \lambda_2, c_{l1}, c_{l2}, c_{l3}$，以及最大训练轮数。
2. **采样数据**：
    - 从数据集 $\mathcal{S},\mathcal{U},\mathcal{D}$ 中采样点 $x_i$​，确保样本点覆盖初始安全集、不安全集和整个状态空间，满足$\|x - x_i\| \leq \bar{\epsilon}$。
3. **计算 $L_{\text{max}}$​**：
    - 根据公式$L_{\text{max}} = \max(L_h, L_h + L_{dh} L_x + L_{d2h})$，计算 Lipschitz 常数的最大值
4. **训练循环**（当 $\mathcal{L}_\theta > 0$ 或 $\mathcal{L}_M \not\leq 0$ 或 $\mathcal{L}_v > 0$ 时）：
    - **更新 SNCBF**：
        - 使用当前参数 $\theta$ 计算 SNCBF $\widetilde{h}$。
    - **计算控制输入**：
        - 通过解决 SNCBF-QP 问题计算控制输入$$\begin{align*} &\min_{u \in U} \|u - u_{\text{ref}}(x_i)\| \\ &\text{s.t.} \quad \frac{\partial \tilde{h}(x_i | \theta)}{\partial x} (f(x_i) + g(x_i)u) + \frac{1}{2} \text{tr} \left( \sigma^\top \frac{\partial^2 \tilde{h}(x_i | \theta)}{\partial x^2} \sigma \right) \geq -\kappa(\tilde{h}(x_i))\end{align*}$$其中$u_{\text{ref}}$是参考控制输入。
    - **计算损失函数**：
        - 计算 $\mathcal{L}_\theta(\theta) =\mathcal{L}_1 + \lambda_1 \mathcal{L}_2 + \lambda_2 \mathcal{L}_3$​ 
        - 计算 Lipschitz 约束损失  $$\begin{align*} \mathcal{L}_M(\theta, \Lambda, \hat{\Lambda}, \bar{\Lambda}) = &-c_{l1} \log \det(M_1(\theta, \Lambda))\\ &- c_{l2} \log \det(M_2(\hat{\theta}, \hat{\Lambda})) \\ &- c_{l3} \log \det(M_3(\bar{\theta}, \bar{\Lambda})) \end{align*}$$
        - 计算有效性损失  $\mathcal{L}_v(\psi) = \max(0, L_{\text{max}} \bar{\epsilon} + \psi)$
    - **更新参数**：
        - 使用梯度下降（如ADAM）更新参数$\theta, \Lambda, \hat{\Lambda}, \bar{\Lambda}, \psi$ 以最小化 $\mathcal{L}_\theta, \mathcal{L}_M, \mathcal{L}_v$。
    - **数据批处理**：
        - 将数据集 $\mathcal{S, U, D}$ 随机打乱为多个批次，逐批计算损失并更新参数。
5. **终止条件**：
    - 当 $\mathcal{L}_\theta = 0$、$\mathcal{L}_M \leq 0$ 、$\mathcal{L}_v = 0$ 时，训练收敛，表明 SNCBF 满足 SOP 的约束和有效性条件。
    - 或：当达到预定义的最大训练轮数时停止。
---
## 实验

### 倒摆系统

$$
\begin{align*}
d\left[ \begin{array}{c}
\theta \\
\dot{\theta} \end{array} \right] = \left( \left[ \begin{array}{c}
\dot{\theta} \\
\frac{g}{l} \sin(\theta) \end{array} \right] + \left[ \begin{array}{c}
0 \\
\frac{1}{ml^2} \end{array} \right] u \right) dt + \sigma \, dW_t,
\end{align*}

$$
其中$\theta$为摆杆偏离垂直方向的角度，$\dot{\theta}$为角速度，$m = 1kg$，$l = 10m$，$g = 9.8 m/s^2$
$\sigma = diag(0.1,0.1)$，为对角度和角速度的随机扰动

- 状态空间$X = [-\pi/4, -\pi/4]^2$
- 初始安全区域$X_s = [-\pi/15,-\pi/15]^2$
- 初始不安全区域$X_u = X \setminus [-\pi/6,-\pi/6]^2$

$\tilde{\epsilon} = 0.00016$
$\kappa(h) = \gamma h, \gamma = 1$
Lipschitz常数$L_h = 0.01, L_{dh} = 0.4, L_{d2h} = 2, L_{max} = 2.4$
SNCBF中含单隐藏层，20个神经元；激活函数为$Softplus(x) = log(1 + exp(x))$

**优化算法：** 使用ADAM优化器更新参数 $\theta, \Lambda, \hat{\Lambda}, \bar{\Lambda}, \psi$

**收敛结果**：$\psi^* = -0.000042$，满足$L_{\text{max}} \bar{\epsilon} + \psi^* \leq 0$，CBF有效
### 自动驾驶中的障碍避免系统

$$
\begin{align*}
d\begin{bmatrix}
x_1 \\
x_2 \\
\psi
\end{bmatrix} &= 
\left(
\begin{bmatrix}
v \cos \psi \\
v \sin \psi \\
0
\end{bmatrix} + \begin{bmatrix}
0 \\
0 \\
1
\end{bmatrix} u \right)
dt + \sigma dW_t
\end{align*}

$$

其中$x_1,x_2,\psi$为机器人在二维平面上的坐标以及偏航角
$v = 1, \sigma = diag(0.1,0.1,0.1)$

- 状态空间$X = [-2, 2]^3$
- 初始安全区域$X_s = X \setminus [-1.5,1.5]^2 \times [-2,2]$ （排除障碍物的道路区域）
- 初始不安全区域$X_u = [-0.2,0.2]^2 \times [-2,2]$（行人所在的区域）

$\tilde{\epsilon} = 0.01$
$\kappa(h) = \gamma h, \gamma = 1$
Lipschitz常数$L_h = 1, L_{dh} = 1, L_{d2h} = 2, L_{max} = 4$
SNCBF中含单隐藏层，20个神经元；激活函数为$Softplus(x) = log(1 + exp(x))$

**收敛结果**：$\psi^* = -0.004002$，满足$L_{\text{max}} \bar{\epsilon} + \psi^* \leq 0$，CBF有效

**与baseline方法比较**：
baseline方法确保的状态空间安全子集比例为 11.8%，本文方法确保的状态空间安全子集比例高达 77.6%，表现更优。