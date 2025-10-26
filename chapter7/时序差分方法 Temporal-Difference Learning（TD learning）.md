## 引入

考虑更复杂的问题：$w = \mathbb{E}[R + \gamma v(X)]$，其中$R, X$为随机变量，$\{ x \},\{ r \}$为对应的采样值，$\gamma$为常数
1. 令 $g(w) = w - \mathbb{E}[R + \gamma v(X)]$ ，$\tilde{g}(w,\eta) = w - (r + \gamma v(x)) = (w - \mathbb{E}[R + \gamma v(X)]) + (\mathbb{E}[R + \gamma v(X)] - [r + \gamma v(x)]) = g(w) + \eta$
2. 使用[[随机近似理论 Stochasted Approximation Theory#Robins-Monro (RM) 算法|RM算法]]求解：$w_{k+1} = w_{k} - \alpha_{k} \tilde{g}(w_{k},\eta_{k}) = w_{k} - \alpha_{k}[w_{k} - (r_{k} + \gamma v(x_{k}))]$

令其中 $R$ 为[[基本概念#奖励 Reward|奖励]]，$\gamma$ 为[[基本概念#折扣率 Discount Rate|折扣率]]

---
## TD Basic

基于数据，学习一个给定策略的状态值State Value

**数据**：通过策略$\pi$得到的trajectory $(s_{0},r_{1},s_{1},\dots,s_{t},r_{t+1},s_{t+1},\dots)$或$\{ (s_{t},r_{t+1},s_{t+1}) \}_{t}$

TD-Basic算法（**通过一个策略估计状态值，不估计动作值或最佳策略**）：
$$
\begin{align*}
v_{t+1}(s_{t})&=v_{t}(s_{t})-\alpha_{t}(s_{t})\left[v_{t}(s_{t})-[r_{t+1}+\gamma v_{t} (s_{t+1})]\right], &(1) \\
v_{t+1}(s)&=v_{t}(s),\quad\forall s\neq s_{t}. &(2)
\end{align*}
$$
其中$s$为状态空间中的任意状态，$v_{t}(s)$为 $t$ 时刻对 $v_{\pi}(s)$ 的一个估计值；$s_{t}$代表一个trajectory中 $t$ 时刻访问到的状态

- 在 $t$ 时，仅有被访问的状态 $s_{t}$ 的状态值 $v_{t}(s_{t})$ 被更新，其他未被访问的状态值不变
- 式(2)中的更新往往被忽略

> [!warning] 注意
> TD Learning的特点就是**边走边估计**，因此$v$和$s$的下标$t$可以看作同一个变量

![PixPin_2025-10-12_15-50-55.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1760255466942_PixPin_2025-10-12_15-50-55.png)

- 其中 $\bar{v}_{t} = r_{t+1} + \gamma v(s_{t+1})$ 称为TD target，代表 $v(s_{t})$ 希望被更新的方向
- $\delta_{t} = v(s_{t}) - [r_{t+1} + \gamma v(s_{t+1})]= v(s_{t}) - \bar{v}_{t}$ 称为 TD error

### TD target

$\bar{v}_{t} = r_{t+1} + \gamma v(s_{t+1})$ 是算法希望 $v_{t}(s_{t})$ 接近的值，算法希望 $v_{t+1}(s_{t})$ 比 $v_{t}(s_{t})$ 更接近 $\bar{v}_{t}$

> [!info] 证明：[[贝尔曼最优公式 Bellman Optimality Equation#^contractionmapping|压缩映射]]
> ![PixPin_2025-10-12_16-01-44.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1760256125147_PixPin_2025-10-12_16-01-44.png)

### TD error

$\delta_{t} = v(s_{t}) - [r_{t+1} + \gamma v(s_{t+1})]= v(s_{t}) - \bar{v}_{t}$ 是两个时刻之间的时序差分（temporal difference），$v(s_{t})$ 是 $t$ 时刻访问的状态 $s_{t}$ 的状态值，$\bar{v}_{t}$ 是算法希望 $v(s_{t})$ 在 $t+1$ 时刻接近的值，**表征 $v_{t}(s)$ 和 $v_{\pi}(s)$ 之间的误差**

定义 $\delta_{\pi ,t} = v_{\pi}(s_{t}) - [r_{t+1} + \gamma v_{\pi}(s_{t+1})]$
则 $$\mathbb{E}[\delta_{\pi,t} | S_{t} = s_{t}] = v_{\pi}(s_{t}) - \mathbb{E}[R_{t+1} + \gamma v_{\pi}(S_{t+1}) | S_{t} = s_{t}] = 0$$
其中 $\mathbb{E}[R_{t+1} + \gamma v_{\pi}(S_{t+1}) | S_{t} = s_{t}] = v_{\pi}(s_{t})$ （[[贝尔曼公式 Bellman Equation#^bellmanequation|贝尔曼公式]]）
故 $v_{t} = v_{\pi}$ 时 $\delta_{t} = 0$，表征了 $v_{t}$ 和 $v_{\pi}$ 之间的误差

### TD算法的实质：用RM算法求解贝尔曼公式

给出[[贝尔曼公式 Bellman Equation#贝尔曼公式 Bellman Equation|贝尔曼公式]]的一个新定义
$$v_{\pi}(s) = \mathbb{E}[R + \gamma G | S = s], \quad s \in \mathcal{S}$$
其中$G$为跳到下一个状态的discounted return
$\mathbb{E}[G | S = s] = \sum_{a} \pi(a | s) \sum_{s'}p(s' | s,a) v_{\pi}(s') = \mathbb{E}[v_{\pi}(S') | S = s]$
其中$S'$为下一个状态
则
$$
v_{\pi}(s) = \mathbb{E}[R + \gamma v_{\pi}(S') | S = s], \quad s \in \mathcal{S}
$$
也称**Bellman expectation equation 贝尔曼期望公式**
接下来用RM算法求解：
定义
$$\begin{align}
g(v(s)) &= v(s) - v_{\pi}(s) \\ &= v(s) - \mathbb{E}[R + \gamma v_{\pi}(S') |s]
\end{align}
$$
已有对 $R$ 的采样值 $r$，$S'$ 的采样 $s'$ 
则观测值噪声函数
$$
\begin{align}
\tilde{g}(v(s)) &= v(s) - [r + \gamma v_{\pi}(s')] \\ \\
&=  v(s) - \mathbb{E}[R + \gamma v_{\pi}(S') | s ]  + \mathbb{E}[R + \gamma v_{\pi}(S') | s] - (r + \gamma v_{\pi}(s')) \\ \\
&= g(v(s)) + \eta
\end{align}
$$




---
## Sarsa

---
## Q-Learning

---
## on-policy和off-policy方法