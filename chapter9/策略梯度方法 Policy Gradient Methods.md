## 从基于值的方法到基于策略的方法

- value based：通过优化与值（状态值或动作值）相关的目标函数来得到最优值，从而得到目标策略
- policy based：直接优化与策略相关的目标函数来得到最优策略

---
## 基本概念

### 策略的连续形式

目前遇到的策略都是以表格的离散形式表示

![image.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1763262312617_20251116110502371.png)

要使用基于策略的方法，先将策略使用函数表示
$$\pi(a | s, \theta), \quad \text{或} \quad \pi_{\theta}(a | s)$$
其中$\theta \in \mathbb{R}^m$为参数向量
- 常见形式为神经网络：输入状态$s$，输出$s$下选择每个动作$a$的概率向量
策略函数的优势
- 可以表示连续状态和动作空间下的可能性
- 泛化能力

### 与离散表示的差异

1. 最优策略的定义
	- 离散：最优策略下的值最优
	- 连续：用标量指标来评估策略，最佳即为最优
2. 如何获取$\pi(a|s)$概率值？
	- 离散：查表
	- 连续：输入网络进行计算
3. 如何更新策略？
	- 离散：直接更新策略表格项
	- 连续：优化目标函数，更新参数$\theta$从而间接更新策略
### 基本思路

1. 使用$\theta$相关的指标或目标函数来定义最优策略：$J(\theta)$，需要最大化
2. 进行优化（基于梯度）：$\theta_{t+1} = \theta_{t} + \alpha \nabla_{\theta} J(\theta_{t})$
   
> [!question] 问题
> 1. 如何选择指标或目标函数
> 2. 如何计算梯度？

---
## 定义最优策略的指标

主要有两类指标

### 平均状态值 average state value

$$
\bar{v}_{\pi} = \sum_{s \in \mathcal{S}} d(s) v_{\pi}(s) = \mathbb{E}[v_{\pi}(S)]
$$
其中$S \sim d$，也可写作矩阵向量形式
$$
\bar{v}_{\pi} = d^T v_{\pi}
$$
#### 如何选择状态分布$d(s)$

**第一种情况：**$d$与$\pi$相互独立，记$d_{0} = d$，$\bar{v}_{\pi}^0 = \bar{v}_{\pi}$
（对策略求梯度时，由于$d$不依赖$\pi$，$\nabla  \bar{v}_{\pi} = d^T \nabla v_{\pi}$）
$d_{0}$的选择：
- 均匀分布，$d_{0} = 1 / |\mathcal{S}|$
- 仅关心某个状态如$s_{0}$，则$d_{0}(s_{0}) = 1, \quad d_{0}(s \neq s_{0}) = 0$

**第二种情况：** $d$依赖于$\pi$
- [[值函数近似 Value Function Approximation (VFA)#稳态分布 stationary distribution | 稳态分布 stationary distribution]]：$d = d_{\pi}$
	- 性质：$d_{\pi}^T P_{\pi} = d^T_{\pi}$，其中$P_{\pi}$为状态转移矩阵


---
## 求解策略梯度

---
## 梯度上升方法 (REINFORCE)