## torchvison提供的数据集

使用数据转换器[`torchvision.transforms`](transforms.md)

```python
import torch
from torch.utils.data import Dataset
from torchvision import datasets
from torchvision.transforms import ToTensor
import matplotlib.pyplot as plt


training_data = datasets.FashionMNIST(
    root="data",
    train=True,
    download=True,
    transform=ToTensor()
)

test_data = datasets.FashionMNIST(
    root="data",
    train=False,
    download=True,
    transform=ToTensor()
)
```
---
## 自定义

继承`torch.utils.data.Dataset`抽象类，实现 `__init__`, `__len__`,`__getitem__`方法

``` python
import os
import pandas as pd
from torchvision.io import read_image
import torch
from torch.utils.data import Dataset

class CustomImageDataset(Dataset):
    def __init__(self, annotations_file, img_dir, transform=None, target_transform=None):
        self.img_labels = pd.read_csv(annotations_file)
        self.img_dir = img_dir
        self.transform = transform
        self.target_transform = target_transform

    def __len__(self): # 样本容量
        return len(self.img_labels)

    def __getitem__(self, idx): # 获取第 idx 个样本数据的方法
        img_path = os.path.join(self.img_dir, self.img_labels.iloc[idx, 0])
        image = read_image(img_path)
        label = self.img_labels.iloc[idx, 1]
        if self.transform:
            image = self.transform(image)
        if self.target_transform:
            label = self.target_transform(label)
        return image, label
```
---
## 划分数据集

推荐使用`random_split`函数，不推荐手动对数据进行切片

```python
from torch.utils.data import random_split
# 划分原训练集为新训练集和验证集
# split = (0.7, 0.3)
train_size, val_size = int(split[0] * len(train_dataset)), int(split[1] * len(train_dataset))

train_dataset, val_dataset = random_split(train_dataset, [train_size, val_size])
```

## 与[`DataLoader`](DataLoader.md)配合