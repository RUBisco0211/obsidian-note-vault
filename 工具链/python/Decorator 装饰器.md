## 作用于函数

例：实现`Timer`类装饰器来计算一个函数运行的时间
### 例1
```python
class Timer:
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwds):
        start = time.time()
        result = self.func(*args, **kwds)
        print(f"{self.func.__name__} took {time.time() - start} seconds")
        return result


@Timer
def add(a, b):
    return a + b


# 等价于
add = Timer(add)
```
### 例2
```python
class Timer:
    def __init__(self, prefix=""):
        self.prefix = prefix

    def __call__(self, func):
        def wrapper(*args, **kwargs):
            start = time.time()
            result = func(*args, **kwargs)
            print(f"{self.prefix} {func.__name__} took {time.time() - start} seconds")
            return result

        return wrapper


@Timer(prefix="Running")
def add(a, b):
    return a + b


# 等价于
add = Timer(prefix="Running")(add)
```

## 作用于类

例：修改类的`__str__`方法来格式化输出类对象
### 例1
```python
def add_str(cls):
    def __str__(self):
        return str(self.__dict__)

    cls.__str__ = __str__
    return cls


@add_str
class Obj:
    def __init__(self, a, b):
        self.a = a
        self.b = b


# 等价于
Obj = add_str(Obj)

print(Obj(1, 2))
```

## 万能公式 
```python
@decorator
def target:
	pass

# 或

@decorator
class target:
	pass

# 等价于
target = decorator(target)
```