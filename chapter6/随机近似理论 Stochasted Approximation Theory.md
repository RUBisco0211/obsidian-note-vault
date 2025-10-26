## 前言

第六章**随机近似理论**作为第七章 **[[时序差分方法 Temporal-Difference Learning（TD learning）|时序差分方法]]** 的理论基础

---
## 引入：增量的均值估计问题

假设有一随机变量$X$，目标是通过收集独立同分布的观测值$\{x_{i}\}_{i=1}^N$来估计$\mathbb{E}[X]$，有以下两种方法
- non-incremental 非增量方法：$\mathbb{E}[X] \approx \bar{x}:=\frac{1}{N} \sum_{i=1}^N x_i$，需要在收集完所有观测值才能计算，效率低
- incremental 增量方法：令$$ w_{k+1} \doteq \frac{1}{k} \sum_{i=1}^k x_i, \quad k=1,2, \ldots $$则$$
w_k=\frac{1}{k-1} \sum_{i=1}^{k-1} x_i, \quad k=2,3, \ldots
$$故$$
w_{k+1}=\frac{1}{k} \sum_{i=1}^k x_i=\frac{1}{k}\left(\sum_{i=1}^{k-1} x_i+x_k\right)=\frac{1}{k}\left((k-1) w_k+x_k\right)=w_k-\frac{1}{k}\left(w_k-x_k\right)
$$
**一般式：**
$$w_{k+1}=w_k-\alpha_k\left(w_k-x_k\right)$$

---
## Robins-Monro (RM) 算法

针对**表达式未知**的函数方程 $g(w) = 0$ 使用RM算法进行求解
$$
w_{k+1} = w_{k} - a_{k} \tilde{g}(w_{k}, \eta_{k}), k=1,2,3,\dots
$$
其中
- $w$为第$k$次迭代的方程根
- $\tilde{g}(w_{k},\eta_{k}) = g(w_{k}) + \eta_{k}$是第$k$次对$g(w)$的带噪声$\eta$的观测值
- $a_{k}$为一正系数

 **哲学：没有模型就需要数据**
### 收敛条件

若满足
- $0 < c_{1} \leq \nabla_{w}g(w) \leq c_{2},\forall w$，即$g(w)$递增且导函数有上界
- $\sum_{k=1}^{\infty} a_{k} = \infty$ 且 $\sum_{k=1}^{\infty} a_{k}^2 < \infty$，确保任意选取$w_{1}$都能收敛
- $\mathbb{E}[\eta_{k} | \mathcal{H}_{k}] = 0$ 且 $\mathbb{E}[\eta^2_{k} | \mathcal{H}_{k}] < \infty$，常见情况为$\{\eta_{k}\}$为独立同分布
	其中$\mathcal{H}_{k} = \{ w_{k},w_{k-1},\dots\}$ 
则$w_{k}$以1的概率收敛到 $w^*$ ，其中$g(w^*) = 0$

> [!info] 收敛条件
> - 梯度需要有上下界
> - 步长系数$a_{k}$不能减小得太快也不能太慢
> - 噪声期望为0,方差不为0

---
## 随机梯度下降 Stochastic Gradient Descent (SGD) 

### 引入
假设要解决优化问题
$$
\min _w J(w)=\mathbb{E}[f(w, X)]
$$
$w$是优化参数，$X$为一随机变量

- 方法1：梯度下降GD：$$w_{k+1}=w_{k}-\alpha_{k}\nabla_{w}\mathbb{E}[f(w_{k},X)]=w_{k}-\alpha_{k}\mathbb{E}[\nabla_{w}f(w_{k},X)]$$仅在有模型的情况下可用
- 方法2：批次梯度下降BGD：$$ \begin{align*}
\mathbb{E}\left\lbrack  {{\nabla }_{w}f\left( {{w}_{k},X}\right) }\right\rbrack   \approx  \frac{1}{n}\mathop{\sum }\limits_{{i = 1}}^{n}{\nabla }_{w}f\left( {{w}_{k},{x}_{i}}\right) . \\ 
{w}_{k + 1} = {w}_{k} - {\alpha }_{k}\frac{1}{n}\mathop{\sum }\limits_{{i = 1}}^{n}{\nabla }_{w}f\left( {{w}_{k},{x}_{i}}\right) .
\end{align*}
$$使用数据采样估计梯度期望。每次迭代都需要采样多次，效率低下
- 方法3：**随机梯度下降SGD**：$${w}_{k + 1} = {w}_{k} - {\alpha }_{k}{\nabla }_{w}f\left( {{w}_{k},{x}_{k}}\right) ,$$直接使用**一次采样**的梯度值进行更新（BGD中$n=1$时为SGD）

SGD实际上是使用**随机梯度**$\nabla_{w} f(w_{k},x_{k})$来近似**梯度**$\mathbb{E}[\nabla_{w} f(w_{k},x_{k})]$

### 收敛性

>[!info] 收敛性证明
> 即证SGD是一个特殊的RM算法
> 
> ![PixPin_2025-10-12_11-03-00.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1760238196633_PixPin_2025-10-12_11-03-00.png)

 SGD的性质：$w_{k}$与$w^*$较远时，行为与GD类似；较近时有一定随机性

### 确定性形式的SGD

在机器学习算法中常见的SGD为一确定形式

考虑如下优化问题：$$\mathop{\min }\limits_{w}J\left( w\right)  = \frac{1}{n}\mathop{\sum }\limits_{{i = 1}}^{n}f\left( {w,{x}_{i}}\right) $$其中$\{ x_{i}\}_{i=1}^n$仅为一组数而非随机变量

求解该问题的GD方法：$${w}_{k + 1} = {w}_{k} - {\alpha }_{k}{\nabla }_{w}J\left( {w}_{k}\right)  = {w}_{k} - {\alpha }_{k}\frac{1}{n}\mathop{\sum }\limits^{n}{\nabla }_{w}f\left( {{w}_{k},{x}_{i}}\right) $$实际情况下，数据集合可能较大，每次只能取到其中的一个数据$\nabla_{w}f(w_{k},w_{k})$，则可以使用类似SGD的方法：$$w_{k+1} = w_{k} - \alpha_{k} \nabla_{w}f(w_{k},w_{k})$$此外，由于该问题不涉及随机变量，需要手动引入在集合$\{ x_{i}\}_{i=1}^n$上**均匀分布**的随机变量$X$
，**把原有的数据看作$X$的采样值**，则原问题变为：$$\mathop{\min }\limits_{w}J\left( w\right)  = \frac{1}{n}\mathop{\sum }\limits_{{i = 1}}^{n}f\left( {w,{x}_{i}}\right) = \mathbb{E}[f(w,X)]$$则该算法就是SGD算法，且在抽取$x_{k}$时应该随机抽取

---
## SGD、BGD和MBGD
$$\begin{align*}
w_{k+1}&=w_{k}-\alpha_{k}\frac{1}{n}\sum_{i=1}^{n}\nabla_{w}f(w_{k},x_{i}),\quad\mathrm{(BGD)} \\
w_{k+1}&=w_{k}-\alpha_{k}\frac{1}{m}\sum_{j\in\mathcal{I}_{k}}\nabla_{w}f(w_{k},x_{j}),\quad({MBGD}) \\
w_{k+1}&=w_k-\alpha_k\nabla_wf(w_k,x_k).\quad\mathrm{(SGD)}
\end{align*}$$
- BGD：每次都使用所有的采样数据进行迭代
- MBGD（Mini-Batch Gradient Descent）：每次仅使用采样中的一部分**独立同分布（i.i.d.）** 数据进行迭代，**有放回抽样**
- SGD：每次使用采样中的一条数据进行迭代

一般来说，MBGD的随机性比SGD更小，比BGD更大，但比BGD更灵活高效
$m=1$ 时变为SGD，$m=n$ 时变为BGD