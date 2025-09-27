## 值迭代 value iteration (VI)

基本思想：$v_{k+1} = \max_{\pi}(r_{\pi} + \gamma P_{\pi} v_{k}), k=1,2,3\dots$
### 算法思路（矩阵向量形式）

每次迭代包括两个步骤
- 策略更新（policy update）：$\pi_{k+1} = \arg \max_{\pi}(r_{\pi} + \gamma P_{\pi} v_{k})$，用状态值$v_{k}$计算策略$\pi_{k+1}$
- 值更新（value update）：$v_{k+1} = r_{\pi} + \gamma P_{\pi_{k+1}} v_{k}$，用$\pi_{k+1}$计算新的状态值$v_{k+1}$

### 算法实现（分量形式）

- 初始化：$p(r | s,a), p(s' | s,a)$已知，给出初始状态值$v_{0}$
- 目标：求出使状态值$V(s)$最大的最优策略$\pi^*$
- 迭代循环（第$k$次，当 $\|v_{k} -v_{k-1} \| > threshold$ 时）：
	- 对于每个$s \in \mathcal{S}$：
		- 对于$s$上的每个动作$a \in \mathcal{A}(s)$：
			- 计算动作值$q_{k}(s,a) = \sum_r p(r|s, a) r + \gamma \sum_{s'} p(s' |s, a) v_{k}(s')$
	- 求出动作值最大的$a^*(s) = \arg \max_{a} q_{k}(s,a)$
	- 策略更新：$\pi_{k+1} (a \mid s)= \begin{cases} 1 \quad a = a^*_{k} \\ 0 \quad a \not = a^*_{k}\end{cases}$
	- 值更新：$v_{k+1}(s) = \max_{a \in \mathcal{A}(s)} q_k(a,s)$

---
## 策略迭代 policy iteration (PI)

### 算法思路（矩阵向量形式）

每次迭代包括两个步骤
- 策略评估（policy evaluation）：计算$v_{\pi_{k}} = r_{\pi_{k}} + \gamma P_{\pi_{k}} v_{\pi_{k}}$（贝尔曼公式），用策略计算当前状态值$v_{\pi_{k}}$
- 策略提升（policy improvement）：计算$\pi_{k+1} = \arg \max_{a}(r_{\pi} + \gamma P_{\pi} v_{\pi_{k}})$，用$v_{\pi_{k}}$计算新的更优的策略（可证明）
### 算法实现（分量形式）

- 初始化：$p(r | s,a), p(s' | s,a)$已知，给出初始策略$\pi_{0}$
- 目标：求出使状态值$V(s)$最大的最优策略$\pi^*$
- 迭代循环（第$k$次，当 $\|v_{k} -v_{k-1} \| > threshold$ 时）：
	- 策略评估（[[贝尔曼公式 Bellman Equation#迭代法]]）：
	- 初始化：给出初始状态$v_{\pi_{k}}^{(0)}$
	- 迭代循环（第$j$次，当$\|v_{\pi_{k}}^{(k)} - v_{\pi_{k}}^{(k-1)} \| > threshold$时）：
		- 对于每个$s \in \mathcal{S}$：
			- 迭代：$v_{\pi_{k}}^{(j+1)}(s) = \sum_{a}\pi_{k}(a|s) \left[ \sum_{r}p(r | s,a)r + \gamma \sum_{r'} p(s' | s,a) v_{\pi_{k}}^{(j)}(s') \right]$
	- 策略提升：
	- 对于每个$s \in \mathcal{S}$:
		- 对于每个$a \in \mathcal{A}(s)$：
			- 计算$q_{\pi_{k}}(s,a) =\sum_{r}p(r | s,a)r + \gamma \sum_{r'} p(s' | s,a) v_{\pi_{k}}(s')$
		- 求出动作值最大的$a_{k}^*(s) = \arg \max_{a} q_{\pi_{k}}(s,a)$
	- 计算$\pi_{k+1} (a \mid s)= \begin{cases} 1 \quad a = a^*_{k} \\ 0 \quad a \not = a^*_{k}\end{cases}$

---
## 截断策略迭代 truncated policy iteration

VI和PI的一般化形式
### 策略迭代和值迭代的对比

策略迭代：$\pi_{0} \xrightarrow{PE} v_{\pi_{0}} \xrightarrow{PI} \pi_{1} \xrightarrow{PE}v_{\pi_{1}} \xrightarrow{PI} \dots$
值迭代：$u_{0} \xrightarrow{PU} \pi_{1}' \xrightarrow{VU} u_{1} \xrightarrow{PU} \pi_{2}' \xrightarrow{VU} \dots$

|            | 策略迭代                                                      | 值迭代                                                  | Comments                                                                       |
| :--------- | :-------------------------------------------------------- | :--------------------------------------------------- | :----------------------------------------------------------------------------- |
| 1) Policy: | $\pi_0$                                                   | N/A                                                  |                                                                                |
| 2) Value:  | $v_{\pi_0} = r_{\pi_0} + \gamma P_{\pi_0} v_{\pi_0}$      | $v_0 := v_{\pi_0}$                                   | 令值迭代的$v_{0} := v_{\pi_{0}}$                                                    |
| 3) Policy: | $\pi_1 = \arg\max_{\pi} (r_\pi + \gamma P_\pi v_{\pi_0})$ | $\pi_1 = \arg\max_{\pi} (r_\pi + \gamma P_\pi v_0)$  | 两个策略$\pi_{1}$相同                                                                |
| 4) Value:  | $v_{\pi_1} = r_{\pi_1} + \gamma P_{\pi_1} v_{\pi_1}$      | $v_1 = r_{\pi_1} + \gamma P_{\pi_1} v_0$             | 策略迭代中迭代法求解$v_{\pi_{1}}$，令初值为$v_{0}$迭代求解，可知$v_{\pi_{1}} \geq v_{1} \geq v_{0}$） |
| 5) Policy: | $\pi_2 = \arg\max_{\pi} (r_\pi + \gamma P_\pi v_{\pi_1})$ | $\pi'_2 = \arg\max_{\pi} (r_\pi + \gamma P_\pi v_1)$ |                                                                                |
| ...        | ...                                                       | ...                                                  | ...                                                                            |

### 截断策略迭代的思路

在上述表格**第四步**中，策略迭代需要使用迭代法求解贝尔曼公式
![PixPin_2025-09-27_10-21-08.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1758939694893_PixPin_2025-09-27_10-21-08.png)

**截断策略迭代**：在迭代到第 $j$ 次时停止（$j$人为指定），取 $\bar{v_{1}} := v_{\pi_{1}}^{(j)}$ 作为结果
当$j = 1$时退化为值迭代法；$j \rightarrow \infty$ 时为策略迭代法

![PixPin_2025-09-27_10-34-50.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1758940511044_PixPin_2025-09-27_10-34-50.png)


