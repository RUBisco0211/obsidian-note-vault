---
paper: https://arxiv.org/abs/2012.14964v2
code: https://github.com/wecacuee/Bayesian_CBF
home: https://vikasdhiman.info/Bayesian_CBF/
---
## 引言

### 相关研究


### 本文工作

- 开发了一种**矩阵变高斯过程（Matrix Variate Gaussian Process, MVGP）** 回归，以此对未知的系统动力学进行估计；并对任意相对度的系统制定概率安全性和稳定性的约束
- 证明了满足所提出的概率约束的控制策略可以由求解一个确定性的**二阶锥规划（Second Order Cone Programming, SOCP）** 来获得
- 将已有成果拓展为一种**自触发的安全控制设定**，自适应地确定每个控制输入在必须进行重新计算前的持续时间，以此保证高概率安全
- 推导出概率安全性和稳定性约束的均值和方差的封闭式表达式，最高达到相对度2

---
## 相关工作

---
## 问题陈述

考虑一个控制仿射非线性系统
$$
 \dot{x} = f(x) + g(x) u = \begin{bmatrix} f(x) & g(x) \end{bmatrix} \begin{bmatrix} 1 \\ u \end{bmatrix} \triangleq F(x) \underline{u},
$$
其中
$x(t) \in \mathcal{X} \subset \mathbb{R}^n$为系统状态
$u(t) \in \mathcal{U} \subset \mathbb{R}^m$为系统输入
$f$和$g$是局部Lipschitz连续的，且可接受的控制输入集$\mathcal{U}$是凸的

**研究的问题**：在$f$和$g$未知且需要在线估计的情况下，使用$x, u, \dot{x}$的观测值来加强系统的稳定和安全特性

### 符号说明

- 记集合$\mathcal{X}$的边界为$\partial{\mathcal{X}}$
- 令向量$vec(X) \in \mathbb{R}^{mn}$为矩阵$X \in \mathbb{R}^{n \times m}$将列进行堆叠得到的向量化形式
- Kronecker积记作$\otimes$
- 函数$V:\mathbb{R}^n \longrightarrow \mathbb{R}$ 沿$f:\mathbb{R}^n \longrightarrow \mathbb{R}^n$的Lie导数记作$\mathcal{L}_f V :\mathbb{R}^n \longrightarrow \mathbb{R}$
- $r$次连续可微函数$h:\mathcal{X} \longrightarrow \mathbb{R}$的空间记作$\mathbb{C}^r(\mathcal{X},\mathbb{R})$

### 已知动力学情况下的稳定性和安全性

**定义1**：控制李雅普诺夫函数（Control Lyapnov Function, CLF）
若有函数$V \in \mathbb{C}^1(\mathcal{X}, \mathbb{R})$满足正定性，即$V(x) > 0, \forall x \in \mathcal{X}\setminus \{0\}$且$V(0) = 0$，且：
$$
\begin{align*}
\inf_{u \in U} \text{CLC}(x, u) \leq 0, \quad \forall x \in \mathcal{X}
\end{align*}
$$
其中$\text{CLC(x, u) \(\triangleq\)} \mathcal{L}_f V(x) + \mathcal{L}_g V(x) u + \gamma(V(x))$称为控制李雅普诺夫条件，$\gamma(x)$为一类$\kappa$函数
则$V(x)$为该系统的**控制李雅普诺夫函数**

**命题1**：稳定性的充分条件
若上述系统 $\dot{x} = f(x) + g(x) u$ 有一CLF $V(x)$，则任意Lipschitz连续的控制策略$\pi(x) \in \{u \in \mathcal{U}| CLC(x,u ) \leq 0 \}$都能渐进地使系统稳定

---
令  $\mathcal{C} \triangleq \{x \in \mathcal{X} | h(x) \geq 0 \}$ 为系统状态的安全集，$h \in \mathbb{C}^1(\mathcal{X}, \mathbb{R})$，若$\mathcal{C}$为**正向不变**的，该系统在$C$上安全；正向不变：即对任意$x (0) \in \mathcal{C}$，能保持$x(t) \in \mathcal{C}, \forall t \geq 0$

**定义2**：控制障碍函数（Control Barrier Function, CBF）
若有函数$h \in \mathbb{C}^1(X, \mathbb{R})$满足：
$$

\begin{align*}
\sup_{u \in U} \text{CBC}(x, u) \geq 0, \quad \forall x \in \mathcal{X},
\end{align*}

$$
其中$\text{CBC(x, u)} \triangleq \mathcal{L}_f h(x) + \mathcal{L}_g h(x) u + \alpha(h(x))$称为控制障碍条件，$\alpha$为一$\mathcal{K}_\infty$类函数

---
**命题2**：安全性的充分条件
考虑一由$h \in \mathbb{C}^1(\mathcal{X}, \mathbb{R})$隐式定义的安全集$\mathcal{C}$，若$h$为系统的CBF且满足$\nabla h(x) \neq 0, \forall x \in \{ x \in \mathcal{X} | h(x) = 0 \}$，则任意Lipschitz连续的控制策略$\pi(x) \in \{u \in \mathcal{U} | CBC(x,u) \geq 0 \}$ 都能使系统关于安全集$\mathcal{C}$安全

---
命题1、2提供了控制策略$\pi(x)$为了确保系统稳定性和安全性需要满足的充分条件。
上述条件是由$u$中的仿射约束定义的，因此可以使用二次规划（QP）的形式来确定
$$
\begin{align*}
\pi(x) \in & \arg\min_{u \in \mathcal{U}, \delta \in \mathbb{R}} \| \mathbf{R}(\mathbf{x}) \mathbf{u} \|^2 + \lambda \delta^2 \\

s.t. 
&  \space {\text{CLC}(x, u)} \leq \delta, \text{CBC(x, u)} \geq 0,
\end{align*}
$$
其中$\mathbf{R}(x) \in \mathbb{R}^{m \times m}$为一惩罚控制努力的矩阵，$\delta$为一松弛变量，通过$\lambda$缩放因子来控制优先考虑安全性
若已经存在一个稳定性策略$\hat\pi(x)$，可以修改为以下优化问题以确保安全性
$$
\begin{align*}
\pi(x) \in \arg \min _{u \in \mathcal{U}}\quad \| \mathbf{R}(x) \left( u - \hat{\pi}(x) \right) \|^2 \\
\text{s.t.} \quad \mathbf{CBC}(x, u) \geq 0.
\end{align*}
$$
实践中，上述QP无法快速求解

---
### 未知动力学情况下的稳定性和安全性（重要）

对于上述控制仿射非线性系统
$$
 \dot{x} = f(x) + g(x) u = \begin{bmatrix} f(x) & g(x) \end{bmatrix} \begin{bmatrix} 1 \\ u \end{bmatrix} \triangleq F(x) \underline{u},
$$
在$F(x) \in \mathbb{R}^{n \times (1+m)}$未知时，使用**高斯过程（Guassian Process, GP）** 对$vec(F(x))$设置先验分布，均值函数为$vec(M_0(x))$，协方差函数为$K_{0}(x, x')$

**目标**：使用系统状态和控制输入在时间上的观测值计算$vec(F(x))$的的后验分布，以此来估计系统的动力学模型；并且使用估得的动力学模型确保系统的稳定性和安全性，尽管可能存在估计误差

#### 问题1
给定未知系统动力学的向量化形式$vec(F(x))$的先验分布
$$
vec(F(x)) \sim \mathcal{GP} ( vec(M_0(x)), K_0(x, x'))
$$
并使用给出的训练集
$$
\begin{align*}
\mathbf{X}_{1:k} & \triangleq [x(t_1),...,x(t_k)] \\
\mathbf{U}_{1:k} & \triangleq [u(t_1),...,u(t_k)] \\
\mathbf{\dot{X}}_{1:k} & \triangleq [\dot{x}(t_1),...,\dot{x}(t_k)] \\
\end{align*}
$$
来计算其后验分布 $\mathcal{GP} ( vec(M_k(x)), K_k(x, x'))$

#### 问题2
给定系统的安全集$\mathcal{C} \triangleq \{ x \in \mathcal{X} \mid h(x) \geq 0 \}$，初始状态$x_k \triangleq x(t_k)$以及$t_k$时$vec(F(x))$的分布 $\mathcal{GP} ( vec(M_k(x)), K_k(x, x'))$。选择一个控制输入$u_k$以及触发周期$\tau_k$使得对于$u(t) \triangleq u_k$，有
$$
\mathbb{P}(CBC(x(t),u_k) \geq 0) \geq p_k,  \forall t \in [t_k, t_k + \tau_k]
$$
其中$p_k \in (0,1)$为用户指定的**风险容忍度**
通过解决问题2可以满足未知动力学系统上的高概率安全要求

---
## 系统动力学的矩阵变量高斯过程回归（MVGP）

本节旨在用提出的MVGP解决问题1。即通过高斯过程，在给定的训练集上估计$f(x)$和$g(x)$

两种已有的思路：
- 最简单的方法：为$F(x)$上的$n(1+m)$个元素都进行解耦的GP；但忽视了$f(x)$和$g(x)$各分量之间的相关性
- 将$F(x)$列向量化之后，使用单一的GP进行建模；虽然能捕捉相关性，但核函数的设计和超参数优化困难
	- 核心区域化高斯过程（Coregionalization GP, CoGP）模型提供了设计核函数的方法：将核函数分解为$K_0(x, x') \triangleq \sum_{\kappa_0}(x,x')$，但需要大量训练数据；且其后验中不保留“矩阵-时间-标量-核”结构，使其无法使用新数据进行增量学习

**本文方法**：受矩阵变量高斯分布（Matrix Variate Gaussian distribution）的启发，提出了一种核函数（协方差函数）$K_0(x, x')$的替代分解方法，优势在于能建模$F(x)$元素之间的相关性，在后验GP中保持结构，并具有和解耦的GP方法相似的训练与测试复杂度

**定义3**：矩阵变量高斯（MVG）分布
一种描述随机矩阵$X \in \mathbb{R}^{n \times m}$的三参数分布，记作$\mathcal{MN}(M,A,B)$
其中
- $M \in \mathbb{R}^{n \times m}$为均值矩阵
- $A \in \mathbb{R}^{n \times n}$是协方差矩阵，在一定比例下，矩阵 $X$ 的**每一列**都具有此协方差 
- $B \in \mathbb{R}^{m \times m}$是协方差矩阵，在一定比例下，矩阵 $X$ 的**每一行**都具有此协方差 
其概率密度函数为
$$
\begin{align*}
p(X; M, A, B) \triangleq \frac{exp(-\frac{1}{2}tr [B^{-1}(X - M)^\tau A^{-1}(X - M)])}{(2\pi)^{nm/2} \det(A)^{m/2} \det(B)^{n/2}}
\end{align*}
$$

**重要性质**：若$X \sim \mathcal{MN}(M, A, B)$，则$vec(X) \sim \mathcal{N}(vec(M), B \otimes A)$

基于此性质，提出以下**高斯过程的先验分解**：
$$
 \begin{align*} 
vec(F(x)) \sim \mathcal{GP}(vec(M_0({x})), B_0(x, x') \otimes A)
\end{align*}
$$
其中$B_0(x,x') \in \mathbb{R}^{(m+1) \times (m+1)}$，$A \in \mathbb{R}^{n \times n}$
称具有该定义的核结构的此类过程为MVGP


假设噪声协方差矩阵满足$S = \sigma^2A$，该假设是保证GP后验协方差中的Kronecker积结构的必要条件

令$\mathcal{B}^{1:k}_{1:k} \in \mathbb{R}^{k(m+1) \times k(m+1)}$为一个拥有$k \times k$个块元素的矩阵，每个块元素为$[\mathcal{B}^{1:k}_{1:k}]_{i,j} \triangleq B_0(x_i,x_j) \in \mathbb{R}^{(m+1) \times (m+1)}$
定义
- $\mathcal{M}_{1:k} \triangleq [M_0(x_1),...,M_0(x_k)] \in \mathbb{R}^{n \times k(m+1)}$
- $\mathcal{B}_{1:k}(x) \triangleq [B_0(x, x_1), \ldots, B_0(x, x_k)] \in \mathbb{R}^{(m+1) \times k(m+1)}$
- $\underline{\mathcal{U}}_{1:k} \triangleq \text{diag}(\underline{u}_1, \ldots, \underline{u}_k) \in \mathbb{R}^{k(m+1) \times k}$
考虑一个任意的测试状态$x_*$，则训练和测试数据的联合高斯：
$$
 \begin{align*}
 \begin{bmatrix}
 \text{vec}(\dot{X}_{1:k}) \\ 
 \text{vec}(F(x_*))
 \end{bmatrix}
  \sim
   \mathcal{N} \left( 
   \begin{bmatrix} \text{vec}(\mathcal{M}_{1:k} \underline{\mathcal{U}}_{1:k}) \\ \text{vec}(M_0(x_*)) \end{bmatrix}, 
   \begin{bmatrix}
\underline{\mathcal{U}}_{1:k}^{\top} \mathcal{B}_{1:k}^{1:k} \mathcal{U}_{1:k} + \sigma^2 I_k & \underline{\mathcal{U}}_{1:k}^{\top} \mathcal{B}_{1:k}^{\top}(x_*) \\
\mathcal{B}_{1:k}(x_*) \underline{\mathcal{U}}_{1:k} & \mathcal{B}_0(x_*, x_*)
\end{bmatrix} \otimes A \right),
\end{align*}
$$
其中
$$
\underline{\mathcal{U}}_{1:k}^{\top} \mathcal{B}_{1:k}^{1:k} \underline{\mathcal{U}}_{1:k} = 
\begin{bmatrix}
\underline{u}_{1}^{\top} B_{0}(x_{1}, x_{1}) \underline{u}_{1} & \cdots & \underline{u}_{1}^{\top} B_{0}(x_{1}, x_{k}) \underline{u}_{k} \\
\vdots & \ddots & \vdots \\
\underline{u}_{k}^{\top} B_{0}(x_{k}, x_{1}) \underline{u}_{1} & \cdots & \underline{u}_{k}^{\top} B_{0}(x_{k}, x_{k}) \underline{u}_{k}
\end{bmatrix}
$$
**命题3**：
给定训练数据$(X_{1:k},U_{1:k},\dot{X}_{1:k})$ ，$vec(F(x))$的后验分布仍然为一GP：
$$
\text{vec}(F(x)) \sim \mathcal{GP}(\text{vec}(M_k​(x)),B_k​(x,x′) \otimes A)
$$
其中均值和协方差为
$$
\begin{align*}
M_k(x) &\triangleq M_0(x) + \left(\dot{X}_{1:k} - \mathcal{M}_{1:k} \underline{\mathcal{U}}_{1:k} \right)(\underline{\mathcal{U}}_{1:k} \mathcal{B}_{1:k}(x))^\dagger \\ 
B_k(x, x') &\triangleq B_0(x, x') - \mathcal{B}_{1:k}(x) \underline{\mathcal{U}}_{1:k} (\underline{\mathcal{U}}_{1:k} \mathcal{B}_{1:k}(x'))^\dagger \\ 
(\underline{\mathcal{U}}_{1:k} \mathcal{B}_{1:k}(x))^{\dagger} &\triangleq \left( \underline{\mathcal{U}}_{1:k}^{\top} \mathcal{B}_{1:k}^{1:k} \underline{\mathcal{U}}_{1:k} + \sigma^2 \textbf{I}_k \right)^{-1} \underline{\mathcal{U}}_{1:k}^{\top} \mathcal{B}_{1:k}^{\top}(x)
\end{align*}
$$
又由**引理8**：
若$\mathbf{X} \sim \mathcal{MN}(\mathbf{M},\mathbf{A},\mathbf{B})$，则有
$$
\mathbf{CX} \sim \mathcal{MN}(\mathbf{CM},\mathbf{CAC^\top,\mathbf{B}}),\mathbf{XD} \sim \mathcal{MN}(\mathbf{MD},\mathbf{A},\mathbf{D^\top BD})
$$
可得，对于一个给定的控制输入$u$，$F(x) \underline{u}$的**MVGP后验分布**如下：
$$
F(x) \underline{u} \sim \mathcal{GP}(M_k(x) \underline{u}, \underline{u}^{\top} B_k(x, x') \underline{u} \otimes A)
$$

**MVGP的优势**
- **后验分布仍然保持核分解结构**，不像CoGP那样在后验更新时破坏核函数的结构，在逐步加入新数据（在线学习）时可以继续保持高效计算
- **参数更少**：如果核分解为 $B_0(x,x') = B \, \kappa_0(x,x')$ 那么除了核参数以外，MVGP 只需要学习一个小矩阵 $B \in \mathbb{R}^{(1+m)\times(1+m)}$以及噪声相关的参数（如 $A$、$\sigma$），而 CoGP 则需要学习 $(1+m)^2 n^2$ 个参数，规模大得多。
- **计算复杂度更低**：MVGP 训练只需反转一个 $k \times k$ 矩阵，复杂度 $O(k^3)$；CoGP 需要反转 $kn \times kn$矩阵，复杂度 $O(k^3 n^3)$
---
## 具有概率安全约束的自触发控制

本节旨在解决问题2，专注于处理形式为 $\mathbb{P}(CBC(x,u) \geq 0) \geq p$ 的概率安全性约束，开发了自触发条件，以自适应地决定系统输入$u$需要重新计算以确保系统高概率安全的时间$t_k,t_{k+1},...$

前提条件：需要保证$F(x) \underline{u}$是Lipschitz连续的，有以下引理：
**引理1**：令
$$
\begin{align*}
f(x) = F(x) u \sim \mathcal{GP}(\mu(x), A \kappa_B(x, x')),
\end{align*}
$$
其中$\mathbf{\mu}(x) = M_k(x) u, \quad \kappa_B(x, x') = u^\top B_k(x, x') u.$
若核函数$\kappa_B(x, x')$足够光滑（有四阶连续偏导），则有：
- $f(x)$的Jacobian矩阵元素在至少$1-\delta_L / n^2$的概率下有界$$ \begin{align*} 
\bigg| \sup_{x \in \mathcal{X}} \frac{\partial f_i(x)}{\partial x_j} \bigg| \leq L_{i,\partial{j}}, \quad \forall i,j \in \{1, \ldots, n\},
\end{align*}$$
- 整体在至少$1-\delta_L$的概率下Lipschitz连续$$
\begin{align*}
\|f(x) - f(x') \| \leq L_f \|x - x' \|, \quad \forall x, x' \in \mathbb{X}.
\end{align*}
$$
---
### 概率性的安全约束

本节目标是把安全约束条件写成**概率形式**，并进一步转化为**凸优化约束**

考虑上文提及的CLF-CBF QP中由$F(x) \underline{u}$在时间$t_k$上的MVGP分布引入的概率稳定性和安全性约束
$$
\begin{align*}
\pi(x_k) \in \arg \min_{u_k \in \mathcal{U}, \delta \in \mathbb{R}} &\| \mathbf{R}(x_k) u_k \|^2 + \lambda \delta^2 \\

s.t. \mathbb{P}(CLC(x_k, u_k)) &\leq \delta | x_k, u_k \rangle \geq \tilde{p}_k \\

\mathbb{P}(CBC(x_k, u_k)) &\geq \zeta | x_k, u_k \rangle \geq \tilde{p}_k,

\end{align*}
$$
其中$\tilde{p_k} \triangleq p_k / (1 - \delta_L)$，$\zeta > 0$为安全裕度

**引理2**：对前文提及的系统动力学以及相应的MVGP后验分布，$CBC_k = CBC(x_k,u_k) = \nabla_x h(x_k)^\top F(x_k)\underline{u}_k + \alpha(h(x_k))$
则$CBC_k$的期望和方差为
$$
\begin{align*}
\mathbb{E}[CBC_k] &= \nabla_x h(x_k)^{\top} \mathbf{M}_k(x_k) \underline{u}_k + \alpha(h(x_k)),\\
Var[CBC_k] &= (\underline{u}_k^{\top} \mathbf{B}_k(x_k, x_k) \underline{u}_k) (\nabla_x h(x_k)^{\top} \mathbf{A} \nabla_x h(x_k)).
\end{align*}
$$
又由$CBC_k \sim \mathcal{N}(E[CBC_k], \text{Var}[CBC_k])$，原安全约束可写为：
$$
\mathbb{P}(CBC_k \geq \zeta \mid x_k, u_k) = 1 - \Phi \left( \frac{\zeta - \mathbb{E}[ CBC_k]}{\sqrt{Var[CBC_k]}} \right) \geq \tilde{p}_k
$$

其中$\Phi$为标准正态的累积分布函数。由此，原优化可以写为
$$
\begin{align*}
\pi(x_k) \in \arg \min_{u_k \in \mathcal{U}, \delta \in \mathbb{R}} &\| \mathbf{R}(x_k) \mathbf{u}_k \|^2 + \lambda \delta^2 \\

s.t. \delta - \mathbb{E}[ \text{CLC}_k] &\geq c(\widehat{p}_k) \sqrt{{Var}[ \text{CLC}_k]} \\
\zeta - \mathbb{E}[ \text{CBC}_k] &\leq -c(\widehat{p}_k) \sqrt{{Var}[ \text{CBC}_k]},
\end{align*}
$$
其中$c(\tilde{p_k}) \triangleq \sqrt{2} \text{erf}^{-1}(2 \tilde{p_k} - 1)$

则原CLF-CBF约束可以转化为一个**二阶锥规划问题**，如下
$$

\begin{align*}
\pi(x) & \in \underset{y \in \mathbb{R}, \delta \in \mathbb{R}, u \in \mathcal{U}}{\arg \min}   y \\
s.t. &\quad y - \left\| \mathbf{R} (x_k)u + \sqrt{\lambda} \delta \right\|_2 \geq 0 \\ 
& \textbf{q}_k (x_k)^{\top} \underline{u} - \delta + c(\hat{p}_k) \left\| \textbf{P}_k(x_k) \underline{u} \right\|_2 \leq 0 \\ 
& \textbf{e}_k(x_k)^\top \underline{u} - \zeta - c(\widehat{p}_k) \left\| \mathbf{V}_k(x_k) \underline{u} \right\|_2 \geq 0,
\end{align*}
$$
其中
$$
\begin{align*}
\textbf{q}_k(x_k) &\triangleq M_k(x_k)^T \nabla_x V(x_k) + \begin{bmatrix}
\gamma(V(x_k)) \\
0_m
\end{bmatrix}, \\

\textbf{P}_k(x_k) &\triangleq \sqrt{\nabla_x V(x_k)^\top \mathbf{A} \nabla_x V(x_k) }\mathbf{B}_k^{\frac{1}{2}}(x_k, x_k), \\

\textbf{e}_k(x_k) &\triangleq \textbf{M}_k(x_k)^\top \nabla_x h(x_k) + \begin{bmatrix}
\alpha(h(x_k)) \\
0_m
\end{bmatrix}, \\ 

\textbf{V}_k(x_k) &\triangleq \sqrt{\nabla_x h(x_k)^\top \mathbf{A} \nabla_x h(x_k) }\mathbf{B}_k^{\frac{1}{2}} (x_k, x_k),
\end{align*}
$$
且$\textbf{B}^{\frac{1}{2}}(x_k,x_k)$为$\textbf{B}_k(x_k,x_k)$的**Cholesky因子**

---
### 自触发设计

上节确保了系统在$t_k$时大概率满足安全约束。本节任务是设计一个自触发策略，将$t_k$上的约束推广到$t_k$后的一段时间内，以确保系统在需要重新计算控制输入之前的时间区间（即触发间隔）$[t_k,t_k + \tau_k)$内都大概率满足安全约束

**引理3**：
若系统满足前文引理1中的前提条件，即$F(x) \underline{u}$为Lipschitz连续的
在时间$t_k$时，系统状态为$x_k$，状态导数为$\dot{x}_k$，则在任意$s \in [0, \tau_k)$，系统状态的总变化量以至少$1 - \delta_L$的概率有界：
$$
\| x(t_k + s) - x_k \| \leq \bar{r}_k(s) \triangleq \frac{1}{L_{f_k}} \|\dot{x}_k \|(e^{s L_{f_k}} - 1)
$$
由于$CBC$中$h$为一连续可微函数，因此由以上引理可得，对于任意触发间隔时间$\tau_k$，存在一个常数$L_{h_k}$满足：
$$
\mathop{\sup }\limits_{{s \in  \left\lbrack  {0,{\tau }_{k}}\right) }}\begin{Vmatrix}{{\nabla }_{\mathbf{x}}h\left( {\mathbf{x}\left( {{t}_{k} + s}\right) }\right) }\end{Vmatrix} \leq  {L}_{{h}_{k}}.
$$

**命题5**：
考虑满足前文所提约束的系统，其安全集为$\mathcal{C}$
假设：
- $F(x) \underline{u}$以至少为$1 - \delta_L$的概率为Lipschitz连续的，且其Lipschitz常数为$L_{f_k}$
- $CBC$约束在时间$t_k$时以及安全裕度$\zeta > 0$的情况下以至少$\tilde{p}_k = p_k /(1 - \delta_L)$的概率安全
- $\alpha$的Lipschitz常数为$L_\alpha$
则存在一个最大的触发间隔$\tau_k$使得系统在整个时间区间$[t_k,t_k + \tau_k)$上，$CBC$以至少$p_k$的概率安全，$\tau_k$满足：
$$
{\tau }_{k} \leq  \frac{1}{{L}_{f_k}}\ln \left( {1 + \frac{{L}_{f_k}\zeta }{\left( {{L}_{f_k} + {L}_{\alpha }}\right) {L}_{h_k}\begin{Vmatrix}{\dot{x}}_{k}\end{Vmatrix}}}\right) 
$$

证明：由于原约束在时间$t_k$时有解，故$\mathbb{P}(CBC_k \geq \zeta | x_k,u_k) \geq \tilde{p}_k$。因此在$CBC_k \geq \zeta$以及系统动力学$F(x) \underline{u}$为Lipschitz连续的条件下，若能证明对于任意$s \in [0,\tau_k)$，都有$CBC(s + t_k) \geq 0$，则结论成立。

由于$\nabla h(x), \alpha(h(x))$都为Lipschitz连续，故$CBC$的Lipschitz常数为$(L_f + L_\alpha)L_h$
故结合引理3，在时间$t \in [t_k, t_k + \tau_k)$时有
$$
\begin{align*}
|CBC(x(t), u_k) - CBC_k | &\leq (L_f + L_\alpha)L_h \cdot \| x(t) - x_k\| \\
& \leq  (L_f + L_\alpha)L_h \cdot \frac{1}{L_{f}} \|\dot{x}_k \|(e^{s L_{f}} - 1)
\end{align*}
$$
由于在$t_k$时$CBC_k \geq \zeta$，要保证整个区间内$CBC(x(t), u_k) \geq 0$，只要确保$CBC$的最小值大于0，即
$$
\zeta \geq (L_f + L_\alpha)L_h \cdot \frac{1}{L_{f}} \|\dot{x}_k \|(e^{\tau_k L_{f}} - 1)
$$
解得
$$
{\tau }_{k} \leq  \frac{1}{{L}_{f_k}}\ln \left( {1 + \frac{{L}_{f_k}\zeta }{\left( {{L}_{f_k} + {L}_{\alpha }}\right) {L}_{h_k}\begin{Vmatrix}{\dot{x}}_{k}\end{Vmatrix}}}\right) 
$$

由此，通过确保$\mathbb{P}(CBC(x) \geq 0) \geq p_k$确保了$\mathbb{P}(h(x) \geq 0) \geq p_k, \forall t \in [t_k,t_k + \tau_k)$，并得到了为确保系统大概率安全的两次重新计算控制输入的最大间隔$\tau_k$

---
## 扩展到更高相对度的系统

**相对度$r$**：控制输入$u$第一次出现在安全函数$h(x)$的第几阶导数里
前文中$\dot{h}(x,u) = L_fh(x) + L_gh(x)u$，其中假设$L_gh(x) \neq 0$，故相对度为1

在相对度大于1的系统中，往往需要使用高阶CBF（HOCBF）。本文考虑指数型CBF（ECBF）

### 在已知动力学系统中使用ECBF

**定义4**：ECBF
函数$h \in \mathbb{C}^r(\mathcal{X},\mathbb{R})$为系统$\dot{x} = f(x) + g(x)u$上一个CBF
若存在$k_\alpha \in \mathbb{R}^r$使得r阶的控制障碍条件$CBC^{(r)}(x,u) \triangleq \mathcal{L}_f^{(r)}h(x) + \mathcal{L}_g \mathcal{L}_f^{(r-1)}h(x)u + k_\alpha^\top \eta(x)$满足
$$
\mathop{\sup }\limits_{{\mathbf{u} \in  \mathcal{U}}}{\operatorname{CBC}}^{\left( r\right) }\left( {\mathbf{x},\mathbf{u}}\right)  \geq 0, \forall x \in \mathcal{X}
$$
则$h$为一个ECBF

若存在$k_\alpha$满足上述要求，则任意确保$CBC^{(r)}(x,u) \geq 0$的控制器$u = \pi(x)$都能确保系统在安全集$\mathcal{C}$上安全

---
### 概率性的ECBF约束

本节目标在于解决安全约束下的优化问题（为表达清晰暂时忽略对CLC的约束）
$$
\begin{align*}
\pi \left( {\mathbf{x}}_{k}\right)  \in  \underset{{\mathbf{u}}_{k} \in  \mathcal{U}}{\arg \min }{\begin{Vmatrix}\mathbf{R}\left( {\mathbf{x}}_{k}\right) {\mathbf{u}}_{k}\end{Vmatrix}}_{2} \\
s.t.  \mathbb{P}\left( {{\mathrm{{CBC}}}_{k}^{\left( r\right) } \geq  \zeta  \mid  {\mathbf{x}}_{k},{\mathbf{u}}_{k}}\right)  \geq  {\widetilde{p}}_{k}
\end{align*}
$$
相对度为$r$的系统的安全约束$CBC^{(r)}_k$的分布无法显示表示，因此使用集中不等式将约束重写为$CBC^{(r)}_k$的**矩**的形式。
首先需要证明$CBC_k^{(r)}$的均值和方差分别为$u$的仿射和二次函数

**引理4**：
令$r \geq 1$为系统相对于$h(x)$的相对度，$F(x)$为具有有限均值和方差的随机函数
则，$CBC^{(r)}(x,u)$的期望和方差分别为$u$的线性和二次函数，并满足：
$$
\begin{align*}
\mathbb{R}[CBC^{(r)}(x,u)] &= e^{(r)}(x)^\top \underline{u} \\
Var[CBC^{(r)}(x,u)] &= \underline{u}^\top V^{(r)}(x) V^{(r)}(x)^\top \underline{u}
\end{align*}
$$
其中
$$
\begin{align*}
e^{(r)}(x) &\triangleq \mathbb{E} \left[ F(x)^\top \nabla_x \mathcal{L}_f^{(r-1)} h(x) + \begin{bmatrix} \mathbf{k}_\alpha^\top \eta(x) \\ 0_m \end{bmatrix} \right] \\
V^{(r)}(x) &\triangleq Var \left[ F(x)^\top \nabla_x \mathcal{L}_f^{(r-1)} h(x) + \begin{bmatrix} \mathbf{k}_\alpha^\top \eta(x) \\ 0_m \end{bmatrix} \right]^{\frac{1}{2}}
\end{align*}
$$

**命题6**：将相对度为1的系统上对于概率性安全约束的的SoCP问题扩展到相对度大于1的问题上
考虑带随机动力学因素且相对度大于1的控制仿射系统，对于ECBF$h(x) \in \mathbb{C}^{(r)}(\mathcal{X}, \mathbb{R})$
若连续时间$t_{k}$时的控制输入由以下SoCP确定，则系统轨迹仍然以$\tilde{p}_{k}$的概率保留在安全集$\mathcal{C}$中
$$
\begin{align*}
\pi\left(\mathbf{x}_k\right)& \in \underset{y \in \mathbb{R}, \mathbf{u}_k \in \mathcal{U}}{\arg \min } y \\ 
\text { s.t. } \quad &y-\left\|\mathbf{R}\left(\mathbf{x}_k\right) \mathbf{u}_k\right\|_2 \geq 0 \\ 
\quad &\mathbf{e}_k^{(r)}\left(\mathbf{x}_k\right)^{\top} \underline{\mathbf{u}}_k-\zeta-c^{(r)}\left(\tilde{p}_k\right)\left\|\mathbf{V}_k^{(r)}\left(\mathbf{x}_k\right) \underline{\mathbf{u}}_k\right\|_2 \geq 0
\end{align*}
$$
其中$c^{(r)}\left(\tilde{p}_k\right) \triangleq \sqrt{\frac{\tilde{p}_k}{1-\tilde{p}_k}}$且$\tilde{p}_k \in(0,1)$对于任意$t_{k}$成立

---
### 自触发设计

暂时引入一个修正后的CBF$h_b(x) \triangleq h(x) - \zeta_{b}$其中$\zeta_{b} > 0$
用$\zeta = 0$解决命题6中的SoCP，但用$h_b(x)$代替$h(x)$
在采样时间点$\tau_{k}$对$h_{b}(x)$强制执行约束，确保以至少$\tilde{p_{k}}$的概率满足$h(x_{k}) \geq \zeta_{b}$

**命题7**：考虑有随机动力学以及安全集$\mathcal{C}$
假设系统关于$h(x)$的相对度$r \geq 1$，且命题6中的SoCP在采样时间$t_{k}$时有解，$h_{b}(x)$作为CBF且$\zeta = 0$
假设系统动力学以至少$1 - \delta_{L}$的概率为Lipschitz连续的
$h$的Lipschitz常数为$L_{h_{k}}$
则在时间段$[t_{k}, t_{k} + \tau_{k})$内以至少$p_{k} = \tilde{p_{k}}(1 - \delta_{L})$的概率满足：
$$h(x(t)) \geq 0, \forall t \in [t_{k}, t_{k} + \tau_{k})$$
其中${\tau }_{k} \leq  \frac{1}{{L}_{{\mathbf{f}}_{k}}}\ln \left( {1 + \frac{{L}_{{\mathbf{f}}_{k}}{\zeta }_{b}}{{L}_{{h}_{k}}\begin{Vmatrix}{\dot{\mathbf{x}}}_{k}\end{Vmatrix}}}\right) .$

---
## 相对度为2的特殊情况

本部分为计算相对度为2的安全条件的均值和方差提供了一种高效方法：
$$\mathrm{CBC}^{(2)}(\mathbf{x}, \mathbf{u})=\left[\nabla_{\mathbf{x}} \mathcal{L}_f h(\mathbf{x})\right]^{\top} F(\mathbf{x}) \underline{\mathbf{u}}+\left[h(\mathbf{x}), \mathcal{L}_f h(\mathbf{x})\right]^{\top} \mathbf{k}_\alpha$$
$\text{CBC}^{(2)}(x,u)$为一随机过程，其分布由高斯过程$\text{vec}(F(x))$引入
其中各部分的计算图如下，包括点乘以及取梯度两种操作：
![PixPin_2025-09-13_19-02-01.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/paper/1757761333066_PixPin_2025-09-13_19-02-01.png)

**引理5**：通过高斯随机向量$\mathbf{x},\mathbf{y},\mathbf{z}$的均值$\bar{\mathbf{x}},\bar{\mathbf{y}},\bar{\mathbf{z}}$和方差$Var[\mathbf{x}],Var[\mathbf{y}],Var[\mathbf{z}]$以及两两协方差$\text{cov}(\mathbf{x},\mathbf{y}),\text{cov}(\mathbf{x},\mathbf{y}),\text{cov}(\mathbf{x},\mathbf{y})$求$\mathbf{x}^\top \mathbf{y}$均值、方差以及$\mathbf{x},\mathbf{y},\mathbf{z}$与其的协方差的方法
$$\begin{gathered}
\mathbb{E}[\mathbf{x}^{\top}\mathbf{y}]=\bar{\mathbf{x}}^{\top}\bar{\mathbf{y}}+\frac{1}{2}\operatorname{tr}(\operatorname{cov}(\mathbf{x},\mathbf{y})+\operatorname{cov}(\mathbf{y},\mathbf{x})) \\
Var[\mathbf{x}^\top\mathbf{y}]=\frac{1}{2}\mathbf{tr}(\mathrm{cov}(\mathbf{x},\mathbf{y})+\mathrm{cov}(\mathbf{y},\mathbf{x}))^2+\bar{\mathbf{y}}^\top Var[\mathbf{x}]\bar{\mathbf{y}} \\
+\bar{\mathbf{x}}^\top Var[\mathbf{y}]\bar{\mathbf{x}}+\bar{\mathbf{y}}^\top\mathrm{cov}(\mathbf{x},\mathbf{y})\bar{\mathbf{x}}+\bar{\mathbf{x}}^\top\mathrm{cov}(\mathbf{y},\mathbf{x})\bar{\mathbf{y}} \\
\begin{bmatrix}
\operatorname{cov}(\mathbf{x},\mathbf{x}^\top\mathbf{y}) \\
\operatorname{cov}(\mathbf{y},\mathbf{x}^\top\mathbf{y}) \\
\operatorname{cov}(\mathbf{z},\mathbf{x}^\top\mathbf{y})
\end{bmatrix}=
\begin{bmatrix}
\operatorname{cov}(\mathbf{x},\mathbf{y})\bar{\mathbf{x}}+Var[\mathbf{x}]\bar{\mathbf{y}} \\
Var[\mathbf{y}]\bar{\mathbf{x}}+\operatorname{cov}(\mathbf{y},\mathbf{x})\bar{\mathbf{y}} \\
\operatorname{cov}(\mathbf{z},\mathbf{y})\bar{\mathbf{x}}+\operatorname{cov}(\mathbf{z},\mathbf{x})\bar{\mathbf{y}}
\end{bmatrix}.
\end{gathered}$$

**引理6**：令$q(\mathbf{x})$为一标量高斯过程，$\mu(\mathbf{x}): \mathbb{R}^n \rightarrow \mathbb{R}$为其可微的均值函数，$\kappa(\mathbf{x}， \mathbf{x'}): \mathbb{R}^n \times \mathbb{R}^n \rightarrow \mathbb{R}$为二阶可微的协方差函数（核函数）
若$\nabla_{x} \mu(\mathbf{x})$对所有$\mathbf{x} \in \mathbb{R}$存在且有限，且核函数的海森矩阵$\mathcal{H}_{\mathbf{x},\mathbf{x'}}\kappa(\mathbf{x},\mathbf{x'}) = \left [\frac{\partial^2 \kappa(\mathbf{x},\mathbf{x'})}{\partial \mathbf{x_{i}},\partial \mathbf{x'}_{j}} \right]^{n,n}_{i=1,j=1}$对任意$(\mathbf{x},\mathbf{x'}) \in \mathbb{R}^{2n}$存在且有限
则该GP存在一个均方导数$\nabla_{x} q(\mathbf{x})$且其也为一高斯过程
$$
\mathcal{GP} (\nabla_{\mathbf{x}}q(\mathbf{x}),\mathcal{H}_{\mathbf{x},\mathbf{x'}} \kappa(\mathbf{x},\mathbf{x'}))
$$
若$s$为另一随机过程，其与$q$的有限协方差为$\text{cov}_{q,s}(\mathbf{x},\mathbf{x'})$，则
$$
\text{cov}(\nabla_{\mathbf{x}}q(\mathbf{x}),s(\mathbf{x'})) = \nabla_{\mathbf{x}} \text{cov}_{q,s}(\mathbf{x},\mathbf{x'})
$$
$\text{CBC}^{(2)}(\mathbf{x},\mathbf{u})$的均值和方差可根据**引理4**计算，算法如下：

**目标**：$\mathbb{E}[\text{CBC}^{(2)}(\mathbf{x},\mathbf{u})]$以及$Var[\text{CBC}^{(2)}(\mathbf{x},\mathbf{u})]$
**已有数据**：ECBF $h(\mathbf{x})$，系统动力学的分布 $\text{vec}(F(\mathbf{x}) \sim \mathcal{GP}(\text{vec}(\mathbf{M}_{k}(\mathbf{x})),\mathbf{B}_{k}(\mathbf{x},\mathbf{x'}) \otimes \mathbf{A})$
**步骤**：
1. 将$\underline{\mathbf{u}} = [1,\mathbf{0}_{m}^\top]^\top$代入**引理2**计算$$\mathbb{E}[\mathcal{L}_{f}h(\mathbf{x})] = \nabla_{\mathbf{x}}h(\mathbf{x})^\top \mathbf{M}_{k}(\mathbf{x})[1,\mathbf{0}_{m}^\top]$$$$Var[\mathcal{L}_{f}h(\mathbf{x})] = \left[\mathbf{B}_k\left(\mathbf{x}, \mathbf{x}^{\prime}\right)\right]_{1,1}\left(\nabla_{\mathbf{x}} h(\mathbf{x})^{\top} \mathbf{A} \nabla_{\mathbf{x}} h(\mathbf{x})\right) $$
2. 

