---
layout:     post
title:      数据分析之路-Python篇（一）：NumPy
subtitle:   数据分析工具学习踩坑记录
date:       2020-01-12
author:     Chi,Guyong
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - Blog
    - 数据分析
    - Python
---

# 前言
在数据分析得工作中，对于数据的处理、清洗是占据了80%的工作的，而如果掌握了 Python 这门语言，借助其丰富的第三方库可以帮助我们以极高的效率来完成对数据预处理的工作。

我在[极客时间](https://time.geekbang.org/)上学习了陈旸的[《数据分析实战45讲》](https://time.geekbang.org/column/intro/147)，数据分析之路的前几篇文章就将记录下有关Python的学习心得。

# 工具说明
我使用的工作本是 Mackbook Pro，因此所有的软件环境和相关配置操作均以 OS X 操作系统为准。

## 编辑器
编辑器沿用之前的习惯，Mac 下的 VSCode 是万精油，连 Java 都能搞，Python 不在话下。简单安装了 Python 的几个常用插件就可以上手使用了。

我这里安装了：
- Python
- Visual Studio IntelliCode (需要配置 Python 路径)

## Python 版本
Mac 默认安装了 Python 2.7 版本，但考虑到 3.x 已经非常成熟，我还是通过 Brew 安装了 3.7 版本。在命令行中为区分原 python 命令，使用python3 映射到 3.7 的解释器。相应的 pip 命令则为 pip3。

```
$ pip install ...
```

# NumPy
## 第一段代码
通过如下命令安装 NumPy：
```
$ pip install numpy
```

脚本中使用如下语法引入 NumPy
```python
import numpy as np
```

按照课程学习内容，首先先创建两个数组：
```python
a = np.array([1,2,3]) # 构建一维数组，秩为1
b = np.array([[1,2,3,4],[4,5,6,7],[7,8,9,0]]) # 构建二维数组，秩为2
```
其中，a 为一维数组，b 为二维数组，数组的维数称为秩。

随后进行指定位置赋值，代码如下：
```python
b[2,1] = 10
```
意思为将b数组中第3行，第2列赋值为10（数组下标从0开始计数）。

随后我们打印下结果
```python
print(a)
print(a.shape)
print(b.shape)
print(a.dtype)
print(b.dtype)
print(b)
```
**此处需要注意， 原教程中直接是 print a，在 python3 中需要以函数传值方式进行打印调用。**

运行后得到结果：
```
[1 2 3]
(3,)
(3, 4)
int64
int64
[[ 1  2  3  4]
 [ 4  5  6  7]
 [ 7 10  9  0]]
```
打印 a，则按参数原样进行显示。 

打印 a.shape 则表示打印数组的维度，对于矩阵，n 行 m 列，例如 b 就是一个 3*4 的矩阵。 

打印 a.dtype 则表示打印数组类型，int64 表示整数（-9223372036854775808 to 9223372036854775807），具体类型参考表点击[这里查看](https://www.runoob.com/numpy/numpy-dtype.html)。

最后打印的 b 与一开始初始化的结果不同，因为我们对第3行、第2列进行了赋值10的操作，因此显示了如上结果。

以上，完整代码如下：
```python
import numpy as np
a = np.array([1,2,3]) # 构建一维数组，秩为1
b = np.array([[1,2,3,4],[4,5,6,7],[7,8,9,0]]) # 构建二维数组，秩为2
b[2,1] = 10 # 将b数组中第3行，第2列赋值为10
print(a)
print(a.shape)
print(b.shape)
print(a.dtype)
print(b.dtype)
print(b)
```

## 定义结构数组
上段的代码创建的数组是以数字作为下标的，如果希望生成一个具有属性含义的下标数组，则需要通过定义结构数组来实现，代码如下：
```python
import numpy as np
columns = np.dtype({
    'names': ['name', 'age', 'chinese', 'math', 'english'],
    'formats': ['S32', 'i', 'i', 'i', 'f']
})

datas = np.array([
    ("Zhangfei", 14, 75, 100, 90),
    ("Guanyu", 24, 85, 96, 88.5),
    ("Zhaoyun", 27, 85, 92, 95.5),
    ("Huangzhong", 40, 88, 91, 100),
    ("Machao", 28, 91, 77, 80)
], dtype = columns)

print(datas)
```
先通过 np.dtype 来定义列名和列的数据类型，随后在创建数组时，将这个 columns 赋值给第二个参数 dtype。打印结果如下：
```
[(b'Zhangfei', 14, 75, 100,  90. ) (b'Guanyu', 24, 85,  96,  88.5)
 (b'Zhaoyun', 27, 85,  92,  95.5) (b'Huangzhong', 40, 88,  91, 100. )
 (b'Machao', 28, 91,  77,  80. )]
```
从结果上并未体现列名，那么结构数组该怎么用呢？例如我现在需要求所有人的年龄平均值。
那么就可以编写如下代码：
```python
ages = datas[:]['age']
print(ages)
print(np.mean(ages))
```
可以得到：
```
[14 24 27 40 28]
26.6
```
由此得出，```ages = datas[:]['age'] ``` 可以将原数据中的 age 列单独赋值到一个变量，np.mean 即求平均值。类似的操作还有求最大值、最小值和中位数等。示例代码如下：
```python
ages = datas[:]['age']
print(ages)
print(np.mean(ages)) # 求平均值

chineses = datas[:]['chinese']
print(np.max(chineses)) # 求最大值

maths = datas[:]['math']
print(np.min(maths)) # 求最小值

englishs = datas[:]['english']
print(np.median(englishs)) # 求中位数
```
结果输出：
```
26.6
91
77
90.0
```

那么，如果想要给每个人计算一个总分呢？
```python
allScore = np.sum([chineses, maths, englishs], axis = 0)
print(allScore)
```
结果输出：
```
[265.  269.5 272.5 279.  248. ]
```

以上是对结果数组的构建和一些简单的数学统计运算。本段完整代码如下：
```python
import numpy as np
columns = np.dtype({
    'names': ['name', 'age', 'chinese', 'math', 'english'],
    'formats': ['S32', 'i', 'i', 'i', 'f']
})

datas = np.array([
    ("Zhangfei", 14, 75, 100, 90),
    ("Guanyu", 24, 85, 96, 88.5),
    ("Zhaoyun", 27, 85, 92, 95.5),
    ("Huangzhong", 40, 88, 91, 100),
    ("Machao", 28, 91, 77, 80)
], dtype = columns)

print(datas)

ages = datas[:]['age']
print(ages)
print(np.mean(ages)) # 求平均值

chineses = datas[:]['chinese']
print(np.max(chineses)) # 求最大值

maths = datas[:]['math']
print(np.min(maths)) # 求最小值

englishs = datas[:]['english']
print(np.median(englishs)) # 求中位数

allScore = np.sum([chineses, maths, englishs], axis = 0)
print(allScore)
```
结果输出：
```
[(b'Zhangfei', 14, 75, 100,  90. ) (b'Guanyu', 24, 85,  96,  88.5)
 (b'Zhaoyun', 27, 85,  92,  95.5) (b'Huangzhong', 40, 88,  91, 100. )
 (b'Machao', 28, 91,  77,  80. )]
[14 24 27 40 28]
26.6
91
77
90.0
[265.  269.5 272.5 279.  248. ]
```

## ufunc 运算
ufunc 是 universal function 的缩写，得益于其基本是 C 语言实现的，运算功能和性能非常强大。从上一段的练习中可以看出，numpy 的数组是可以直接进行矩阵运算的，那么其所支持的运算就是利用 ufunc 实现的，包括：
- 连续数组的创建
- 算数运算
- 统计函数
- NumPy 排序等等