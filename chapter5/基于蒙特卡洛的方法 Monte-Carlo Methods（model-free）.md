## 概述

>[!question] 问题
>- 如何在没有模型的情况下进行进行估计？ > Monte Carlo 模拟


## Monte Carlo Basic

### 思路

基本思想：将[[基于模型的（model-based）BOE求解方法#策略迭代 policy iteration (PI)|策略迭代]]中依赖模型，即计算动作值$q(s,a)$的部分进行替换

> [!info] 策略迭代
> 策略迭代中的两个步骤
> - 策略评估（policy evaluation）：计算$v_{\pi_{k}} = r_{\pi_{k}} + \gamma P_{\pi_{k}} v_{\pi_{k}}$（贝尔曼公式），用策略计算当前状态值$v_{\pi_{k}}$
> - 策略提升（policy improvement）：计算$\pi_{k+1} = \arg \max_{\pi}(r_{\pi} + \gamma P_{\pi} v_{\pi_{k}})$，用$v_{\pi_{k}}$计算新的更优的策略
> 其中策略提升的分量形式：
> $$ \begin{align*}
\pi_{k+1} &=  \arg \max_{\pi} \sum_{a} \pi(a \mid s) \left[ \sum_{r}p(r | s,a)r + \gamma \sum_{s'} p(s' | s,a) v_{\pi_{k}}(s') \right] \\
&= \arg \max_{\pi} \sum_{a} \pi(a \mid s) q_{\pi_{k}}(s,a), s \in \mathcal{S}
\end{align*} $$

计算 $q_{\pi_{k}}(s,a)$ 的方法：
- 依赖模型：$$q_{\pi_{k}}(s, a) = \sum_{r}p(r | s,a)r + \gamma \sum_{s'} p(s' | s,a) v_{\pi_{k}}(s')$$
- **不依赖模型：[[贝尔曼公式 Bellman Equation#动作值 Action value#定义|动作值的原始定义]]**$$q_{\pi_{k}}(s, a) = \mathbb{E}[G_t|S_t = s, A_t = a]$$
### 蒙特卡罗估计的基本步骤

- 从初始的**状态-动作组合**$(s,a)$出发，按策略 $\pi_{k}$ 得到一个episode
- 计算该episode的奖励值 $g(s,a)$
- $g(s,a)$ 即为 $G_{t}$ 的一个采样值 $q_{\pi_{k}}(s, a) = \mathbb{E}[G_t|S_t = s, A_t = a]$
- 多次采样得到采样集 $\{ g^{(j)}(s,a)\}$，则有 $$q_{\pi_{k}}(s, a) = \mathbb{E}[G_t|S_t = s, A_t = a] \approx \frac{1}{N} \sum_{i=1}^N g^{(i)}(s,a)  $$
在强化学习领域，这种采样值被称作**经验 experience**

### 实现

![PixPin_2025-09-27_21-23-00.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1758979395110_PixPin_2025-09-27_21-23-00.png)

每个迭代中：
- 策略评估：通过**蒙特卡洛估计**求出所有状态动作组合$(s,a)$对应的状态值$q_{\pi_{k}}(s,a)$
- 策略提升：$\pi_{k+1} = \arg \max_{\pi}(r_{\pi} + \gamma P_{\pi} v_{\pi_{k}})$

> [!tip] MC Basic的缺点
> - 非常低效的方法，仅作为蒙特卡洛方法核心思想介绍

> [!info] 蒙特卡洛模拟中episode的长度设置
> - 过短：只有离目标较近的状态可以达到最优


## Monte Carlo Exploring Starts

### 思路

考虑网格世界中的一条路径
$$
s_1 \xrightarrow{a_2} s_2 \xrightarrow{a_4} s_1 \xrightarrow{a_2} s_2 \xrightarrow{a_3} s_5 \xrightarrow{a_1} \ldots
$$
概念：
- **访问 Visit**：一对状态-动作组合每次出现在路径中，称作对这个组合的一次Visit
MC Basic的做法：对所有状态动作组合进行初始访问，并以此计算动作值$q(s,a)$。没有充分利用路径上的所有数据

> [!info]
> 如上，在以$(s_{1},a_{2})$开始的路径中还包括了以$(s_{2},a_{4}),(s_{2},a_{3}),(s_{5},a_{1})$等组合开始的路径，可用于估算$q(s_{2},a_{4}),q(s_{2},a_{3}),q(s_{5},a_{1})$等
>  =>用**动态规划**的思路充分利用一个episode上的所有数据进行估算

充分利用数据的方法：
- first-visit method：仅在第一次访问$(s,a)$时估计$q(s,a)$
- every-visit method：每次访问$(s,a)$都进行估计

更新策略的时机：
![PixPin_2025-09-28_10-17-04.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1759025840236_PixPin_2025-09-28_10-17-04.png)

Generalized Policy Iteration（GPI）
![PixPin_2025-09-28_10-58-39.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1759028337578_PixPin_2025-09-28_10-58-39.png)

### 实现

![PixPin_2025-09-28_11-04-03.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1759028662347_PixPin_2025-09-28_11-04-03.png)

>[!info] 细节
>- 计算一个episode中各个子episode的reward $g$ 时，从后往前计算以减少计算量


## Monte Carlo $\epsilon-$greedy

> [!warning] 收敛性
> $\epsilon$-greedy算法的探索与收敛是矛盾的，很可能无法收敛

exploring starts：每次trajectory开始时，agent都要从所有可能的$(s,a)$对开始尝试进行模拟
MC-exploring-starts方法能找到最优策略，依赖的是所有$(s,a)$对都会被尝试到

**局限性**：
- 状态和动作空间巨大的任务中不可行
- 不通用

取代exploring starts的方法：soft policy，即不确定策略
### $\epsilon$-greedy policy

对于$q_{\pi}(s,a)$最大的$(s,a)$对，给予较大的选择概率，其他动作给予较小的选择概率，即
$$
\pi(a \mid s)= \begin{cases}1-\frac{\varepsilon}{|\mathcal{A}(s)|}(|\mathcal{A}(s)|-1), & \text { for the greedy action, } \\ \frac{\varepsilon}{|\mathcal{A}(s)|}, & \text { for the other }|\mathcal{A}(s)|-1 \text { actions. }\end{cases}
$$
其中$\epsilon \in [0,1]$，$\mathcal{A}(s)$为状态$s$上的动作集合

> [!info] $\epsilon$-greedy 策略的思路：平衡 exploitation（对路径的充分利用） 和 exploration（路径探索的广度）
> - 完全贪心的策略得到的路径reward一定是最好的，但需要遍历所有的状态动作对，计算量巨大
> - soft policy牺牲一定的最优性，但不需要巨量的路径探索就能考虑到最优动作以外的其他选择，从而极大降低计算量

$\epsilon = 0$时，退化为完全贪心策略；$\epsilon = 1$时，退化为随机策略
### 算法

![PixPin_2025-10-10_21-51-45.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1760104311459_PixPin_2025-10-10_21-51-45.png)

mc-basic 和mc-exploring-starts 方法中，策略提升步骤在于求解
$$
\pi_{k+1}(s)=\arg \max _{\pi \in \Pi} \sum_a \pi(a \mid s) q_{\pi_k}(s, a)
$$
也即完全贪心策略
$$
\pi_{k+1}(a \mid s)= \begin{cases}1, & a=a_k^* \\ 0, & a \neq a_k^*\end{cases}
$$
其中 $\Pi$ 为所有可能策略的集合，

mc-$\epsilon$-greedy方法中的策略提升为
$$
\pi_{k+1}(s)=\arg \max _{\pi \in \Pi_{\epsilon}} \sum_a \pi(a \mid s) q_{\pi_k}(s, a)
$$
也即
$$
\pi_{k+1}\left(a \mid s_t\right)= \begin{cases}1-\frac{\left|\mathcal{A}\left(s\right)\right|-1}{\left|\mathcal{A}\left(s\right)\right|} \epsilon, & a=a^*_{k} \\ \frac{1}{\left|\mathcal{A}\left(s\right)\right|} \epsilon, & a \neq a^*_{k}\end{cases}
$$
其中$\Pi_{\epsilon}$是所有$\epsilon$-greedy策略的集合

### $\epsilon$参数的意义

$\epsilon$ 参数衡量贪心程度，越小策略越贪心；越大策略越随机，越容易遍历到所有状态动作对
**故在迭代刚开始时可以让$\epsilon$较大，之后逐渐减小，来加速最优策略的迭代**

随着$\epsilon$增大，策略的最优性会降低；与最优策略的**一致性consistency**降低

