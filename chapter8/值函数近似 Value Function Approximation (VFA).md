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

令$v_{\pi}(s), \hat{v}(s,w)$是**真状态值函数**及其的一个估计，$w$是估计函数的参数
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
稳态分布的近似值可以由如下步骤得到
![PixPin_2025-10-29_20-17-20.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761740333223_PixPin_2025-10-29_20-17-20.png)

- 给定一个策略（一般为探索性的软策略）并让智能体按照这个策略运行
- 在由$\pi$生成的一个很长的episode中，记 $n_{\pi}(s)$ 为状态 $s$ 被访问的次数
- 则$$d_{\pi}(s) \approx\frac{n_{\pi}(s)}{\sum_{s' \in \mathcal{S}  }n_{\pi}(s')}$$
$d_{\pi}(s)$ 的值也可以由以下等式计算得到
$$
d_{\pi}^T = d_{\pi}^T P_{\pi}
$$
其中$P_{\pi}$为策略$\pi$下MDP的[[贝尔曼公式 Bellman Equation#^StateTransferMatrix|状态转移矩阵]]，$[P_{\pi}]_{ij} = p_{\pi}\{ s'=s_{j} | s=s_{i} \}$

### 优化目标函数的方法

使用**梯度下降法**进行优化：
$$
w_{k+1} = w_{k} - \alpha_{k} \nabla_{w} J(w_{k})
$$
其中梯度为：
$$
\begin{align}
\nabla_{w} J(w) &= \nabla_{w} \mathbb{E}[(v_{\pi}(S) - \hat{v}_{\pi}(S,w))^2] \\ \\
&= \mathbb{E} [ \nabla_{w} (v_{\pi}(S) - \hat{v}_{\pi}(S,w))^2 ] \\ \\
&= -2\mathbb{E} [\nabla_{w} \hat{v}(S,w)(v_{\pi}(S) - \hat{v}_{\pi}(S,w))] \\  \\
\end{align}
$$
真实梯度中需要计算期望，可以使用**随机梯度下降**
即用 $\nabla_{w} \hat{v}(s_{t},w_{t}) (v_{\pi}(s_{t}) - \hat{v}(s_{t},w_{t}))$ 代替 $\mathbb{E} [\nabla_{w} \hat{v}(S,w)(v_{\pi}(S) - \hat{v}_{\pi}(S,w))]$
则优化的迭代公式为：
$${w}_{t + 1} = {w}_{t} + {\alpha }_{t}\left( {{v}_{\pi }\left( {s}_{t}\right)  - \widehat{v}\left( {{s}_{t},{w}_{t}}\right) }\right) {\nabla }_{w}\widehat{v}\left( {{s}_{t},{w}_{t}}\right) $$
其中 $2\alpha_{k}$ 被合并为 $\alpha_{k}$ 

---
## 使用VFA的Sarsa


---
## 使用VFA的Q-Learning

---
## Deep Q-Learning (DQN)

---
## 在强化学习中引入神经网络

