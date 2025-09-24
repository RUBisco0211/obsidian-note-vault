## 最优策略

### 策略的比较

（使用状态值比较）

若 $v_{\pi_1}(s) \geq v_{\pi_2}(s), \forall s \in \mathcal{S}$ 则认为策略$\pi_1$优于$\pi_2$
### 定义

若$v_{\pi^*}(s) \geq v_\pi(s), \forall s \in \mathcal{S}$对于其他任意策略$\pi$都成立，则认为$\pi^*$为最优策略

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

令 $f(x) := \max _\pi\left(r_\pi+\gamma P_\pi v\right)$
则BOE变为 $v = f(v)$

>[!info] 先决条件：压缩映射原理
> - 不动点：$x \in X, f:X \rightarrow X$，若$f(x) = x$则$x$为不动点
> - 压缩映射：
	
---
## 最优状态值


