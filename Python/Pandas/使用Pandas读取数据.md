---
tags:
  - Python
  - Pandas
  - 数学建模
date: 2018-08-23
---

# 使用 Pandas 读取数据

在参加数模的过程中，常常要碰到的一个问题就是数据的处理。通常题目所给的数据集往往会是 excel 文件或是 csv 文件。而对于这两个格式的文件，pandas 中已经内置了对应的方案，大大简化了数据预处理的步骤。

## 基础操作

对于最基础的情况，我们只需给出对应文件即可。

```python
# excel文件
df = pd.read_excel('file path')
# csv文件
df = pd.read_csv('file path')
```

这里使用的是路径字符串来指定对应文件，这在大多数情况下都能满足需求。

当然，若有特殊需求，也可以用文件流作为参数。

```python
with open('file path') as f:
    df = pd.read_csv(f)
```

这里值得一提的是，在以默认参数读取 csv 文件时，**csv 文件路径不能包含中文**，因为默认的 c 语言引擎不支持中文，具体会在后文中说明。

## 通用参数

在实际场景中，默认的参数常常并不能完全满足我们的需求，但所幸 pandas 为我们提供了丰富的可选参数，使得我们能够根据实际场景的需求来读取对应的文件。

其中，大多数的可选参数对各类数据源来说都是通用的。(示例代码中将以读取 excel 文件为例)

这里我将介绍几个常用的参数。

### index_col

index_col 用来指定索引列，主要有以下三种用法：

1. None

   这是默认的 index_col 参数，表示不指定索引列。

   最后所得 DataFrame 的索引为从 0 开始的自增数列。

   ```python
   df = pd.read_excel('file path', index_col=None)
   ```

2. int

   对应的列数，从 0 开始计数。

   ```python
   df = pd.read_excel('file path', index_col=0)
   ```

3. list

   一般用的不多，用于指定多级索引，list 中的对应元素表示每一级索引所在的列数。

   ```python
   # 这里的[0,1]表示将第0列以及第1列作为索引
   # 其中，第0列为一级索引，第1列为二级索引
   df = pd.read_excel('file path', index_col=[0,1])
   ```

### header

header 用来指定标题行，用法和 index_col 基本类似。

其中最大的区别就是 header 的默认参数行为，与 index_col 不同，pandas 会默认把第一行作为标题行。

1. None

   无标题行，然后 pandas 会自动生成一个从 0 开始的数列。

   ```python
   df = pd.read_excel('file path', header=None)
   ```

2. int

   对应的行数，从 0 开始计数。

   **注意**：若取大于 0 的行作为标题，pandas 将会忽略标题行之前的数据。

   ```python
   df = pd.read_excel('file path', header=0)
   ```

3. list

   用于指定多级标题，list 中的对应元素表示每一级标题所在的行数。

   ```python
   # 这里的[0,1]表示将第0行以及第1行作为标题
   # 其中，第0行为一级标题，第1行为二级标题
   df = pd.read_excel('file path', index_col=[0,1])
   ```

## excel 文件

具体到 excel 文件的读取，会碰到的一个问题就是工作簿(sheet)的处理。

pandas 也很贴心的为我们准备了`sheet_name`参数，由此我们可以指定所想要的工作簿。

参数用法也很简单：

```python
# 读取第一个工作簿(默认参数)
df = pd.read_excel('file path', sheet_name=0)
# 读取名称为'Sheet1'的工作簿
df = pd.read_excel('file path', sheet_name='Sheet1')
# 读取第一个工作簿和名为'Sheet2'的工作簿
df = pd.read_excel('file path', sheet_name=[0, 'Sheet2'])
# 读取所有工作簿
# 返回类型为OrderedDict，以工作簿名为key
df = pd.read_excel('file path', sheet_name=None)
```

## csv 文件

### engine

上文也提到了，在使用默认参数读取 csv 文件时，文件路径不能包含中文。

这是因为 pandas 所使用的 c 语言引擎不支持中文所导致的，所有也就引出了一个参数：engine。

engine 有两个可选项，分别是`c`和`python`，默认为`c`。

> 其中，c 代表 c 语言引擎，其底层使用 c 语言实现，读取效率高；而 python 则是以纯 python 实现，读取效率比 c 慢一些，但能支持中文路径。

所以，对于路径中包含中文的文件来说，有两个解决方案：

1. 改用 python 引擎
2. 根据路径创建文件流再传递给 pandas

### encoding

encoding 参数用于设置读取文件时所使用的编码方式，默认为 utf-8。

当发生中文乱码的时候，可以尝试改用 gbk 编码。

但个人还是推荐在读取文本之前就将所以编码统一设置为 utf-8，方便使用而且不易出错，像 VS Code 之类的编辑器都提供了转化编码的功能。
