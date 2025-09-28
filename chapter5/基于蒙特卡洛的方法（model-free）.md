## 概述

>[!question] 问题
>- 如何在没有模型的情况下进行进行估计？ >> Monte Carlo 模拟

---
## Monte Carlo Basic

### 思路

基本思想：将**策略迭代**中依赖模型，即计算动作值$q(s,a)$的部分进行替换

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
- **不依赖模型，动作值的原始定义：**$$q_{\pi_{k}}(s, a) = \mathbb{E}[G_t|S_t = s, A_t = a]$$

### 蒙特卡罗估计的基本步骤

- 从初始的状态-动作组合$(s,a)$出发，按策略 $\pi_{k}$ 得到一个episode
- 计算该episode的奖励值 $g(s,a)$
- $g(s,a)$ 即为 $G_{t}$ 的一个采样值 $q_{\pi_{k}}(s, a) = \mathbb{E}[G_t|S_t = s, A_t = a]$
- 多次采样得到采样集 $\{ g^{(j)}(s,a)\}$，则有 $$q_{\pi_{k}}(s, a) = \mathbb{E}[G_t|S_t = s, A_t = a] \approx \frac{1}{N} \sum_{i=1}^N g^{(i)}(s,a)  $$
在强化学习领域，这种采样值被称作**经验 experience**

### 具体实现

![PixPin_2025-09-27_21-23-00.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1758979395110_PixPin_2025-09-27_21-23-00.png)

每个迭代中：
- 策略评估：通过**蒙特卡洛估计**求出所有状态动作组合$(s,a)$对应的状态值$q_{\pi_{k}}(s,a)$
- 策略提升：$\pi_{k+1} = \arg \max_{\pi}(r_{\pi} + \gamma P_{\pi} v_{\pi_{k}})$

> [!tip] MC Basic的缺点
> - 非常低效的方法，仅作为蒙特卡洛方法核心思想介绍

> [!info] 蒙特卡洛模拟中episode的长度设置
> 

---
## Monte Carlo Exploring Starts

---
## Monte Carlo $\epsilon-$greedy

