## 概述

>[!question] 问题
>- 如何在没有模型的情况下进行进行估计？ >> Monte Carlo 模拟

---
## MC Basic

思路：将策略迭代中依赖模型的部分进行替换

策略迭代中的两个步骤
- 策略评估（policy evaluation）：计算$v_{\pi_{k}} = r_{\pi_{k}} + \gamma P_{\pi_{k}} v_{\pi_{k}}$（贝尔曼公式），用策略计算当前状态值$v_{\pi_{k}}$
- 策略提升（policy improvement）：计算$\pi_{k+1} = \arg \max_{\pi}(r_{\pi} + \gamma P_{\pi} v_{\pi_{k}})$，用$v_{\pi_{k}}$计算新的更优的策略


## MC Exploring Starts

## MC $\epsilon-$greedy

