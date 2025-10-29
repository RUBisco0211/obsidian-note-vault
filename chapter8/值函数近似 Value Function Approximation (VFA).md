## 从离散表示到连续表示

之前的方法中，$q(s,a)$都是由表格的离散形式表示

![PixPin_2025-10-26_16-59-09.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761469160746_PixPin_2025-10-26_16-59-09.png)

- 优势：直观、简洁
- 劣势：难以处理巨大的状态空间或连续的状态或动作空间（**存储和泛化能力**）

使用值函数近似的优势：
- 只需要存储函数参数，大大减少存储空间
- **仅需要访问部分状态数据即可对未访问的状态值进行估计**，泛化能力大幅提升

---
## 使用VFA的状态值估计

$v_{\pi}(s), \hat{v}(s,w)$是**真状态值函数**及其的一个估计，$w$是估计函数的参数
目标：找到最优的参数$w$让$\hat{v}(s,w)$能尽可能地接近$v_{\pi}(s)$

### 目标函数定义
$$J(w) = \mathbb{E}[(v_{\pi}(S) - \hat{v}(S,w)) ^2]$$
其中 $S \in \mathcal{S}$ 为一随机变量
要求解这个优化问题，需要先知道$S$的概率分布

### 稳态分布 stationary distribution 

对一个马尔科夫链来说，稳态分布是在经过转移后仍然保持不变的概率分布
直观上讲，在平稳状态下，$d_{\pi}(s)$是agent处于状态$s$的概率

令 $\{ d_{\pi}(s) \}_{s \in \mathcal{S}}$ 为策略$\pi$下的Markov process的**稳态分布**，$d_{\pi}(s) \ge 0$且$\sum_{s \in \mathcal{S}} d_{\pi}(s) = 1$，则
$$
J(w) = \sum_{s\in \mathcal{S}} d_{\pi}(s)(v_{\pi}(s) - \hat{v}(s,w))^2
$$
稳态分布可以由如下步骤得到
![PixPin_2025-10-29_20-17-20.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761740333223_PixPin_2025-10-29_20-17-20.png)

- 给定一个策略（一般为探索性的软策略）并让智能体按照这个策略运行
- 在由$\pi$生成的一个很长的episode中，记 $n_{\pi}(s)$ 为状态 $s$ 被访问的次数
- 则$$d_{\pi}(s) = \frac{n_{\pi}(s)}{\sum_{}}$$


---
## 使用VFA的Sarsa


---
## 使用VFA的Q-Learning

---
## Deep Q-Learning (DQN)

---
## 在强化学习中引入神经网络

