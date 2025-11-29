## 所有模型的基类

- 需要实现`__init__`方法（构造函数）和`forward`前向传播方法
- `Module`内部实现了`__call__`方法并调用了用户实现的`forward`函数，故可以实现**自动微分**

```python
import torch.nn as nn
import torch.nn.functional as F

class Model(nn.Module):
    def __init__(self) -> None:
        super().__init__()
        self.conv1 = nn.Conv2d(1, 20, 5)
        self.conv2 = nn.Conv2d(20, 20, 5)

    def forward(self, x):
        x = F.relu(self.conv1(x))
        return F.relu(self.conv2(x))
```

---
## 方法

- `add_module(name, module)`: 可以通过`name`作为属性名来访问对应的子模块
- `cuda(device)`: 把模型部署到gpu上
- `train()`: 设置模型为训练模式，有**随机失活（dropout）**
- `eval()`: 设置模型为测试模式，无随机失活
- `state_dict()`: 保存模型参数，返回`dict`
- `load_state_dict(state_dict, strict=True)`: 从`state_dict`中加载模型参数
- `requires_grad_(requires_grad=True)`: 控制是否需要进行**梯度下降**来更新参数
- ...

---
## 保存模型参数

- `torch.save(state_dict, path)`: 保存模型参数到文件
- `torch.load(path)`: 加载参数文件

```python
import torch
import torch.nn as nn

model = MyModel()

# 保存模型参数
torch.save(model.state_dict(), 'model.pth')

# 加载模型参数
new_model = MyModel()
new_model.load_state_dict(torch.load('model.pth'))

modified_model = ModifiedModel()
result = modified_model.load_state_dict(torch.load('model.pth'), strict=False)
print("Missing keys:", result.missing_keys)  # 输出缺少的键
print("Unexpected keys:", result.unexpected_keys)  # 输出多余的键
```