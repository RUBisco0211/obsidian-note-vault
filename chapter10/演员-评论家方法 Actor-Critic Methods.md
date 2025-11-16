回顾策略梯度方法
1. 目标函数$J(\theta)$
2. 迭代公式
$$\begin{array} { l } { \theta _ { t + 1 } = \theta _ { t } + \alpha \nabla _ { \theta } J ( \theta _ { t } ) } \\ { \qquad = \theta _ { t } + \alpha \mathbb { E } _ { S \sim \eta, A \sim \pi } \Big [ \nabla _ { \theta } \ln \pi ( A | S, \theta _ { t } ) q _ { \pi } ( S, A ) \Big ) } \end{array}
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
## Q-value Actor-Critic

![image.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1763294404371_20251116195952461.png)

其中$w$为Critic函数的参数
- Critic：通过[[值函数近似 Value Function Approximation (VFA)#使用VFA的Sarsa|Sarsa]]方法结合值函数估计，输入五元组$(s_{t},a_{t},r_{t+1},s_{t+1},a_{t+1})$，输出对动作值的估计$q_{t}(s_{t},a_{t},w_{t+1})$
- Actor：输入三元组$(s_{t},a_{t},q_{t})$，进行策略更新
以上方法中生成数据的策略需要由Actor网络进行更新，behavior policy和target policy是同一个，故为on-policy方法

---
## Advantage Actor-Critic (A2C)

---
## Off-policy的Actor-Critic方法

### 重要性采样 Importance Sampling

---
## Deterministic Actor-Critic

