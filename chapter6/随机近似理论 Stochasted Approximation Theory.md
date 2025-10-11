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
 
  ---
## 随机梯度下降 Stochastic Gradient Descent (SGD) 

## SGD BGD MBGD