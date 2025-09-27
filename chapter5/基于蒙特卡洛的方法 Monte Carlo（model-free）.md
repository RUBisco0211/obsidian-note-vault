## 概述

>[!question] 问题
>- 如何在没有模型的情况下进行进行估计？ >> Monte Carlo 模拟

---
## MC Basic

思路：将策略迭代中依赖模型的部分进行替换

策略迭代中的两个步骤
- 策略评估（policy evaluation）：计算$v_{\pi_{k}} = r_{\pi_{k}} + \gamma P_{\pi_{k}} v_{\pi_{k}}$（贝尔曼公式），用策略计算当前状态值$v_{\pi_{k}}$
- 策略提升（policy improvement）：计算$\pi_{k+1} = \arg \max_{\pi}(r_{\pi} + \gamma P_{\pi} v_{\pi_{k}})$，用$v_{\pi_{k}}$计算新的更优的策略

策略提升的分量形式：
$$ \begin{align*}
\pi_{k+1} &=  \arg \max_{\pi} \sum_{a} \pi(a \mid s) \left[ \sum_{r}p(r | s,a)r + \gamma \sum_{s'} p(s' | s,a) v_{\pi_{k}}(s') \right] \\
&= \arg \max_{\pi} \sum_{a} \pi(a \mid s) q_{\pi_{k}}(s,a), s \in \mathcal{S}
\end{align*} $$
计算 $q_{\pi_{k}}(s,a)$ 的方法：
- 依赖模型：$q_{\pi_{k}}(s, a) = \sum_{r}p(r | s,a)r + \gamma \sum_{s'} p(s' | s,a) v_{\pi_{k}}(s')$
- 不依赖模型，动作值的原始定义：$q_{\pi_{k}}(s, a) = \mathbb{E}[G_t|S_t = s, A_t = a]$
## MC Exploring Starts

## MC $\epsilon-$greedy

