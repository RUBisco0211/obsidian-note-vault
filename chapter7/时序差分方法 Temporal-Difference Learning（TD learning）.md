## 引入

考虑更复杂的问题：$w = \mathbb{E}[R + \gamma v(X)]$，其中$R, X$为随机变量，$\{ x \},\{ r \}$为对应的采样值，$\gamma$为常数
1. 令 $g(w) = w - \mathbb{E}[R + \gamma v(X)]$ ，$\tilde{g}(w,\eta) = w - (r + \gamma v(x)) = (w - \mathbb{E}[R + \gamma v(X)]) + (\mathbb{E}[R + \gamma v(X)] - [r + \gamma v(x)]) = g(w) + \eta$
2. 使用[[随机近似理论 Stochasted Approximation Theory#Robins-Monro (RM) 算法|RM算法]]求解：$w_{k+1} = w_{k} - \alpha_{k} \tilde{g}(w_{k},\eta_{k}) = w_{k} - \alpha_{k}[w_{k} - (r_{k} + \gamma v(x_{k}))]$

令其中 $R$ 为[[基本概念#奖励 Reward|奖励]]，$\gamma$ 为[[基本概念#折扣率 Discount Rate|折扣率]]

---
## TD Basic

基于数据，学习一个给定策略的状态值State Value

**数据**：通过策略$\pi$得到的trajectory $(s_{0},r_{1},s_{1},\dots,s_{t},r_{t+1},s_{t+1},\dots)$或$\{ (s_{t},r_{t+1},s_{t+1}) \}_{t}$

TD-Basic算法：
$$
\begin{align*}
v_{t+1}(s_{t})&=v_{t}(s_{t})-\alpha_{t}(s_{t})\left[v_{t}(s_{t})-[r_{t+1}+\gamma v_{t} (s_{t+1})]\right], &(1) \\
v_{t+1}(s)&=v_{t}(s),\quad\forall s\neq s_{t}. &(2)
\end{align*}
$$
其中$s$为状态空间中的任意状态，$v_{t}(s)$为 $t$ 时刻对 $v_{\pi}(s)$ 的一个估计值；$s_{t}$代表一个trajectory中 $t$ 时刻访问到的状态

- 在 $t$ 时，仅有被访问的状态 $s_{t}$ 的状态值 $v_{t}(s_{t})$ 被更新，其他未被访问的状态值不变
- 式(2)中的更新往往被忽略

> [!warning] 注意
> TD Learning的特点就是**边走边估计**，因此$v$和$s$的下标$t$可以看作同一个变量

![PixPin_2025-10-12_15-50-55.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1760255466942_PixPin_2025-10-12_15-50-55.png)

- 其中 $\bar{v}_{t} = r_{t+1} + \gamma v(s_{t+1})$ 称为TD target，代表 $v(s_{t})$ 希望被更新的方法
- $\delta_{t} = v(s_{t}) - [r_{t+1} + \gamma v(s_{t+1})]= v(s_{t}) - \bar{v}_{t}$ 称为 TD error

### TD target

$\bar{v}_{t} = r_{t+1} + \gamma v(s_{t+1})$ 是算法希望 $v_{t}(s_{t})$ 接近的值，算法希望 $v_{t+1}(s_{t})$ 比 $v_{t}(s_{t})$ 更接近 $\bar{v}_{t}$

> [!info] 证明：压缩映射原理
> ![PixPin_2025-10-12_16-01-44.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1760256125147_PixPin_2025-10-12_16-01-44.png)



---
## Sarsa

---
## Q-Learning

---
## on-policy和off-policy方法