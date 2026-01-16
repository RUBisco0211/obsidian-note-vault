---
title: transforms
created: 2025-12-26 15:25:52
updated: 2026-01-16 22:49:47
---
## 转换管道`Compose`

```python
import torch
from torchvison import transforms， datasets

transform = transforms.Compose(
        [transforms.ToTensor(),  # 转为tensor
         transforms.Normalize((0.5, 0.5, 0.5), (0.5, 0.5, 0.5)),  # 标准化 / 均值归一化 
         ])

## 加载数据
train_dataset = datasets.CIFAR10(root=root, train=True, download=True, transform=transform)
test_dataset = datasets.CIFAR10(root=root, train=False, download=True, transform=transform)
```

## 自定义transform

只需要实现`__call__`方法使其可以被作为函数调用，即可适配`transform.Compose`

```python
import torch
from torchvision import transforms
from PIL import Image
import random

class RandomCrop:
    def __init__(self, size : tuple[int, int]):
        self.size = size

    def __call__(self, img):
        width, height = img.size
        crop_height, crop_width = self.size

        # 随机生成裁剪的左上角坐标
        top = random.randint(0, height - crop_height)
        left = random.randint(0, width - crop_width)

        # 裁剪图像
        img_cropped = img.crop((left, top, left + crop_width, top + crop_height))
        return img_cropped

## 使用自定义 Transform
transform = transforms.Compose([
    RandomCrop((100, 100)),  # 随机裁剪为 100x100
    transforms.ToTensor(),   # 转换为张量
])
```

## 自带transforms

### 作用于`PIL.image`

- `Resize()`: 调整图片尺寸
- `RandomCrop()`: 随机裁剪
-  ...
### 转换`PIL.image`与`Tensor`

- `ToTensor()`
- `ToPILImage()`
- ...
### 作用于`Tensor`

- `Normalize(mean, std)`: 均值归一化，参数为每一维的**均值**和**标准差**
- ...

## `transform.v2`(推荐)

- 允许传递多个数据并进行同步转换
- 允许嵌套数据
- 只会对`Tensor`（包括[`BoundingBoxes`](tv_tensors.md#BoundingBoxes), [`Mask`](tv_tensors.md#Mask)等）类数据进行转换，不影响数据结构

```python
import torchvision.transforms.v2 as T

## ...

transforms = [
    T.Resize((512, 256)),
    T.RandomHorizontalFlip(p=1),
    T.RandomPhotometricDistort(p=1),
]

imgs = [img]
labels = [boxes]
for transform in transforms:
    new_img, new_boxes = transform(img, boxes)
    imgs.append(new_img)
    labels.append(new_boxes)
```




