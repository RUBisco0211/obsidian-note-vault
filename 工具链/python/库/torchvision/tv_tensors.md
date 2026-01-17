---
title: tv_tensors
created: 2025-12-26 15:25:52
updated: 2026-01-16 21:51:05
---
## `torch.Tensor`的一些子类

```python
from torchvision import tv_tensors
```

## `BoundingBoxes`

```python
img = Image.open(os.path.join(img_dir, file_name_list[0] + ".jpg"))
boxes = []

for line in (
    open(os.path.join(label_dir, file_name_list[0] + ".txt"), "r").read().split("\n")
):
    if line == "":
        continue
    line = line.split(" ")[1:]
    line = list(map(float, line))
    # 把bbox数据还原为原始尺寸
    line[0] *= img.width
    line[1] *= img.height
    line[2] *= img.width
    line[3] *= img.height
    boxes.append(line)
    
boxes = tv_tensors.BoundingBoxes(
    boxes, format="CXCYWH", canvas_size=(img.width, img.height)
)
```

转换为`BoundigBoxes`之后，可以用[[transforms#transform.v2]]中的api实现候选框数据与图片数据的同步变换

```python
import torchvision.transforms.v2 as T

# ...

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

### 参数

- `data`: 候选框数据，需要还原为原始画幅尺寸
```python
[
	[x, y, w, h],
	[x, y, w, h],
	...
]
```
- `format`: 定义`data`的格式：
	- `XYXY`: 四点式
	- `XYWH`: 左上角坐标 + 宽高,
	- `CXCYWH`: 中心点坐标 + 宽高
- `canvas_size`: img的宽高tuple
## `Mask`


## `Image`



