---
title: 使用Dataset创建`DataLoader`
created: 2025-12-26 15:25:52
updated: 2026-01-16 21:49:39
---
# 使用[Dataset](Dataset.md)创建`DataLoader`

```python
from torch.utils.data import DataLoader
from torchvision import  datasets

# 加载数据
train_dataset = datasets.CIFAR10(root=root, train=True, download=True, transform=transform)
test_dataset = datasets.CIFAR10(root=root, train=False, download=True, transform=transform)

# 创建数据集加载器
# shuffle控制每个epoch训练轮次中是否要打乱数据
train_dataloader = DataLoader(training_data, batch_size=64, shuffle=True)
test_dataloader = DataLoader(test_data, batch_size=64, shuffle=True)
```
---
# 迭代

`DataLoader`可迭代，其元素为对应`Dataset`的`__getitem__`方法返回的数据，但被整合为`batch_size`大小的`Tensor`，即其第一维度大小为`batch_size`

```python
dataloader = DataLoader(dataset, batch_size=10, shuffle=True)

# batch_size = 10
for batch in dataloader:
    inputs, targets = batch
    print("Input shape:", inputs.shape)  # 输出: torch.Size([10, 3, 32, 32])
    print("Target shape:", targets.shape)  # 输出: torch.Size([10])
```
---

使用[`tqdm`](tqdm.md)迭代

```python
from tqdm import tqdm

for img, label in tqdm(train_loader, desc=f"[Train] Epoch {epoch+1} / {epochs}"):
	img, label = img.to(device), label.to(device)
```


