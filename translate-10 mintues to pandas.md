# 翻译-10分钟入门Pandas

------

本文是Pandas的简短介绍，主要面向新用户。您可以在[代码示例集（Cookbook）](https://pandas.pydata.org/docs/user_guide/cookbook.html#cookbook)中查看更复杂的用法。

通常，我们按如下方式导入：

```python
import numpy as np
import pandas as pd
```

## Pandas 中的基本数据结构

Pandas 提供了两类用于处理数据的基本结构：

1.  **`Series`**：一个一维的标签化数组，可保存任何类型的数据（整数、字符串、Python对象等）。
2.  **`DataFrame`**：一个二维的数据结构，可以像二维数组或包含行和列的表格一样保存数据。

## 对象创建

创建**`Series`**，传入一个值列表，Pandas 会自动创建一个默认的整数索引（`RangeIndex`）。

```python
s = pd.Series([1, 3, 5, np.nan, 6, 8])
s
# 输出：
# 0    1.0
# 1    3.0
# 2    5.0
# 3    NaN
# 4    6.0
# 5    8.0
# dtype: float64
```

创建**`DataFrame`**，通过传递一个NumPy数组，并指定一个由`date_range()`生成的日期时间索引和标签化的列名。

```python
dates = pd.date_range("20130101", periods=6)
dates
# 输出：
# DatetimeIndex(['2013-01-01', '2013-01-02', '2013-01-03', '2013-01-04',
#                '2013-01-05', '2013-01-06'],
#               dtype='datetime64[ns]', freq='D')

df = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=list("ABCD"))
df
# 输出示例：
#                    A         B         C         D
# 2013-01-01  0.469112 -0.282863 -1.509059 -1.135632
# 2013-01-02  1.212112 -0.173215  0.119209 -1.044236
# ...
```

通过传递一个字典对象来创建**`DataFrame`**，其中键是列标签，值是列数据。

```python
df2 = pd.DataFrame(
    {
        "A": 1.0,
        "B": pd.Timestamp("20130102"),
        "C": pd.Series(1, index=list(range(4)), dtype="float32"),
        "D": np.array([3] * 4, dtype="int32"),
        "E": pd.Categorical(["test", "train", "test", "train"]),
        "F": "foo",
    }
)
df2
# 输出：
#      A          B    C  D      E    F
# 0  1.0 2013-01-02  1.0  3   test  foo
# 1  1.0 2013-01-02  1.0  3  train  foo
# 2  1.0 2013-01-02  1.0  3   test  foo
# 3  1.0 2013-01-02  1.0  3  train  foo
```

生成的`DataFrame`的列具有不同的**数据类型（dtypes）**：

```python
df2.dtypes
# 输出：
# A           float64
# B     datetime64[s]
# C           float32
# D             int32
# E          category
# F            object
# dtype: object
```

## 查看数据

使用`DataFrame.head()`和`DataFrame.tail()`分别查看框架的顶部和底部行：

```python
df.head() # 默认查看前5行
df.tail(3) # 查看最后3行
```

显示`DataFrame.index`或`DataFrame.columns`：

```python
df.index
df.columns
```

使用`DataFrame.to_numpy()`返回底层数据的NumPy表示（不包含索引或列标签）：

```python
df.to_numpy()
# 输出示例：
# array([[ 0.4691, -0.2829, -1.5091, -1.1356],
#        [ 1.2121, -0.1732,  0.1192, -1.0442],
#        ... ])
```

> **注意**：
> **NumPy数组整个数组只有一个dtype，而pandas DataFrame每列可以有一个dtype。** 当你调用`DataFrame.to_numpy()`时，Pandas会找到一个能够容纳DataFrame中所有dtype的NumPy数据类型。如果公共数据类型是`object`，则`DataFrame.to_numpy()`将需要复制数据。

`describe()`显示数据的快速统计摘要：

```python
df.describe()
```

转置数据：

```python
df.T
```

`DataFrame.sort_index()`按轴排序：

```python
df.sort_index(axis=1, ascending=False) # 按列名降序排序
```

`DataFrame.sort_values()`按值排序：

```python
df.sort_values(by="B") # 按列“B”的值升序排序
```

## 数据选择

### 通过 `[]` 选择

对于`DataFrame`，传递单个标签会选择一列，并产生一个`Series`（等同于`df.A`）：

```python
df["A"]
```

对于`DataFrame`，传递一个切片`:`会选择匹配的行：

```python
df[0:3] # 选择前3行
df["20130102":"20130104"] # 通过标签切片选择行（包含两端）
```

### 按标签选择

使用`DataFrame.loc()`或`DataFrame.at()`。

选择匹配标签的行：

```python
df.loc[dates[0]]
```

选择所有行（`:`）和特定的列标签：

```python
df.loc[:, ["A", "B"]]
```

对于标签切片，**两端都包含**：

```python
df.loc["20130102":"20130104", ["A", "B"]]
```

选择单个行和列标签返回一个标量：

```python
df.loc[dates[0], "A"]
```

使用`at`快速访问标量（效果与上述方法相同）：

```python
df.at[dates[0], "A"]
```

### 按位置选择

使用`DataFrame.iloc()`或`DataFrame.iat()`。

通过传递的整数位置选择：

```python
df.iloc[3] # 选择第4行（0基索引）
```

整数切片的行为类似于NumPy/Python：

```python
df.iloc[3:5, 0:2] # 选择行切片和列切片
```

通过整数位置列表选择：

```python
df.iloc[[1, 2, 4], [0, 2]] # 选择不连续的行和列
```

显式地对行进行切片：

```python
df.iloc[1:3, :] # 选择第2到第3行，所有列
```

显式地对列进行切片：

```python
df.iloc[:, 1:3] # 选择所有行，第2到第3列
```

显式获取一个值：

```python
df.iloc[1, 1]
```

使用`iat`快速访问标量：

```python
df.iat[1, 1]
```

### 布尔索引

选择`df.A`大于0的行：

```python
df[df["A"] > 0]
```

从`DataFrame`中选择满足布尔条件的值：

```python
df[df > 0] # 大于0的值保留，其他变为NaN
```

使用`isin()`方法进行过滤：

```python
df2 = df.copy()
df2["E"] = ["one", "one", "two", "three", "four", "three"]
df2[df2["E"].isin(["two", "four"])]
```

### 数据设置

设置新列会自动按索引对齐数据：

```python
s1 = pd.Series([1, 2, 3, 4, 5, 6], index=pd.date_range("20130102", periods=6))
df["F"] = s1 # 新增列‘F’
```

按标签设置值：

```python
df.at[dates[0], "A"] = 0
```

按位置设置值：

```python
df.iat[0, 1] = 0
```

通过分配NumPy数组进行设置：

```python
df.loc[:, "D"] = np.array([5] * len(df))
```

带有设置的`where`操作：

```python
df2 = df.copy()
df2[df2 > 0] = -df2 # 将所有大于0的值替换为其相反数
```

## 缺失数据

对于NumPy数据类型，`np.nan`代表缺失数据。默认情况下，它不参与计算。

**重新索引（Reindexing）** 允许您更改/添加/删除指定轴上的索引。这会返回一个数据副本：

```python
df1 = df.reindex(index=dates[0:4], columns=list(df.columns) + ["E"])
df1.loc[dates[0] : dates[1], "E"] = 1
df1
```

`DataFrame.dropna()`删除任何有缺失数据的行：

```python
df1.dropna(how="any")
```

`DataFrame.fillna()`填充缺失数据：

```python
df1.fillna(value=5)
```

`isna()`获取值为`nan`的布尔掩码：

```python
pd.isna(df1)
```

## 操作

### 统计

操作通常**排除**缺失数据。

计算每列的平均值：

```python
df.mean() # 默认按列计算
```

计算每行的平均值：

```python
df.mean(axis=1) # 按行计算
```

### 用户自定义函数

`DataFrame.agg()`和`DataFrame.transform()`分别应用**归约**或**广播**结果的用户定义函数。

```python
df.agg(lambda x: np.mean(x) * 5.6) # 聚合，对每列应用函数
df.transform(lambda x: x * 101.2) # 转换，对每个元素应用函数
```

### 值计数

```python
s = pd.Series(np.random.randint(0, 7, size=10))
s.value_counts() # 统计每个唯一值出现的次数
```

### 字符串方法

`Series`配备了一组字符串处理方法（在`str`属性中），可以轻松地对数组的每个元素进行操作。

```python
s = pd.Series(["A", "B", "C", "Aaba", "Baca", np.nan, "CABA", "dog", "cat"])
s.str.lower() # 将每个字符串转换为小写
```

## 合并

### 连接（Concat）

使用`concat()`沿行方向连接pandas对象：

```python
df = pd.DataFrame(np.random.randn(10, 4))
pieces = [df[:3], df[3:7], df[7:]]
pd.concat(pieces) # 将拆分的片段重新连接
```

> **注意**：
> 向`DataFrame`添加列相对较快。然而，添加行需要复制，可能代价高昂。我们建议将预构建的记录列表传递给`DataFrame`构造函数，而不是通过迭代追加记录来构建`DataFrame`。

### 连接（Join）

`merge()`支持沿特定列的SQL风格连接类型。

```python
left = pd.DataFrame({"key": ["foo", "foo"], "lval": [1, 2]})
right = pd.DataFrame({"key": ["foo", "foo"], "rval": [4, 5]})
pd.merge(left, right, on="key")
```

在唯一键上进行`merge()`：

```python
left = pd.DataFrame({"key": ["foo", "bar"], "lval": [1, 2]})
right = pd.DataFrame({"key": ["foo", "bar"], "rval": [4, 5]})
pd.merge(left, right, on="key")
```

## 分组（Grouping）

“分组”是指涉及以下一个或多个步骤的过程：
1.  **拆分**：根据某些条件将数据拆分为组。
2.  **应用**：将函数独立应用于每个组。
3.  **合并**：将结果组合成数据结构。

按列标签分组，选择列标签，然后对结果组应用`sum()`函数：

```python
df = pd.DataFrame({
    "A": ["foo", "bar", "foo", "bar", "foo", "bar", "foo", "foo"],
    "B": ["one", "one", "two", "three", "two", "two", "one", "three"],
    "C": np.random.randn(8),
    "D": np.random.randn(8)
})
df.groupby("A")[["C", "D"]].sum()
```

按多个列标签分组会形成`MultiIndex`（多层索引）：

```python
df.groupby(["A", "B"]).sum()
```

## 重塑

### 堆叠（Stack）

`stack()`方法“压缩”`DataFrame`列中的一个层级：

```python
arrays = [["bar", "bar", "baz", "baz", "foo", "foo", "qux", "qux"],
          ["one", "two", "one", "two", "one", "two", "one", "two"]]
index = pd.MultiIndex.from_arrays(arrays, names=["first", "second"])
df = pd.DataFrame(np.random.randn(8, 2), index=index, columns=["A", "B"])
df2 = df[:4]
stacked = df2.stack(future_stack=True)
stacked
```

对于“堆叠”后的`DataFrame`或`Series`（索引为`MultiIndex`），`stack()`的逆操作是`unstack()`，默认情况下它**解堆**最后一个层级：

```python
stacked.unstack()
stacked.unstack(1) # 解堆第二层
stacked.unstack(0) # 解堆第一层
```

### 数据透视表（Pivot Tables）

`pivot_table()`通过指定`values`（值）、`index`（索引）和`columns`（列）来透视一个`DataFrame`。

```python
df = pd.DataFrame({
    "A": ["one", "one", "two", "three"] * 3,
    "B": ["A", "B", "C"] * 4,
    "C": ["foo", "foo", "foo", "bar", "bar", "bar"] * 2,
    "D": np.random.randn(12),
    "E": np.random.randn(12)
})
pd.pivot_table(df, values="D", index=["A", "B"], columns=["C"])
```

## 时间序列

Pandas具有简单、强大且高效的功能，用于在执行频率转换期间进行重采样操作（例如，将秒数据转换为5分钟数据）。

```python
rng = pd.date_range("1/1/2012", periods=100, freq="s")
ts = pd.Series(np.random.randint(0, 500, len(rng)), index=rng)
ts.resample("5Min").sum() # 每5分钟求和
```

`Series.tz_localize()`将时间序列本地化到一个时区：

```python
rng = pd.date_range("3/6/2012 00:00", periods=5, freq="D")
ts = pd.Series(np.random.randn(len(rng)), rng)
ts_utc = ts.tz_localize("UTC") # 本地化为UTC时区
```

`Series.tz_convert()`将已有时区信息的时间序列转换为另一个时区：

```python
ts_utc.tz_convert("US/Eastern") # 从UTC转换到美国东部时间
```

向时间序列添加非固定持续时间（`BusinessDay`）：

```python
rng + pd.offsets.BusinessDay(5) # 增加5个工作日
```

## 分类数据

Pandas可以在`DataFrame`中包含分类数据。

将原始成绩转换为分类数据类型：

```python
df = pd.DataFrame({"id": [1, 2, 3, 4, 5, 6], "raw_grade": ["a", "b", "b", "a", "a", "e"]})
df["grade"] = df["raw_grade"].astype("category")
df["grade"]
```

将类别重命名为更有意义的名称：

```python
new_categories = ["very good", "good", "very bad"]
df["grade"] = df["grade"].cat.rename_categories(new_categories)
```

对类别重新排序，并同时添加缺失的类别：

```python
df["grade"] = df["grade"].cat.set_categories(["very bad", "bad", "medium", "good", "very good"])
```

按分类列排序遵循类别中的顺序，而不是词法顺序：

```python
df.sort_values(by="grade")
```

使用`observed=False`按分类列分组也会显示空类别：

```python
df.groupby("grade", observed=False).size()
```

## 绘图

我们使用引用matplotlib API的标准约定：

```python
import matplotlib.pyplot as plt
plt.close("all") # 关闭图形窗口

ts = pd.Series(np.random.randn(1000), index=pd.date_range("1/1/2000", periods=1000))
ts = ts.cumsum()
ts.plot() # 绘制Series

df = pd.DataFrame(np.random.randn(1000, 4), index=ts.index, columns=["A", "B", "C", "D"])
df = df.cumsum()
df.plot() # 绘制DataFrame，所有列
plt.legend(loc='best')
```

## 数据的导入和导出

### CSV

**写入CSV文件**：使用`DataFrame.to_csv()`

```python
df.to_csv("foo.csv")
```

**从CSV文件读取**：使用`read_csv()`

```python
pd.read_csv("foo.csv")
```

### Parquet

**写入Parquet文件**：

```python
df.to_parquet("foo.parquet")
```

**从Parquet文件读取**：使用`read_parquet()`

```python
pd.read_parquet("foo.parquet")
```

### Excel

**写入Excel文件**：使用`DataFrame.to_excel()`

```python
df.to_excel("foo.xlsx", sheet_name="Sheet1")
```

**从Excel文件读取**：使用`read_excel()`

```python
pd.read_excel("foo.xlsx", "Sheet1", index_col=None, na_values=["NA"])
```

## 常见陷阱（Gotchas）

如果你尝试对`Series`或`DataFrame`执行布尔操作，可能会看到如下异常：

```python
if pd.Series([False, True, False]):
     print("I was true")
# 抛出 ValueError: The truth value of a Series is ambiguous...
```

错误提示很明确：**`Series`的真值是模糊的**。应使用`a.empty`， `a.bool()`， `a.item()`， `a.any()`或`a.all()`来判断。
