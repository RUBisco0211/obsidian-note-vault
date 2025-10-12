## 引入：从非增量到增量的方法

假设有一随机变量$X$，目标是通过收集独立同分布的观测值$\{x_{i}\}_{i=1}^N$来估计$\mathbb{E}[X]$，有以下两种方法
- non-incremental 非增量方法：$\mathbb{E}[X] \approx \bar{x}:=\frac{1}{N} \sum_{i=1}^N x_i$，需要在收集完所有观测值才能计算，效率低
- incremental 增量方法：令
$$ w_{k+1} \doteq \frac{1}{k} \sum_{i=1}^k x_i, \quad k=1,2, \ldots $$
则
$$
w_k=\frac{1}{k-1} \sum_{i=1}^{k-1} x_i, \quad k=2,3, \ldots
$$
故
$$
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
### RM算法收敛的条件

若满足
- $0 < c_{1} \leq \nabla_{w}g(w) \leq c_{2},\forall w$，即$g(w)$递增且导函数有上界
- $\sum_{k=1}^{\infty} a_{k} = \infty$ 且 $\sum_{k=1}^{\infty} a_{k}^2 < \infty$，确保任意选取$w_{1}$都能收敛
- $\mathbb{E}[\eta_{k} | \mathcal{H}_{k}] = 0$ 且 $\mathbb{E}[\eta^2_{k} | \mathcal{H}_{k}] < \infty$，常见情况为$\{\eta_{k}\}$为独立同分布
	其中$\mathcal{H}_{k} = \{ w_{k},w_{k-1},\dots\}$ 
则$w_{k}$以1的概率收敛到 $w^*$ ，其中$g(w^*) = 0$

---
## 随机梯度下降 Stochastic Gradient Descent (SGD) 

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
- 方法3：**随机梯度下降SGD**：$${w}_{k + 1} = {w}_{k} - {\alpha }_{k}{\nabla }_{w}f\left( {{w}_{k},{x}_{k}}\right) ,$$直接使用一次采样的梯度值进行更新（BGD中$n=1$时为SGD）

SGD实际上是使用随机梯度$\nabla_{w} f(w_{k},x_{k})$来近似梯度$$