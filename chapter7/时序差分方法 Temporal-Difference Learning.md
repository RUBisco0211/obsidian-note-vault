## 引入

考虑更复杂的问题：$w = \mathbb{E}[R + \gamma v(X)]$，其中$R, X$为随机变量，$\{ x \},\{ r \}$为对应的采样值，$\gamma$为常数
1. 令 $g(w) = w - \mathbb{E}[R + \gamma v(X)]$ ，$\tilde{g}(w,\eta) = w - (r + \gamma v(x)) = (w - \mathbb{E}[R + \gamma v(X)]) + (\mathbb{E}[R + \gamma v(X)] - [r + \gamma v(x)]) = g(w) + \eta$
2. 使用[[随机近似理论 Stochasted Approximation Theory#Robins-Monro (RM) 算法|RM算法]]求解：$w_{k+1} = w_{k} - \alpha_{k} \tilde{g}(w_{k},\eta_{k}) = w_{k} - \alpha_{k}[w_{k} - (r_{k} + \gamma v(x_{k}))]$

令其中 $R$ 为[[基本概念#奖励 Reward|奖励]]，$\gamma$ 为[[基本概念#折扣率 Discount Rate|折扣率]]


---
## TD basic


---
## Sarsa

---
## Q-Learning

---
## on-policy和off-policy方法