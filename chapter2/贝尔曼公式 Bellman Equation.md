## 引入：计算Return(Discounted)的方法

### 法1：根据定义

![PixPin_2025-07-06_08-55-44.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1751763361124_PixPin_2025-07-06_08-55-44.png)

### 法2：==自举 Bootstraping==

![PixPin_2025-07-06_08-58-03.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1751763495776_PixPin_2025-07-06_08-58-03.png)

从一个状态出发得到的return依赖于从其他状态出发得到的return
写为矩阵向量形式：

![PixPin_2025-07-06_09-01-16.png](https://cloudflare-imgbed-1v8.pages.dev/file/img/note/rl/1/1751763699218_PixPin_2025-07-06_09-01-16.png)

即为特定条件的确定性问题上的**贝尔曼公式**
## 状态值 State value



## 贝尔曼公式

### 推导

### 矩阵向量形式

### 求解状态值

## 动作值 Action value
