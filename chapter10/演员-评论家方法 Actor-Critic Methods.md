回顾策略梯度方法
1. 目标函数$J(\theta)$
2. 迭代公式
$$\begin{array} { l } { \theta _ { t + 1 } = \theta _ { t } + \alpha \nabla _ { \theta } J ( \theta _ { t } ) } \\ { \qquad = \theta _ { t } + \alpha \mathbb { E } _ { S \sim \eta, A \sim \pi } \Big [ \nabla _ { \theta } \ln \pi ( A | S, \theta _ { t } ) q _ { \pi } ( S, A ) \Big ] } \end{array}
$$
3. 随机梯度上升
$$
\theta _ { t + 1 } = \theta _ { t } + \alpha \nabla _ { \theta } \ln \pi ( a _ { t } | s _ { t }, \theta _ { t } ) q _ { t } ( s _ { t }, a _ { t } )
$$
4. 估计$q_{t}(s_{t},a_{t})$

使用[[时序差分方法 Temporal-Difference Learning（TD learning）|时序差分方法]]估计$q_{t}(s_{t},a_{t})$即为Actor-Critic方法
## 基本概念

- Actor：负责策略更新policy update
- Critic：负责策略评估policy evaluation
---
## Q-value Actor-Critic (QAC)

![image.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1763294404371_20251116195952461.png)

其中$w$为Critic函数的参数
- Critic：通过[[值函数近似 Value Function Approximation (VFA)#使用VFA的Sarsa|Sarsa]]方法结合值函数估计，输入五元组$(s_{t},a_{t},r_{t+1},s_{t+1},a_{t+1})$，输出对动作值的估计$q_{t}(s_{t},a_{t},w_{t+1})$
- Actor：输入三元组$(s_{t},a_{t},q_{t})$，进行策略更新
以上方法中生成数据的策略需要由Actor网络进行更新，behavior policy和target policy是同一个，故为on-policy方法

---
## Advantage Actor-Critic (A2C)

策略梯度方法的一个性质：对$q_{\pi}(S,A)$偏置量的不变性
$$
\begin{align*}
\nabla_{\theta}J(\theta) &= \mathbb{E}_{S \sim \eta, A \sim \pi} \big[ \nabla_{\theta} \ln \pi(A | S,\theta_{t}) q_{\pi}(S,A) \big] \\ \\
&= \mathbb{E}_{S \sim \eta, A \sim \pi} \big[ \nabla_{\theta} \ln \pi(A | S,\theta_{t}) (q_{\pi}(S,A) - \underbrace{b(S)}_{偏置量b}) \big] 
\end{align*}
$$
加上一个偏置量后结果不受影响

> [!question] 为什么能添加偏置？为什么要添加偏置？
> - 要证明添加偏置不影响结果，即证$$
 \mathbb { E } _ { S \sim \eta, A \sim \pi } \Big [ \nabla _ { \theta } \ln \pi ( A | S, \theta _ { t } ) b ( S ) \Big ] = 0
> $$
> 
> 如下：
> $$\begin{align*}
\mathbb { E } _ { S \sim \eta, A \sim \pi } \left [ \nabla _ { \theta } \ln \pi \big ( A \big | S, \theta _ { t } \big ) b ( S ) \right ] &= \sum _ { s \in \mathcal{S} } \eta ( s ) \sum _ { a \in \mathcal{A} } \pi \big ( a \big | s, \theta _ { t } \big ) \nabla _ { \theta } \ln \pi \big ( a \big | s, \theta _ { t } \big ) b ( s) \\ \\
&= \sum_{s \in \mathcal{S}} \eta(s) \sum_{a \in \mathcal{A}} \nabla_{\theta} \pi(a | s, \theta_{t}) b(s) \\ \\
&= \sum_{s \in \mathcal{S}} \eta(s) b(s) \sum_{a \in \mathcal{A}} \nabla_{\theta} \pi(a | s, \theta_{t}) \\ \\
&= \sum_{s \in \mathcal{S}} \eta(s) b(s) \nabla_{\theta} \underbrace{\sum_{a \in \mathcal{A}}  \pi(a | s, \theta_{t})}_{1} \\ \\
&= 0
>\end{align*}$$
> - 添加偏置的优势
> 目标函数的梯度$\nabla_{\theta}J(\theta) = \mathbb{E}[X]$，其中
> $$X(S,A) = \nabla_{\theta} \ln \pi(A|S,\theta_{t}) [q(S,A) - b(S)]$$
> 
> 	- 已知：
> 		- $\mathbb{E}[X]$对$b(S)$是不变量
> 		- $\text{var}(X)$ 即$X$的方差对$b(S)$不是不变量
> ![image.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1763300119648_20251116213512042.png)
> 	- 目标：选择一个最优的偏置$b$让$var(X)$最小
> 	- 最优偏置$b^*(s)$如下
> ![image.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1763300322621_20251116213833954.png)
> 但太复杂，常常将系数项去掉，选择次优偏置$$b(s) = \mathbb{E}_{A \sim \pi}[q(s,A)] = v_{\pi}(s)$$

添加偏置后的迭代公式：
$$
\begin{align*}
\theta_{t+1} &= \theta _ { t } + \alpha \mathbb { E }  \Big [ \nabla _ { \theta } \ln \pi ( A | S, \theta _ { t } ) \big [q _ { \pi } ( S, A ) - v_{\pi}(S) \big] \Big ] \\ \\
&= \theta_{t} + \alpha \mathbb{E} \Big[ \nabla_{\theta} \ln \pi(A | S, \theta_{t}) \underbrace{\delta_{\pi}(S,A)}_{\text{优势函数}} \Big]
\end{align*}
$$
- **优势函数advantage function**：$\delta_{\pi}(S,A) = q_{\pi}(S,A) - v_{\pi}(S)$

重新组织随机梯度下的迭代公式：
$$
\begin{align*}
\theta_{t+1} &= \theta_{t} + \alpha \nabla_{\theta} \ln \pi(a_{t} | s_{t}, \theta_{t}) \delta_{\pi}(s_{t},a_{t}) \\ \\
&= \theta_{t} + \alpha \frac{\nabla_{\theta}\pi(a_{t} | s_{t}, \theta_{t})}{\pi(a_{t} | s_{t}, \theta_{t})} \delta_{\pi}(s_{t},a_{t}) \\ \\
&= \theta_{t} + \underbrace{ \alpha \Big( \frac{\delta_{\pi}(s_{t},a_{t})}{\pi(a_{t} | s_{t}, \theta_{t})} \Big)}_{步长} \nabla_{\theta} \pi(a_{t} | s_{t}, \theta_{t}) \\ 
\end{align*}
$$
步长系数$\delta_{\pi}(s_{t},a_{t}) / \pi(a_{t} | s_{t}, \theta_{t})$也是一个可以平衡exploitation和exploration的量，参考[[策略梯度方法 Policy Gradient Methods#^NewStepSize]]

进一步，$\delta_{t}(s_{t},a_{t}) = q_{t}(s_{t},a_{t}) - v_{t}(s_{t})$可以由TD-error $r_{t+1} + \gamma v_{t}(s_{t+1}) - v_{t}(s_{t})$ 估计

> [!info] 估计的可行性
> $$
\mathbb{E}[q_{\pi}(S,A) - v_{\pi}(S) \mid S = s_{t}, A = a_{t}] = \mathbb{E}[R + \gamma v_{\pi}(S') - v_{\pi}(S) \mid S = s_{t}, A = a_{t}]
$$

估计的优势：仅需要一个神经网络来估计$v_{\pi}(s)$，而不是两个网络分布估计$q_{\pi}(s,a)$和$v_{\pi}(s)$

---
## Off-policy的Actor-Critic方法

### 重要性采样 Importance Sampling

---
## Deterministic Actor-Critic

