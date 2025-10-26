## 引入

考虑更复杂的问题：$w = \mathbb{E}[R + \gamma v(X)]$，其中$R, X$为随机变量，$\{ x \},\{ r \}$为对应的采样值，$\gamma$为常数
1. 令 $g(w) = w - \mathbb{E}[R + \gamma v(X)]$ ，$\tilde{g}(w,\eta) = w - (r + \gamma v(x)) = (w - \mathbb{E}[R + \gamma v(X)]) + (\mathbb{E}[R + \gamma v(X)] - [r + \gamma v(x)]) = g(w) + \eta$
2. 使用[[随机近似理论 Stochasted Approximation Theory#Robins-Monro (RM) 算法|RM算法]]求解：$w_{k+1} = w_{k} - \alpha_{k} \tilde{g}(w_{k},\eta_{k}) = w_{k} - \alpha_{k}[w_{k} - (r_{k} + \gamma v(x_{k}))]$

令其中 $R$ 为[[基本概念#奖励 Reward|奖励]]，$\gamma$ 为[[基本概念#折扣率 Discount Rate|折扣率]]

---
## TD Basic：学习状态值

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
定义目标函数
$$\begin{align}
g(v(s)) &= v(s) - v_{\pi}(s) \\ \\  
&= v(s) - \mathbb{E}[R + \gamma v_{\pi}(S') |s]
\end{align}
$$
已有对 $R$ 的采样值 $r$，$S'$ 的采样 $s'$ 
则带噪声的观测值函数
$$
\begin{align}
\tilde{g}(v(s)) &= v(s) - [r + \gamma v_{\pi}(s')]  \\
 \\
&= \Big( v(s) - \mathbb{E}[R + \gamma v_{\pi}(S') | s ]   \Big) + \Big( \mathbb{E}[R + \gamma v_{\pi}(S') | s] - (r + \gamma v_{\pi}(s')) \Big) \\  \\

&= g(v(s)) + \eta
\end{align}
$$

则RM算法的迭代公式为：
$$
\begin{align}
v_{t+1}(s) &= v_{t}(s) - \alpha_{t} \tilde{g}(v_{t}(s)) \\ \\
&= v_{t}(s) - \alpha_{t}\Big( v_{t}(s) - [r_{t} + \gamma v_{\pi}(s_{t}')] \Big) \quad t=1,2,3,\dots
\end{align}
$$
其中$v_{k}(s)$为在第$k$次迭代时对$v_{\pi}(s)$的估计值，$r_{k},s'_{k}$为第$k$次迭代时得到的采样值
>[!important] 从RM算法到TD方法
> 以上RM迭代公式现有的缺陷：
> - 这是仅对一个状态$s$的估计，因此采样$\{(s_{t},r_{t},s_{t}')\}$为对状态$s$的**重复采样**
> - $v_{\pi}(s_{t}')$未知，无法计算
> 
> 因此作出以下两点修改：
> - 将对状态$s$的**重复采样**$\{(s_{t},r_{t},s_{t}')\}$替换为一个trajectory$\{(s_{t},r_{t+1},s_{t+1})\}$，$t$时仅更新被访问到的状态$s_{t}$的状态值估计$v_{t}(s_{t}) \to v_{t+1}(s_{t})$，对其他$s \not = s_{t}$则令$v_{t+1}(s) = v_{t}(s)$，即不变
> - 将$v_{\pi}(s_{t}')$替换为估计值$v_{t}(s')$，且由于上述采样trajectory中$s_{t} \xrightarrow{r_{t+1}} s_{t+1}$，故估计值为$v_{t}(s_{t+1})$

修改后即为TD方法
$$
\begin{equation}
v_{t+1}(s) = \left\{
\begin{aligned}
&v_{t}(s)-\alpha_{t}(s)\big(v_{t}(s)-[r_{t+1}+\gamma v_{t} (s_{t+1})]\big),\quad &s = s_{t},  \\\\
& v_{t}(s),\quad &\forall s\neq s_{t}. 
\end{aligned}
\right.
\end{equation}
$$
### 与 Monte Carlo 方法的比较

| 维度   | TD                                         | MC                                                |
| ---- | ------------------------------------------ | ------------------------------------------------- |
| 更新方式 | 在线 (Online)：TD 是在线更新的，每获得一个奖励即可立刻更新状态/动作值。 | 离线 (Offline)：MC 必须等到整个 episode 结束后，才能基于完整的回报进行更新。 |
| 适用任务 | 可处理 episodic（情节性） 与 continuing（持续性） 任务。    | 仅适用于 episodic（情节性） 任务，即任务需有明确终止状态。                |
| 估计方式 | Bootstrapping：依赖于当前价值估计进行更新，因此需要初始猜测值。     | Non-bootstrapping：直接基于完整回报估计状态/动作值，不依赖初始估计。       |
| 方差表现 | 低估计方差：由于TD只依赖局部的即时回报和下一状态估计，随机变量较少，方差较低。   | 高估计方差：MC 依赖整条 episode 的return序列，受随机性影响大，方差更高。     |

- **TD 方法** 强调“边走边学”，依靠部分经验（bootstrapping），效率高但略带偏差。
- **MC 方法** 强调“完整体验后再学”，不依赖估计（non-bootstrapping），理论上无偏但方差大。

---
## Sarsa：学习动作值

通过估计动作值$q(s,a)$来求解最优策略

目标：估计一个给定策略的动作值
假设有经验序列$\{ (s_{t},a_{t},r_{t+1}, s_{t+1}, a_{t+1})\}_{t}$
则Sarsa迭代公式
$$
\begin{equation}
q_{t+1}(s,a) = \left\{ 
\begin{aligned}

&q_{t}(s,a) - \alpha_{t}(s,a) \big[ q_{t}(s,a) - [r_{t+1} + \gamma q_{t}(s_{t+1},a_{t+1})] \big], \quad &(s,a) = (s_{t},a_{t}), \\ \\
&q_{t}(s,a), \quad &\forall (s,a) \neq (s_{t},a_{t})

\end{aligned}
\right.
\end{equation}
$$
依赖的数据为$(s_{t},a_{t},r_{t+1}, s_{t+1}, a_{t+1})$，故称Sarsa

### Sarsa 依赖的贝尔曼公式

Sarsa依赖的贝尔曼公式形式：
$$
q_{\pi}(s,a) = \mathbb{E}[R + \gamma q_{\pi}(S',A') | s,a], \quad \forall s \in \mathcal{S},a \in \mathcal{A}(s)
$$
接着用RM算法进行迭代求解

### 与 policy imporvement 结合

![PixPin_2025-10-26_11-45-47.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761450359156_PixPin_2025-10-26_11-45-47.png)

使用 $\epsilon$-greedy 策略进行策略更新

---
## Expected Sarsa

把Sarsa中的$q_{t}(s_{t+1},a_{t+1})$替换为**对该状态上各个不同动作得到的动作值的期望** $\mathbb{E}[q_{t}(s_{t+1},A)])$，即$v_{t}(s_{t+1})$

$$
\begin{equation}
q_{t+1}(s,a) = \left \{ 
\begin{aligned}
&q_{t}(s,a) - \alpha_{t}(s,a) \big[q_{t}(s,a) - (r_{t+1} + \gamma \mathbb{E}[q_{t}(s_{t+1},A)]) \big], \quad &(s,a) = (s_{t},a_{t}) \\ \\
&q_{t}(s,a), \quad &\forall(s,a) \neq (s_{t},a_{t})
\end{aligned}
\right.
\end{equation}
$$
其中$\mathbb{E}[q_t(s_{t+1},A)]=\sum_{a}\pi_{t}(a|s_{t+1})q_{t}(s_{t+1},a)\doteq v_{t}(s_{t+1})$

相比Sarsa，计算量更大，但不需要依赖数据$a_{t+1}$，涉及的随机变量减少

### Expected Sarsa依赖的贝尔曼公式

$$q_{\pi}(s,a) = \mathbb{E}\big[ R_{t+1} + \gamma \mathbb{E}_{A_{t+1} \sim \pi(S_{t+1})} [q_{\pi}(S_{t+1},A_{t+1})] \mid S_{t} = s,A_{t} = a \big] , \quad\forall s,a$$

即
$$q_{\pi}(s,a)=\mathbb{E}\left[R_{t+1}+\gamma v_{\pi}(S_{t+1})|S_{t}=s,A_{t}=a\right]$$

---
## $n$-step Sarsa

**$n$-step Sarsa是Sarsa的一种推广，可以变形为Sarsa和Monte Carlo两种方法**

考虑动作值的定义$q_\pi(s,a)=\mathbb{E}[G_t|S_t=s,A_t=a].$
其中$G_{t}$有多种分解形式

![PixPin_2025-10-26_13-08-58.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761455352641_PixPin_2025-10-26_13-08-58.png)

![PixPin_2025-10-26_13-11-17.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761455487947_PixPin_2025-10-26_13-11-17.png)

则$n$-step Sarsa的迭代公式为：

![PixPin_2025-10-26_13-12-33.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761455561010_PixPin_2025-10-26_13-12-33.png)

使用$G^{(\infty)}$分解法，再令$\alpha_{t} = 1$，则
$$q_{t+1}(s,a) = r_{t+1} + \gamma r_{t+2} + \gamma^2 r_{t+3} + \dots$$
即为Monte Carlo方法

---
## Q-Learning：直接估计最优动作值

$$
\begin{equation}
q_{t+1}(s,a) = \left \{ 
\begin{aligned}
&q_{t}(s,a) - \alpha_{t}(s,a) \big[ q_{t}(s,a) - [r_{t+1} + \gamma \max_{a \in \mathcal{A}} q_{t}(s_{t+1}, a)] \big], \quad &(s,a) = (s_{t},a_{t}), \\ \\
&q_{t}(s,a), \quad &\forall(s,a) \neq(s_{t},q_{t}).
\end{aligned}
\right.
\end{equation}

$$


---
## on-policy和off-policy方法