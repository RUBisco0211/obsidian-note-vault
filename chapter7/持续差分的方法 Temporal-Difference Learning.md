## 引入

考虑均值估计问题：根据随机变量$X$的样本$\{ x \}$计算$w = \mathbb{E}[X]$
1. 令$g(w) = w - \mathbb{E}[X]$，得到求根问题$g(w) = 0$
2. 带噪声的观测值为 $\tilde{g}(w,\eta) = w - x = (w - \mathbb{E}[X]) + (\mathbb{E}[X] - x) = g(w) + \eta$
3. 根据[[随机近似理论 Stochasted Approximation Theory#Robins-Monro (RM) 算法|RM算法]]求解，即$w_{k+1} = w_{k} - \alpha_{k} \tilde{g}(w_{k},\eta_{k}) = w_{k} - \alpha_{k}(w_{k} - x_{k})$

---
## TD basic


---
## Sarsa

---
## Q-Learning

---
## on-policy和off-policy方法