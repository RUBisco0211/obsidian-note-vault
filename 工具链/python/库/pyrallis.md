---
title: pyrallis
created: 2026-01-17 20:34:56
updated: 2026-01-17 20:40:13
---

`pyrallis` 是一个**基于 dataclass 的 Python 命令行参数解析库**，可以把命令行参数**自动映射**到 dataclass，对科研/训练脚本特别友好（类似 `argparse + dataclass`，但更简洁）。

## 安装

```bash
pip install pyrallis
```

## 基本用法

### 定义参数（用 dataclass）

```python
from dataclasses import dataclass
import pyrallis

@dataclass
class Args:
    lr: float = 1e-3
    batch_size: int = 32
    epochs: int = 10
    use_cuda: bool = False
```

### 解析命令行参数

```python
def main():
    args = pyrallis.parse(Args)
    print(args)

if __name__ == "__main__":
    main()
```

### 运行方式

```bash
python train.py --lr 0.0005 --batch_size 64 --use_cuda
```

输出：

```text
Args(lr=0.0005, batch_size=64, epochs=10, use_cuda=True)
```

✅ **特点**

- 不用手写 `argparse`
    
- 自动类型转换
    
- 自动生成 `--help`
    



## 自动生成 `--help`

```bash
python train.py --help
```

示例输出：

```text
--lr FLOAT
--batch_size INT
--epochs INT
--use_cuda BOOL
```



## 嵌套参数

适合深度学习训练配置。

```python
from dataclasses import dataclass

@dataclass
class OptimConfig:
    lr: float = 1e-3
    weight_decay: float = 1e-4

@dataclass
class TrainConfig:
    batch_size: int = 32
    epochs: int = 20

@dataclass
class Args:
    optim: OptimConfig = OptimConfig()
    train: TrainConfig = TrainConfig()
```

### 命令行调用

```bash
python train.py \
  --optim.lr 0.0001 \
  --train.batch_size 64
```

结果：

```python
args.optim.lr == 0.0001
args.train.batch_size == 64
```



## 支持 YAML / JSON 配置文件

### YAML 配置文件 `config.yaml`

```yaml
lr: 0.0003
batch_size: 128
epochs: 50
use_cuda: true
```

### 加载配置文件

```python
args = pyrallis.parse(Args, config_path="config.yaml")
```

### 命令行覆盖配置文件

```bash
python train.py --config config.yaml --lr 0.001
```

👉 **优先级**：命令行 > 配置文件 > 默认值

## List / Optional / Enum 支持

```python
from typing import List, Optional
from enum import Enum

class Mode(Enum):
    train = "train"
    eval = "eval"

@dataclass
class Args:
    layers: List[int] = (64, 128, 256)
    dropout: Optional[float] = None
    mode: Mode = Mode.train
```

```bash
python train.py --layers 128 256 512 --mode eval
```



## 自动保存配置

```python
args = pyrallis.parse(Args)
pyrallis.dump(args, "used_config.yaml")
```

生成的 YAML 可直接复用。
