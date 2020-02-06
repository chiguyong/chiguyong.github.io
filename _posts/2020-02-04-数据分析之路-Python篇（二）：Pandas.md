---
layout:     post
title:      数据分析之路-Python篇（二）：Pandas
subtitle:   数据分析工具学习踩坑记录
date:       2020-02-04
author:     Chi,Guyong
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - Blog
    - 数据分析
    - Python
---

## Pandas
在数据分析工作中，Pandas 的使用频率是很高的，一方面是因为 Pandas 提供的基础数据结构 DataFrame 与 json 的契合度很高，转换起来就很方便。另一方面，如果我们日常的数据清理工作不是很复杂的话，通常用几句 Pandas 代码就可以对数据进行规整。

Pandas 可以说是基于 NumPy 构建的含有更高级数据结构和分析能力的工具包。在 NumPy 中数据结构是围绕 ndarray 展开的。

#### 数据结构：Series 和 DataFrame
**Series 是个定长的字典序列。** Series 有两个基本属性：index 和 values。
```python
import pandas as pd 
from pandas import Series, DataFrame
x1 = Series([1,2,3,4])
x2 = Series(data=[1,2,3,4], index=['a', 'b', 'c', 'd'])
print(x1)
print(x2)
```

结果输出：
```
0    1
1    2
2    3
3    4
dtype: int64
a    1
b    2
c    3
d    4
dtype: int64
```
结果中可以看出输出的内容是键值对应的，且默认情况下，键是从0开始递增的自然数。我们还可以使用字典方式来创建 Series。
```python
d = {'a':1, 'b':2, 'c':3, 'd':4}
x3 = Series(d)
print(x3)
```

结果输出：
```
a    1
b    2
c    3
d    4
dtype: int64
```

**DataFrame 类型数据结构类似数据库表。** 它包括了行索引和列索引，我们可以将 DataFrame 看成是由相同索引的 Series 组成的字典类型。

```python
import pandas as pd
from pandas import Series, DataFrame
data = {'Chinese': [66, 95, 93, 90,80],'English': [65, 85, 92, 88, 90],'Math': [30, 98, 96, 77, 90]}
df1 = DataFrame(data)
df2 = DataFrame(data, columns=['English', 'Math', 'Chinese'])
df3 = DataFrame(data, index=['ZhangFei', 'GuanYu', 'ZhaoYun', 'HuangZhong', 'DianWei'], columns=['English', 'Math', 'Chinese'])
print(df1)
print(df2)
print(df3)
```

结果输出
```
   Chinese  English  Math
0       66       65    30
1       95       85    98
2       93       92    96
3       90       88    77
4       80       90    90
   English  Math  Chinese
0       65    30       66
1       85    98       95
2       92    96       93
3       88    77       90
4       90    90       80
            English  Math  Chinese
ZhangFei         65    30       66
GuanYu           85    98       95
ZhaoYun          92    96       93
HuangZhong       88    77       90
DianWei          90    90       80
```
由此看出，df2 可以重新对列进行顺序调整，df3 可以为行增加一个可识别的索引。那么下面再看下在处理数据过程中 Series 和 DataFrame 的用法。

#### 数据导入和输出
Pandas 允许直接从 xlsx，csv 等文件中导入数据，也可以输出到 xlsx, csv 等文件，非常方便。
```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'))
score.to_excel('data1.xlsx')
print(score)
```

在执行过程中，发现报错，缺少 xlrd 和 openpyxl（第一次只报了 xlrd 缺失，安装后会报 openpyxl 缺失）。执行 pip3 install 进行安装。

全部安装完毕后执行，可以看到所读取的 Excel 的数据内容，并且会在同目录下生成一个 data1.xlsx 的文件，生成的文件与源文件最大的不同是多了一列默认索引。

在原来代码基础上做个调整：
```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '职位', '邮箱', '手机号'])
score.to_excel('data1.xlsx')
print(score)
```

根据输出结果，可以看出，生成的新文件和打印的数据均重新将列进行了排序。

#### 数据清洗
数据清洗是数据准备过程中必不可少的环节. 我这里按课程中老师最后留的课后作业作为数据样本。用之前读取 Excel 文件的代码执行后，结果输出：
```
   姓名  语文  数学    英语
0  张飞  66  65   NaN
1  关羽  95  85  98.0
2  赵云  95  92  96.0
3  黄忠  90  88  77.0
4  典韦  80  90  90.0
```
一般，数据清洗会遇到如下一种或几种情况。
##### 1. 删除行或列

Pandas 提供了一个便捷的方法 drop() 函数来删除我们不想要的列或行。比如我们想把“语文”这列删掉。
``` python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '语文', '数学', '英语'])
df = score.drop(columns=['语文'])
print(df)
```

结果输出：
```
   姓名  数学    英语
0  张飞  65   NaN
1  关羽  85  98.0
2  赵云  92  96.0
3  黄忠  88  77.0
4  典韦  90  90.0
```

想把“张飞”这行删掉（即第一行）。

```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '语文', '数学', '英语'])
df = score.drop(index=[0])
print(df)
```

结果输出：
```
   姓名  语文  数学    英语
1  关羽  95  85  98.0
2  赵云  95  92  96.0
3  黄忠  90  88  77.0
4  典韦  80  90  90.0
```
从结果可以看出，数据索引 0 的这行已经被删除了。


##### 2. 重命名列名
如果你想对 DataFrame 中的 columns 进行重命名，可以直接使用 rename(columns=new_names, inplace=True) 函数，比如我把列名 **英语** 改成 **English**。
```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '语文', '数学', '英语'])
df = score.rename(columns={'英语': 'English'})
print(df)
```

结果输出：
```
   姓名  语文  数学  English
0  张飞  66  65      NaN
1  关羽  95  85     98.0
2  赵云  95  92     96.0
3  黄忠  90  88     77.0
4  典韦  80  90     90.0
```

##### 3. 去重
数据采集可能存在重复的行，这时只要使用 drop_duplicates() 就会自动把重复的行去掉。
```python
df = df.drop_duplicates()
```

##### 4. 格式化
###### 4.1 更改数据格式
这是个比较常用的操作，因为很多时候数据格式不规范，我们可以使用 astype 函数来规范数据格式，比如我们把语文字段的值改成 str 类型，或者 int64 可以这么写：
```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '语文', '数学', '英语'])
df = score['语文'].astype('str')
print(df)
```

结果输出：
```
0    66
1    95
2    95
3    90
4    80
```

###### 4.2 数据间的空格
有时候我们先把格式转成了 str 类型，是为了方便对数据进行操作，这时想要删除数据间的空格，我们就可以使用 strip 函数：
```python
#删除左右两边空格

df2['语文']=df2['语文'].map(str.strip)

#删除左边空格

df2['语文']=df2['语文'].map(str.lstrip)

#删除右边空格

df2['语文']=df2['语文'].map(str.rstrip)
```

如果数据里有某个特殊的符号，我们想要删除怎么办？同样可以使用 strip 函数，比如 **语文** 字段里有美元符号，我们想把这个删掉，可以这么写：
```python
df2['语文']=df2['语文'].str.strip('$')
```

###### 4.3 大小写转换
大小写是个比较常见的操作，比如人名、城市名等的统一都可能用到大小写的转换，在 Python 里直接使用 upper(), lower(), title() 函数，方法如下：
```python
#全部大写

df2.columns = df2.columns.str.upper()

#全部小写

df2.columns = df2.columns.str.lower()

#首字母大写

df2.columns = df2.columns.str.title()
```

###### 4.4 查找空值
数据量大的情况下，有些字段存在空值 NaN 的可能，这时就需要使用 Pandas 中的 isnull 函数进行查找。比如，刚刚的数据例子：
```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '语文', '数学', '英语'])
df = score.isnull()
print(df)
```

结果输出：
```
      姓名     语文     数学     英语
0  False  False  False   True
1  False  False  False  False
2  False  False  False  False
3  False  False  False  False
4  False  False  False  False
```

如果我想知道哪列存在空值，可以使用 df.isnull().any()，结果如下：
```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '语文', '数学', '英语'])
df = score.isnull().any()
print(df)
```

结果输出：
```
姓名    False
语文    False
数学    False
英语     True
dtype: bool
```

##### 5. 使用 apply 函数对数据进行清洗
apply 函数是 Pandas 中自由度非常高的函数，使用频率也非常高。比如我们想对 name 列的数值都进行大写转化可以用：
```python
df['name'] = df['name'].apply(str.upper)
```
也可以 apply 一个自定义的函数。

#### 数据统计 
在数据清洗后，我们就要对数据进行统计了。Pandas 和 NumPy 一样，都有常用的统计函数，如果遇到空值 NaN，会自动排除。

常用的统计函数包括：
- count(): 统计个数，空值 NaN 不计算。
- describe(): 一次性输出多个统计指标，包括 count,mean,std,min,max等
- min(): 最小值
- max(): 最大值
- sum(): 总和
- mean(): 平均值
- median(): 中位数
- var(): 方差
- std(): 标准差
- argmin(): 统计最小值的索引位置
- argmax(): 统计最大值的索引位置
- idxmin(): 统计最小值的索引值
- idxmax(): 统计最大值的索引值

其中有一个 describe() 函数，统计函数千千万，describe() 函数最简便。它是个统计大礼包，可以快速让我们对数据有个全面的了解。例如：
```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '语文', '数学', '英语'])
df = score.describe()
print(df)
```

结果输出：
```
              语文         数学         英语
count   5.000000   5.000000   4.000000
mean   85.200000  84.000000  90.250000
std    12.357184  10.931606   9.464847
min    66.000000  65.000000  77.000000
25%    80.000000  85.000000  86.750000
50%    90.000000  88.000000  93.000000
75%    95.000000  90.000000  96.500000
```

## 实践：增加一列总分项
根据上面的数据样例，我期望对数据最后增加一列总和列，程序如下：
```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '语文', '数学', '英语'])

# 张飞的英语是 NaN, 这里给 NaN 赋予默认值 0。一般数据分析的场景下，也可以使用平均值或者中位数来填充无法获取的数据。

score['英语'].fillna(0, inplace=True)

# 自定义一个加总的函数

def total_score(df):
    df['总分'] = df['语文'] + df['英语'] + df['数学']
    return df
df = score.apply(total_score, axis=1)

print(df)
```

结果输出：
```
   姓名  语文  数学    英语     总分
0  张飞  66  65   0.0  131.0
1  关羽  95  85  98.0  278.0
2  赵云  95  92  96.0  283.0
3  黄忠  90  88  77.0  255.0
4  典韦  80  90  90.0  260.0
```

在练习中，我根据评论中其他人的写法，又调整了程序，并增加了一个需求是增加一行数据，用于总计语文、数学、英语、总分的总和。代码如下：
```python
import pandas as pd
from pandas import Series, DataFrame
score = DataFrame(pd.read_excel('data.xlsx'), columns=['姓名', '语文', '数学', '英语'])

# 张飞的英语是 NaN, 这里给 NaN 赋予默认值 0。一般数据分析的场景下，也可以使用平均值或者中位数来填充无法获取的数据。

score['英语'].fillna(0, inplace=True)

score['总分'] = score.sum(axis=1)

df1 = score.sum(axis=0).drop(index='姓名')
df2 = score.append(df1, ignore_index=True)

df2['姓名'].fillna('总计', inplace=True)

print(df2)
```

结果输出：
```
   姓名   语文   数学     英语      总分
0  张飞   66   65    0.0   131.0
1  关羽   95   85   98.0   278.0
2  赵云   95   92   96.0   283.0
3  黄忠   90   88   77.0   255.0
4  典韦   80   90   90.0   260.0
5  总计  426  420  361.0  1207.0
```
#### 补充：axis 说明
这里面有个概念让我也懵了一下，就是axis的设置，翻查了资料（资料来源[简书：行走的程序猿](https://www.jianshu.com/p/4f18e8327872)），总结如下：

![示意图](https://tva1.sinaimg.cn/large/006tNbRwgy1gbmjyh5i9mj30i409hdg6.jpg)

从这张图可以看出：

axis=0、axis=index，指的是遍历每个index、行号，即在纵向上遍历每列，所以做sum()、mean()等运算时，是对每列数据做操作，而drop(index, axis=0)，传入的参数指定了某一行号，所以会在纵向上遍历每列，去掉行号对应位置的数据。

axis=1、axis=columns，指的是遍历每个columns、列名，即在横向上遍历每行，所以做sum()、mean()等运算时，是对每行数据做操作，而drop(col, axis=1)，传入的参数指定了某一列名，所以会在横向上遍历每行，去掉列名对应位置的数据。


## 总结
今天根据教程，尝试对一个数据表进行程序级别的数据批量处理，这里使用的都是基础方法，在实际的数据准备过程中，数据清洗将会有更多的复杂情况，但基本都是这些方法的组合使用。