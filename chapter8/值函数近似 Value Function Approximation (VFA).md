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
这里的 $v_{\pi}(s_{t})$ 是要求的量，用其他需要的量来代替
两种代替方式：
#### Monte Carlo方法

用Monte-Carlo方法得到的discounted return $g_{t}$ 作为 $v_{\pi}(s_{t})$ 的估计值
![PixPin_2025-10-29_21-05-36.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761743149972_PixPin_2025-10-29_21-05-36.png)
#### 时序差分（TD）方法

用TD方法中的 $r_{t+1} + \gamma \hat{v}(s_{t+1}, w_{t})$ ，**即 TD-target** 作为 $v_{\pi}(s_{t})$ 的估计值
![PixPin_2025-10-29_21-08-38.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761743331752_PixPin_2025-10-29_21-08-38.png)

![PixPin_2025-10-29_21-13-38.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761743633191_PixPin_2025-10-29_21-13-38.png)

### 估计函数 $\hat{v}(s,w)$ 的选取

#### 线性函数
$$\hat{v}(s,w) = \phi^T(s)w$$
其中 $\phi(s)$ 为**特征向量**，有多种选取方法：基于多项式的、基于傅里叶级数的

则其梯度为
$$
\nabla_{w} \hat{v}(s,w) = \phi(s)
$$
代入到TD方法的迭代式中，得到
$${w}_{t + 1} = {w}_{t} + {\alpha }_{t}\left\lbrack  {{r}_{t + 1} + \gamma {\phi }^{T}\left( {s}_{t + 1}\right) {w}_{t} - {\phi }^{T}\left( {s}_{t}\right) {w}_{t}}\right\rbrack  \phi \left( {s}_{t}\right) $$
称为**TD-Linear**

- 劣势：特征向量选取困难，需要对问题有很好的理解
- 优势：理论性质易于分析（白盒）；可以统一离散和连续状态空间下的问题（离散表示为线性函数近似的一个特殊情况）

> [!info] 离散表示是线性函数近似的一个特殊情况
> 考虑一个特殊的特征向量$\phi(s) = e_{s} \in \mathbb{R}^{|\mathcal{S}|}$，其中 $e_{s}$ 的$s$对应分量为1，其他都为0
> 则 $\hat{v}(s,w) = w(s)$，其中$w(s)$为$w$对应$s$的分量，此时$\hat{v}(s,w)$为一个向量，即为离散表示
> 把$e_{s}$代入**TD-Linear**，即为[[时序差分方法 Temporal-Difference Learning（TD learning）#TD Basic：学习状态值|TD Basic方法]]（仅更新$s_{t}$的状态值，其他不变）
> 
> ![PixPin_2025-10-29_21-48-52.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761745743674_PixPin_2025-10-29_21-48-52.png)
> 

#### 神经网络（非线性）

### 案例

**目标**：通过优化使状态值估计函数得到的曲面尽量接近真实值的曲面

![PixPin_2025-10-29_21-59-37.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1761746387728_PixPin_2025-10-29_21-59-37.png)

**数据**：500 个 episode，每个 500 步，出发时的$(s,a)$随机选择，服从均匀分布

---
## 使用VFA的Sarsa

VFASarsa的迭代公式
$${w}_{t + 1} = {w}_{t} + {\alpha }_{t}\left\lbrack  {{r}_{t + 1} + \gamma \widehat{q}\left( {{s}_{t + 1},{a}_{t + 1},{w}_{t}}\right)  - \widehat{q}\left( {{s}_{t},{a}_{t},{w}_{t}}\right) }\right\rbrack  {\nabla }_{w}\widehat{q}\left( {{s}_{t},{a}_{t},{w}_{t}}\right) $$


---
## 使用VFA的Q-Learning

---
## Deep Q-Learning (DQN)

---
## 在强化学习中引入神经网络

