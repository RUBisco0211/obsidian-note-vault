## 最优策略

### 策略的比较

（使用状态值比较）

若 $v_{\pi_1}(s) \geq v_{\pi_2}(s), \forall s \in \mathcal{S}$ 则认为策略$\pi_1$优于$\pi_2$
### 定义

若 $v_{\pi^*}(s) \geq v_\pi(s), \forall s \in \mathcal{S}$ 对于其他任意策略$\pi$都成立，则认为$\pi^*$为最优策略

> [!question] 问题
> - 是否存在？
> - 是否唯一？
> - 是随机还是确定？
> - 如何求取？

---
## 贝尔曼最优公式 BOE

实际为一最优化问题
### 元素形式
$$
\begin{align*}
v(s) &= \max_\pi \sum_a \pi(a \mid s)\left(\sum_r p(r \mid s, a) r+\gamma \sum_{s^{\prime}} p\left(s^{\prime} \mid s, a\right) v\left(s^{\prime}\right)\right), \quad \forall s \in \mathcal{S} \\
&= \max_\pi \sum_a \pi(a \mid s) q(s,a)
\end{align*}


$$
### 向量矩阵形式

$$
v=\max _\pi\left(r_\pi+\gamma P_\pi v\right)
$$
其中$r_\pi \in \mathbb{R}^n$，$P_{\pi} \in \mathbb{R}^{n \times n}$
$$
\begin{aligned}
& {\left[r_\pi\right]_s \triangleq \sum_a \pi(a \mid s) \sum_r p(r \mid s, a) r} \\
& {\left[P_\pi\right]_{s, s^{\prime}}=p\left(s^{\prime} \mid s\right) \triangleq \sum_a \pi(a \mid s) \sum_{s^{\prime}} p\left(s^{\prime} \mid s, a\right)}
\end{aligned}
$$
### 求解

BOE中需要注意：$\sum_{a} \pi(a | s) = 1$

则$v(s) = \max_\pi \sum_a \pi(a \mid s) q(s,a) = \max_{a \in \mathcal{A}(s)} q(s,a)$
$$
\begin{align*}
&\pi(a \mid s)= \begin{cases}1 & a=a^* \\ 0 & a \neq a^*\end{cases} \\
&a^* = \arg \max_{a} q(s,a)
\end{align*}
$$
即当前状态下的最大动作值

对于
$$
v=\max _\pi\left(r_\pi+\gamma P_\pi v\right)
$$
固定$\pi$，将等式右边看作$v$的函数，令 $f(v) := \max _\pi\left(r_\pi+\gamma P_\pi v\right)$
则BOE变为 $v = f(v)$，需要求解该等式

>[!info] 前导知识点：压缩映射（contraction mapping）
> - 概念：
> 	- 不动点：$x \in X, f:X \rightarrow X$，若$f(x) = x$则$x$为不动点
> 	- 压缩映射：若$\|f(x_{1}) - f(x_{2}) \| \leq \gamma \|x_{1} - x_{2} \|, \gamma \in (0,1)$则称$f$为一压缩映射
> 	- 推广到向量函数：$x = f(x) = Ax, x \in \mathbb{R}^n, A \in \mathbb{R}^{n \times n}, \|A \| \leq \gamma < 1$则 $f(x) = Ax$为一压缩映射
> 
> -  **压缩映射定理**：对于任意形式为$x = f(x)$的等式，若$f$为一压缩映射，则：
> 	- 存在性：存在一个不动点$x^*$满足$x^* = f(x^*)$
> 	-  唯一性：$x^*$是唯一的
> 	-  算法：考虑序列$\{ x_{k}\}, x_{k+1} = f(x_{k})$，则当$k \longrightarrow \infty$时有$x_{k} \longrightarrow x^*$，且收敛速度是指数级的
应用到BOE求解（需要先证明$f$为一压缩映射）：
BOE存在唯一的解 $v^*$ ，迭代公式为 $v_{k+1} = f(v_{k}) = \max _\pi\left(r_\pi+\gamma P_\pi v_{k}\right)$ 

^contractionmapping

假设 $v^*$ 为BOE的解，满足 $v^*  = \max _\pi\left(r_\pi+\gamma P_\pi v^*\right)$。固定 $v^*$来求解$\pi^*$，假设$\pi^* = \arg \max_{\pi}(r_{\pi} + \gamma P_{\pi}v^*)$
则 $v^* = r_{\pi^*} = \gamma P_{\pi^*}v^*$
即为对应策略$\pi^*$的贝尔曼公式，故$v^* = v_{\pi^*}$，最优状态值实际上是最优策略下的对应状态值，**贝尔曼最优公式实际上是特殊的[[贝尔曼公式 Bellman Equation|贝尔曼公式]]**
对应的最优策略为
$$
\pi^*(a|s) = \begin{cases}
1 \quad a = a^*(s) \\ 
0 \quad a \not = a^*(s)
\end{cases}
$$
其中 $a^*(s) = \arg \max_{a} q^*(a,s)$ 

---
## 分析最优策略

BOE:
$v(s) = \max_\pi \sum_a \pi(a \mid s)\left(\sum_r p(r \mid s, a) r+\gamma \sum_{s^{\prime}} p\left(s' \mid s, a\right)v(s') \right)$

> [!question] 影响最优策略的因素
> - 奖励函数设计：$r$
> - 系统模型：$p(s' \mid s,a), p(r \mid s,a)$
> - 折扣率：$\gamma$

需要求解的量：$v(s), v(s'), \pi(a \mid s)$

### 案例

![PixPin_2025-09-24_22-39-43.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1758724796174_PixPin_2025-09-24_22-39-43.png)

减小折扣率$\gamma$，策略变得更加短视：

![PixPin_2025-09-24_22-41-39.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1758724903836_PixPin_2025-09-24_22-41-39.png)

$\gamma$减小到0，策略变得及其短视，退化为完全贪心策略，只关注即时奖励：

![PixPin_2025-09-24_22-44-33.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1758725084952_PixPin_2025-09-24_22-44-33.png)

设原有的最优状态值$v^* = \max_{\pi}(r_{\pi} + \gamma P_{\pi} v^*)$
对原有的所有奖励进行**线性变换** $r \longrightarrow ar+b, a \not = 0$，**不影响最优策略$\pi^*$**
新的最优状态值 $v' = av^* + \frac{b}{1-\gamma} \mathbf{1}$，其中 $\mathbf{1} = [1,\dots,1]^\top$

![PixPin_2025-09-24_22-49-07.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1758725365849_PixPin_2025-09-24_22-49-07.png)

