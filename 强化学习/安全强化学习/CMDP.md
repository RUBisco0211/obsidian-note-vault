---
title: CMDP
created: 2026-01-18 09:59:04
updated: 2026-01-18 10:07:15
---

CMDP 是 **Constrained Markov Decision Process（约束马尔可夫决策过程）** 的缩写，是在标准 MDP 基础上加入**约束条件**的一类强化学习/决策模型。

---

## 直观理解

**MDP**：

> 在不确定环境中，找一个策略，使“回报最大”。

**CMDP**：

> 在不确定环境中，找一个策略，  
> **在满足若干约束的前提下，使主目标回报最大**。

👉 可以理解为：  
**“既要效果好，又不能违规/超预算/超风险”**

---

## CMDP 的数学定义

一个 CMDP 通常定义为一个六元组：

$$
  
(\mathcal{S}, \mathcal{A}, P, r, c_1,\dots,c_K, \gamma)  
]
$$

其中：

- $\mathcal{S}$：状态空间
- $\mathcal{A}$：动作空间
- $P(s'|s,a)$：状态转移概率
- $r(s,a)$：**主奖励函数（要最大化）**
- $c_k(s,a)$：第 $k$ 个**代价 / 约束函数**
- $\gamma \in (0,1)$：折扣因子

---

### 优化目标（核心）
$$
  
\begin{aligned}  
\max_{\pi} \quad & \mathbb{E}_\pi\left[\sum_{t=0}^\infty \gamma^t r(s_t,a_t)\right] \\ 
\text{s.t.} \quad  
& \mathbb{E}_\pi\left[\sum_{t=0}^\infty \gamma^t c_k(s_t,a_t)\right] \le d_k, \quad k=1,\dots,K  
\end{aligned}  
$$

解释：

- 最大化 **长期累计奖励**
- 同时要求：
    - 每个代价（能耗、风险、延迟、违规次数等）
    - **长期期望不超过给定阈值 $d_k$**

---

## CMDP 与 MDP 的区别

|方面|MDP|CMDP|
|---|---|---|
|目标|最大化奖励|在约束下最大化奖励|
|回报|单一 reward|reward + 多个 cost|
|策略|只追求最优|需权衡收益与安全|
|难度|相对简单|明显更难|

---

## 常见求解方法

### 拉格朗日松弛

把约束“合并”进奖励函数：

$$
  
r'(s,a) = r(s,a) - \sum_{k=1}^K \lambda_k c_k(s,a)  
$$
- $\lambda_k \ge 0$：拉格朗日乘子
- 变成一个 **无约束 MDP**
- 交替更新：
    - 策略 $\pi$
    - 乘子 $\lambda$

📌 强化学习中非常常见（如 Lagrangian PPO）

---

### 线性规划

当状态、动作有限时：

- CMDP 可以写成 **线性规划**
- 直接求最优解
- 但不适合大规模问题

---

### 安全强化学习（Safe RL）

CMDP 是 Safe RL 的理论基础：

- Shielding
- Primal-Dual 方法
- Constrained Policy Optimization (CPO)

