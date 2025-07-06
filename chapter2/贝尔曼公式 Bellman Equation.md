## 引入：计算Return(Discounted)的方法

### 法1：根据定义

![PixPin_2025-07-06_08-55-44.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1751763361124_PixPin_2025-07-06_08-55-44.png)

### 法2：==自举 Bootstraping==

![PixPin_2025-07-06_08-58-03.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1751763495776_PixPin_2025-07-06_08-58-03.png)

从一个状态出发得到的return依赖于从其他状态出发得到的return
写为矩阵向量形式：

![PixPin_2025-07-06_09-01-16.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1751763699218_PixPin_2025-07-06_09-01-16.png)

即为特定条件的确定性问题上的**贝尔曼公式**

---
## 状态值 State value

考虑如下单步状态转换过程：
$$S_t\xrightarrow{A_t}R_{t+1},S_{t+1}$$
**此处$S$、$R$、$A$都为随机变量**

- $S_t \rightarrow A_t$ 由策略 $\pi(A_t=a|S_t=s)$ 决定
- $S_t,A_t \rightarrow R_{t+1}$ 由 $p(R_{t+1}=r|S_t=s,A_t=a)$ 决定
- $S_t,A_t \rightarrow S_{t+1}$ 由 $p(S_{t+1}=s'|S_t=s,A_t=a)$ 决定

推广到多步的trajectory：
$$S_t\xrightarrow{A_t}R_{t+1},S_{t+1}\xrightarrow{A_{t+1}}R_{t+2},S_{t+2}\xrightarrow{A_{t+2}}R_{t+3},\ldots$$
对应的discounted return $G_t$
$$G_t=R_{t+1}+\gamma R_{t+2}+\gamma^2R_{t+3}+\ldots$$
### 定义

状态值State value，为在策略$\pi$下，==**从状态$s$出发得到的$G_t$的期望**==
$$v_\pi(s)=\mathbb{E}[G_t|S_t=s]$$
可以表征状态的价值 
**确定性策略的状态值和**
![PixPin_2025-07-06_09-56-26.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1751767006334_PixPin_2025-07-06_09-56-26.png)

---
## 贝尔曼公式 Bellman Equation

描述不同状态的State value之间的关系
### 推导

考虑trajectory：
$$S_t \xrightarrow{A_t} R_{t+1},S_{t+1} \xrightarrow{A_{t+1}}R_{t+1},S_{t+2} \xrightarrow{A_{t+2}}R_{t+3}, \dots$$
则：
$$
\begin{aligned}
G_t &= R_{t+1}+\gamma R_{t+2}+\gamma^2 R_{t+3} + \dots, \\
 &= R_{t+1} + \gamma(R_{t+2} + \gamma R_{t+3} + \dots), \\
 &= R_{t+1} + \gamma G_{t+1}
\end{aligned}
$$
则根据State value定义，有：
$$
\begin{aligned}
v_{\pi}(s) &= \mathbb{E}[G_t | S_t = s] \\
&= \mathbb{E}[R_{t+1} + \gamma G_{t+1} | S_t = s] \\
&= \mathbb{E}[R_{t+1} | S_t = s ] + \gamma \mathbb{E}[G_{t+1} | S_t = s]
\end{aligned}
$$
即
$$
v_{\pi}(s) = \mathbb{E}[R_{t+1} | S_t = s] + \gamma 
$$
### 矩阵向量形式

### 求解状态值

## 动作值 Action value
