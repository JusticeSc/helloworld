# （翻译）Pandas入门：基础核心功能

------

本节我们将讨论 pandas 数据结构所共有的许多基础核心功能。首先，让我们像在 [10分钟入门 pandas](https://pandas.pydata.org/docs/user_guide/10min.html#min) 部分中那样创建一些示例对象：

```python
In [1]: index = pd.date_range("1/1/2000", periods=8)

In [2]: s = pd.Series(np.random.randn(5), index=["a", "b", "c", "d", "e"])

In [3]: df = pd.DataFrame(np.random.randn(8, 3), index=index, columns=["A", "B", "C"])
```

## 头部和尾部[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#head-and-tail)

要查看 Series 或 DataFrame 对象的一个小样本，可以使用 [`head()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.head.html#pandas.DataFrame.head) 和 [`tail()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.tail.html#pandas.DataFrame.tail) 方法。默认显示的元素数量是五个，但你可以传递一个自定义数字。

```python
In [4]: long_series = pd.Series(np.random.randn(1000))

In [5]: long_series.head()
Out[5]: 
0   -1.157892
1   -1.344312
2    0.844885
3    1.075770
4   -0.109050
dtype: float64

In [6]: long_series.tail(3)
Out[6]: 
997   -0.289388
998   -1.020544
999    0.589993
dtype: float64
```

## 属性和底层数据[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#attributes-and-underlying-data)

pandas 对象具有许多属性，使你能够访问元数据。

- **shape**：给出对象的轴维度，与 ndarray 一致。
- 轴标签：
    - **Series**：*index*（唯一的轴）。
    - **DataFrame**：*index*（行）和 *columns*（列）。

注意，**这些属性可以被安全地赋值！**

```python
In [7]: df[:2]
Out[7]: 
                   A         B         C
2000-01-01 -0.173215  0.119209 -1.044236
2000-01-02 -0.861849 -2.104569 -0.494929

In [8]: df.columns = [x.lower() for x in df.columns]

In [9]: df
Out[9]: 
                   a         b         c
2000-01-01 -0.173215  0.119209 -1.044236
2000-01-02 -0.861849 -2.104569 -0.494929
2000-01-03  1.071804  0.721555 -0.706771
2000-01-04 -1.039575  0.271860 -0.424972
2000-01-05  0.567020  0.276232 -1.087401
2000-01-06 -0.673690  0.113648 -1.478427
2000-01-07  0.524988  0.404705  0.577046
2000-01-08 -1.715002 -1.039268 -0.370647
```

pandas 对象（[`Index`](https://pandas.pydata.org/docs/reference/api/pandas.Index.html#pandas.Index)、[`Series`](https://pandas.pydata.org/docs/reference/api/pandas.Series.html#pandas.Series)、[`DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html#pandas.DataFrame)）可以被看作是数组的容器，数组持有实际数据并进行实际计算。对于许多类型，底层数组是 [`numpy.ndarray`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray)。然而，pandas 和第三方库可能会*扩展* NumPy 的类型系统以添加对自定义数组的支持（参见 [dtypes](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#basics-dtypes)）。

要获取 [`Index`](https://pandas.pydata.org/docs/reference/api/pandas.Index.html#pandas.Index) 或 [`Series`](https://pandas.pydata.org/docs/reference/api/pandas.Series.html#pandas.Series) 内的实际数据，请使用 `.array` 属性。

```python
In [10]: s.array
Out[10]: 
<NumpyExtensionArray>
[ 0.4691122999071863, -0.2828633443286633, -1.5090585031735124,
 -1.1356323710171934,  1.2121120250208506]
Length: 5, dtype: float64

In [11]: s.index.array
Out[11]: 
<NumpyExtensionArray>
['a', 'b', 'c', 'd', 'e']
Length: 5, dtype: object
```

[`array`](https://pandas.pydata.org/docs/reference/api/pandas.Series.array.html#pandas.Series.array) 将始终是一个 [`ExtensionArray`](https://pandas.pydata.org/docs/reference/api/pandas.api.extensions.ExtensionArray.html#pandas.api.extensions.ExtensionArray)。关于 [`ExtensionArray`](https://pandas.pydata.org/docs/reference/api/pandas.api.extensions.ExtensionArray.html#pandas.api.extensions.ExtensionArray) 的具体细节以及为什么 pandas 使用它们，有点超出了本介绍的范围。更多信息请参见 [dtypes](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#basics-dtypes)。

如果你确定需要一个 NumPy 数组，请使用 [`to_numpy()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.to_numpy.html#pandas.Series.to_numpy) 或 `numpy.asarray()`。

```python
In [12]: s.to_numpy()
Out[12]: array([ 0.4691, -0.2829, -1.5091, -1.1356,  1.2121])

In [13]: np.asarray(s)
Out[13]: array([ 0.4691, -0.2829, -1.5091, -1.1356,  1.2121])
```

当 Series 或 Index 由 [`ExtensionArray`](https://pandas.pydata.org/docs/reference/api/pandas.api.extensions.ExtensionArray.html#pandas.api.extensions.ExtensionArray) 支持时，[`to_numpy()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.to_numpy.html#pandas.Series.to_numpy) 可能涉及数据复制和值强制转换。更多信息请参见 [dtypes](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#basics-dtypes)。

[`to_numpy()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.to_numpy.html#pandas.Series.to_numpy) 提供了对结果 [`numpy.ndarray`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray) 的 `dtype` 的某些控制。例如，考虑带有时区的日期时间。NumPy 没有表示带时区的日期时间的 dtype，因此有两种可能有用的表示方式：

1. 一个对象类型的 [`numpy.ndarray`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray)，其中包含具有正确 `tz` 的 [`Timestamp`](https://pandas.pydata.org/docs/reference/api/pandas.Timestamp.html#pandas.Timestamp) 对象。
2. 一个 `datetime64[ns]` 类型的 [`numpy.ndarray`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.html#numpy.ndarray)，其中的值已转换为 UTC 并丢弃了时区。

使用时，时区可以通过 `dtype=object` 来保留：

```python
In [14]: ser = pd.Series(pd.date_range("2000", periods=2, tz="CET"))

In [15]: ser.to_numpy(dtype=object)
Out[15]: 
array([Timestamp('2000-01-01 00:00:00+0100', tz='CET'),
       Timestamp('2000-01-02 00:00:00+0100', tz='CET')], dtype=object)
```

或者通过 `dtype='datetime64[ns]'` 来丢弃：

```python
In [16]: ser.to_numpy(dtype="datetime64[ns]")
Out[16]: 
array(['1999-12-31T23:00:00.000000000', '2000-01-01T23:00:00.000000000'],
      dtype='datetime64[ns]')
```

获取 [`DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html#pandas.DataFrame) 内部的“原始数据”可能稍微复杂一些。当你的 `DataFrame` 所有列只有单一数据类型时，[`DataFrame.to_numpy()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_numpy.html#pandas.DataFrame.to_numpy) 将返回底层数据：

```python
In [17]: df.to_numpy()
Out[17]: 
array([[-0.1732,  0.1192, -1.0442],
       [-0.8618, -2.1046, -0.4949],
       [ 1.0718,  0.7216, -0.7068],
       [-1.0396,  0.2719, -0.425 ],
       [ 0.567 ,  0.2762, -1.0874],
       [-0.6737,  0.1136, -1.4784],
       [ 0.525 ,  0.4047,  0.577 ],
       [-1.715 , -1.0393, -0.3706]])
```

如果一个 DataFrame 包含同类型数据，ndarray 实际上可以就地修改，并且更改将反映在数据结构中。对于异构数据（例如，DataFrame 的某些列并非全部是相同 dtype），情况则并非如此。`values` 属性本身（与轴标签不同）不能被赋值。

注意

处理异构数据时，结果 ndarray 的 dtype 将被选择以适应所有涉及的数据。例如，如果涉及字符串，结果将是对象类型。如果只有浮点数和整数，结果数组将是浮点类型。

在过去，pandas 推荐使用 [`Series.values`](https://pandas.pydata.org/docs/reference/api/pandas.Series.values.html#pandas.Series.values) 或 [`DataFrame.values`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.values.html#pandas.DataFrame.values) 来从 Series 或 DataFrame 中提取数据。你仍然可以在旧的代码库和网上找到对此的引用。展望未来，我们建议避免使用 `.values`，而使用 `.array` 或 `.to_numpy()`。`.values` 有以下缺点：

1. 当你的 Series 包含[扩展类型](https://pandas.pydata.org/docs/development/extending.html#extending-extension-types)时，[`Series.values`](https://pandas.pydata.org/docs/reference/api/pandas.Series.values.html#pandas.Series.values) 返回的是 NumPy 数组还是扩展数组并不明确。[`Series.array`](https://pandas.pydata.org/docs/reference/api/pandas.Series.array.html#pandas.Series.array) 将始终返回一个 [`ExtensionArray`](https://pandas.pydata.org/docs/reference/api/pandas.api.extensions.ExtensionArray.html#pandas.api.extensions.ExtensionArray)，并且永远不会复制数据。[`Series.to_numpy()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.to_numpy.html#pandas.Series.to_numpy) 将始终返回一个 NumPy 数组，可能以复制/强制转换值为代价。
2. 当你的 DataFrame 包含混合数据类型时，[`DataFrame.values`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.values.html#pandas.DataFrame.values) 可能涉及数据复制和将值强制转换为通用 dtype，这是一个相对昂贵的操作。[`DataFrame.to_numpy()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_numpy.html#pandas.DataFrame.to_numpy) 作为一个方法，更清楚地表明返回的 NumPy 数组可能不是 DataFrame 中相同数据的视图。

## 加速操作[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#accelerated-operations)

pandas 支持使用 `numexpr` 库和 `bottleneck` 库来加速某些类型的二进制数值和布尔操作。

这些库在处理大型数据集时特别有用，并能提供大幅速度提升。`numexpr` 使用智能分块、缓存和多核。`bottleneck` 是一组专门的 cython 例程，在处理包含 `nans` 的数组时特别快。

这是一个示例（使用 100 列 x 100，000 行的 `DataFrames`）：

| 操作        | 0.11.0 (ms) | 先前版本 (ms) | 与先前版本的比率 |
| ----------- | ----------- | ------------- | ---------------- |
| `df1 > df2` | 13.32       | 125.35        | 0.1063           |
| `df1 * df2` | 21.71       | 36.63         | 0.5928           |
| `df1 + df2` | 22.04       | 36.50         | 0.6039           |

强烈建议安装这两个库。更多安装信息请参见 [推荐依赖项](https://pandas.pydata.org/docs/getting_started/install.html#install-recommended-dependencies) 部分。

默认情况下两者都已启用使用，你可以通过设置选项来控制：

```python
pd.set_option("compute.use_bottleneck", False)
pd.set_option("compute.use_numexpr", False)
```

## 灵活的二进制操作[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#flexible-binary-operations)

在 pandas 数据结构之间的二进制操作中，有两个关键点需要注意：

*   高维对象（例如 DataFrame）和低维对象（例如 Series）之间的广播行为。
*   计算中的缺失数据。

我们将演示如何独立处理这些问题，尽管它们可以同时处理。

### 匹配 / 广播行为[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#matching-broadcasting-behavior)

DataFrame 具有 [`add()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.add.html#pandas.DataFrame.add)、[`sub()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sub.html#pandas.DataFrame.sub)、[`mul()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.mul.html#pandas.DataFrame.mul)、[`div()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.div.html#pandas.DataFrame.div) 以及相关函数 [`radd()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.radd.html#pandas.DataFrame.radd)、[`rsub()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rsub.html#pandas.DataFrame.rsub)、…… 等方法用于执行二进制操作。对于广播行为，Series 输入是主要的关注点。使用这些函数，你可以通过 **axis** 关键字来匹配 *index*（索引）或 *columns*（列）：

```python
In [18]: df = pd.DataFrame(
   ....:     {
   ....:         "one": pd.Series(np.random.randn(3), index=["a", "b", "c"]),
   ....:         "two": pd.Series(np.random.randn(4), index=["a", "b", "c", "d"]),
   ....:         "three": pd.Series(np.random.randn(3), index=["b", "c", "d"]),
   ....:     }
   ....: )

In [19]: df
Out[19]: 
        one       two     three
a  1.394981  1.772517       NaN
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435
d       NaN  0.279344 -0.613172

In [20]: row = df.iloc[1]

In [21]: column = df["two"]

In [22]: df.sub(row, axis="columns")
Out[22]: 
        one       two     three
a  1.051928 -0.139606       NaN
b  0.000000  0.000000  0.000000
c  0.352192 -0.433754  1.277825
d       NaN -1.632779 -0.562782

In [23]: df.sub(row, axis=1)
Out[23]: 
        one       two     three
a  1.051928 -0.139606       NaN
b  0.000000  0.000000  0.000000
c  0.352192 -0.433754  1.277825
d       NaN -1.632779 -0.562782

In [24]: df.sub(column, axis="index")
Out[24]: 
        one  two     three
a -0.377535  0.0       NaN
b -1.569069  0.0 -1.962513
c -0.783123  0.0 -0.250933
d       NaN  0.0 -0.892516

In [25]: df.sub(column, axis=0)
Out[25]: 
        one  two     three
a -0.377535  0.0       NaN
b -1.569069  0.0 -1.962513
c -0.783123  0.0 -0.250933
d       NaN  0.0 -0.892516
```

此外，你还可以将多级索引 DataFrame 的一个层级与一个 Series 对齐。

```python
In [26]: dfmi = df.copy()

In [27]: dfmi.index = pd.MultiIndex.from_tuples(
   ....:     [(1, "a"), (1, "b"), (1, "c"), (2, "a")], names=["first", "second"]
   ....: )

In [28]: dfmi.sub(column, axis=0, level="second")
Out[28]: 
                   one       two     three
first second                            
1     a      -0.377535  0.000000       NaN
      b      -1.569069  0.000000 -1.962513
      c      -0.783123  0.000000 -0.250933
2     a            NaN -1.493173 -2.385688
```

Series 和 Index 也支持内置的 [`divmod()`](https://docs.python.org/3/library/functions.html#divmod) 函数。此函数同时执行地板除和取模运算，返回一个与左侧操作数类型相同的二元组。例如：

```python
In [29]: s = pd.Series(np.arange(10))

In [30]: s
Out[30]: 
0    0
1    1
2    2
3    3
4    4
5    5
6    6
7    7
8    8
9    9
dtype: int64

In [31]: div, rem = divmod(s, 3)

In [32]: div
Out[32]: 
0    0
1    0
2    0
3    1
4    1
5    1
6    2
7    2
8    2
9    3
dtype: int64

In [33]: rem
Out[33]: 
0    0
1    1
2    2
3    0
4    1
5    2
6    0
7    1
8    2
9    0
dtype: int64

In [34]: idx = pd.Index(np.arange(10))

In [35]: idx
Out[35]: Index([0, 1, 2, 3, 4, 5, 6, 7, 8, 9], dtype='int64')

In [36]: div, rem = divmod(idx, 3)

In [37]: div
Out[37]: Index([0, 0, 0, 1, 1, 1, 2, 2, 2, 3], dtype='int64')

In [38]: rem
Out[38]: Index([0, 1, 2, 0, 1, 2, 0, 1, 2, 0], dtype='int64')
```

我们也可以进行逐元素的 [`divmod()`](https://docs.python.org/3/library/functions.html#divmod) 运算：

```python
In [39]: div, rem = divmod(s, [2, 2, 3, 3, 4, 4, 5, 5, 6, 6])

In [40]: div
Out[40]: 
0    0
1    0
2    0
3    1
4    1
5    1
6    1
7    1
8    1
9    1
dtype: int64

In [41]: rem
Out[41]: 
0    0
1    1
2    2
3    0
4    0
5    1
6    1
7    2
8    2
9    3
dtype: int64
```

### 缺失数据 / 带填充值的操作[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#missing-data-operations-with-fill-values)

在 Series 和 DataFrame 中，算术函数具有输入 *fill_value* 的选项，即当一个位置最多只有一个值缺失时要替换的值。例如，当添加两个 DataFrame 对象时，你可能希望将 NaN 视为 0，除非两个 DataFrame 都缺少该值，在这种情况下结果将是 NaN（如果需要，你可以稍后使用 `fillna` 将 NaN 替换为其他值）。

```python
In [42]: df2 = df.copy()

In [43]: df2.loc["a", "three"] = 1.0

In [44]: df
Out[44]: 
        one       two     three
a  1.394981  1.772517       NaN
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435
d       NaN  0.279344 -0.613172

In [45]: df2
Out[45]: 
        one       two     three
a  1.394981  1.772517  1.000000
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435
d       NaN  0.279344 -0.613172

In [46]: df + df2
Out[46]: 
        one       two     three
a  2.789963  3.545034       NaN
b  0.686107  3.824246 -0.100780
c  1.390491  2.956737  2.454870
d       NaN  0.558688 -1.226343

In [47]: df.add(df2, fill_value=0)
Out[47]: 
        one       two     three
a  2.789963  3.545034  1.000000
b  0.686107  3.824246 -0.100780
c  1.390491  2.956737  2.454870
d       NaN  0.558688 -1.226343
```

### 灵活的比较[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#flexible-comparisons)

Series 和 DataFrame 具有二进制比较方法 `eq`、`ne`、`lt`、`gt`、`le` 和 `ge`，其行为类似于上述二进制算术操作：

```python
In [48]: df.gt(df2)
Out[48]: 
     one    two  three
a  False  False  False
b  False  False  False
c  False  False  False
d  False  False  False

In [49]: df2.ne(df)
Out[49]: 
     one    two  three
a  False  False   True
b  False  False  False
c  False  False  False
d   True  False  False
```

这些操作产生一个与左侧输入对象类型相同的 pandas 对象，其 dtype 为 `bool`。这些 `boolean` 对象可用于索引操作，参见 [布尔索引](https://pandas.pydata.org/docs/user_guide/indexing.html#indexing-boolean) 部分。

### 布尔归约[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#boolean-reductions)

你可以应用归约操作：[`empty`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.empty.html#pandas.DataFrame.empty)、[`any()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.any.html#pandas.DataFrame.any)、[`all()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.all.html#pandas.DataFrame.all) 和 [`bool()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.bool.html#pandas.DataFrame.bool) 来提供汇总布尔结果的方法。

```python
In [50]: (df > 0).all()
Out[50]: 
one      False
two       True
three    False
dtype: bool

In [51]: (df > 0).any()
Out[51]: 
one      True
two      True
three    True
dtype: bool
```

你可以归约到一个最终的布尔值。

```python
In [52]: (df > 0).any().any()
Out[52]: True
```

你可以通过 [`empty`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.empty.html#pandas.DataFrame.empty) 属性来测试 pandas 对象是否为空。

```python
In [53]: df.empty
Out[53]: False

In [54]: pd.DataFrame(columns=list("ABC")).empty
Out[54]: True
```

警告

断言 pandas 对象的真值将引发错误，因为测试其是否为空或其值是否存在是模糊不清的。

```python
In [55]: if df:
   ....:     print(True)
   ....: 
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-55-318d08b2571a> in ?()
----> 1 if df:
      2     print(True)

~/work/pandas/pandas/pandas/core/generic.py in ?(self)
   1578     @final
   1579     def __nonzero__(self) -> NoReturn:
-> 1580         raise ValueError(
   1581             f"The truth value of a {type(self).__name__} is ambiguous. "
   1582             "Use a.empty, a.bool(), a.item(), a.any() or a.all()."
   1583         )

ValueError: The truth value of a DataFrame is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
In [56]: df and df2
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-56-b241b64bb471> in ?()
----> 1 df and df2

~/work/pandas/pandas/pandas/core/generic.py in ?(self)
   1578     @final
   1579     def __nonzero__(self) -> NoReturn:
-> 1580         raise ValueError(
   1581             f"The truth value of a {type(self).__name__} is ambiguous. "
   1582             "Use a.empty, a.bool(), a.item(), a.any() or a.all()."
   1583         )

ValueError: The truth value of a DataFrame is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
```

更详细的讨论请参见 [陷阱](https://pandas.pydata.org/docs/user_guide/gotchas.html#gotchas-truth)。

### 比较对象是否等价[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#comparing-if-objects-are-equivalent)

你可能会发现，通常有多种方法可以计算相同的结果。作为一个简单的例子，考虑 `df + df` 和 `df * 2`。要测试这两个计算是否产生相同的结果，使用上面展示的工具，你可能会想使用 `(df + df == df * 2).all()`。但实际上，这个表达式是 False：

```python
In [57]: df + df == df * 2
Out[57]: 
     one   two  three
a   True  True  False
b   True  True   True
c   True  True   True
d  False  True   True

In [58]: (df + df == df * 2).all()
Out[58]: 
one      False
two       True
three    False
dtype: bool
```

注意，布尔 DataFrame `df + df == df * 2` 包含一些 False 值！这是因为 NaN 不比较为相等：

```python
In [59]: np.nan == np.nan
Out[59]: False
```

因此，NDFrames（如 Series 和 DataFrame）有一个 [`equals()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.equals.html#pandas.DataFrame.equals) 方法来测试相等性，对应位置的 NaN 被视为相等。

```python
In [60]: (df + df).equals(df * 2)
Out[60]: True
```

注意，Series 或 DataFrame 的索引需要按相同顺序排列，相等性才为 True：

```python
In [61]: df1 = pd.DataFrame({"col": ["foo", 0, np.nan]})

In [62]: df2 = pd.DataFrame({"col": [np.nan, 0, "foo"]}, index=[2, 1, 0])

In [63]: df1.equals(df2)
Out[63]: False

In [64]: df1.equals(df2.sort_index())
Out[64]: True
```

### 比较类数组对象[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#comparing-array-like-objects)

当将 pandas 数据结构与标量值进行比较时，你可以方便地执行逐元素比较：

```python
In [65]: pd.Series(["foo", "bar", "baz"]) == "foo"
Out[65]: 
0     True
1    False
2    False
dtype: bool

In [66]: pd.Index(["foo", "bar", "baz"]) == "foo"
Out[66]: array([ True, False, False])
```

pandas 还处理相同长度的不同类数组对象之间的逐元素比较：

```python
In [67]: pd.Series(["foo", "bar", "baz"]) == pd.Index(["foo", "bar", "qux"])
Out[67]: 
0     True
1     True
2    False
dtype: bool

In [68]: pd.Series(["foo", "bar", "baz"]) == np.array(["foo", "bar", "qux"])
Out[68]: 
0     True
1     True
2    False
dtype: bool
```

尝试比较不同长度的 `Index` 或 `Series` 对象将引发 ValueError：

```python
In [69]: pd.Series(['foo', 'bar', 'baz']) == pd.Series(['foo', 'bar'])
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
Cell In[69], line 1
----> 1 pd.Series(['foo', 'bar', 'baz']) == pd.Series(['foo', 'bar'])

File ~/work/pandas/pandas/pandas/core/ops/common.py:76, in _unpack_zerodim_and_defer.<locals>.new_method(self, other)
     72             return NotImplemented
     74 other = item_from_zerodim(other)
---> 76 return method(self, other)

File ~/work/pandas/pandas/pandas/core/arraylike.py:40, in OpsMixin.__eq__(self, other)
     38 @unpack_zerodim_and_defer("__eq__")
     39 def __eq__(self, other):
---> 40     return self._cmp_method(other, operator.eq)

File ~/work/pandas/pandas/pandas/core/series.py:6133, in Series._cmp_method(self, other, op)
   6130 res_name = ops.get_op_result_name(self, other)
   6132 if isinstance(other, Series) and not self._indexed_same(other):
-> 6133     raise ValueError("Can only compare identically-labeled Series objects")
   6135 lvalues = self._values
   6136 rvalues = extract_array(other, extract_numpy=True, extract_range=True)

ValueError: Can only compare identically-labeled Series objects

In [70]: pd.Series(['foo', 'bar', 'baz']) == pd.Series(['foo'])
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
Cell In[70], line 1
----> 1 pd.Series(['foo', 'bar', 'baz']) == pd.Series(['foo'])

File ~/work/pandas/pandas/pandas/core/ops/common.py:76, in _unpack_zerodim_and_defer.<locals>.new_method(self, other)
     72             return NotImplemented
     74 other = item_from_zerodim(other)
---> 76 return method(self, other)

File ~/work/pandas/pandas/pandas/core/arraylike.py:40, in OpsMixin.__eq__(self, other)
     38 @unpack_zerodim_and_defer("__eq__")
     39 def __eq__(self, other):
---> 40     return self._cmp_method(other, operator.eq)

File ~/work/pandas/pandas/pandas/core/series.py:6133, in Series._cmp_method(self, other, op)
   6130 res_name = ops.get_op_result_name(self, other)
   6132 if isinstance(other, Series) and not self._indexed_same(other):
-> 6133     raise ValueError("Can only compare identically-labeled Series objects")
   6135 lvalues = self._values
   6136 rvalues = extract_array(other, extract_numpy=True, extract_range=True)

ValueError: Can only compare identically-labeled Series objects
```

### 组合重叠的数据集[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#combining-overlapping-data-sets)

偶尔出现的一个问题是组合两个相似的数据集，其中一个数据集的值优先于另一个。例如，两个代表特定经济指标的数据系列，其中一个被认为是“更高质量”的。然而，质量较低的系列可能在历史上延伸得更远或具有更完整的数据覆盖。因此，我们希望组合两个 DataFrame 对象，其中一个 DataFrame 中的缺失值有条件地由另一个 DataFrame 中相同标签的值填充。实现此操作的函数是 [`combine_first()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.combine_first.html#pandas.DataFrame.combine_first)，我们进行演示：

```python
In [71]: df1 = pd.DataFrame(
   ....:     {"A": [1.0, np.nan, 3.0, 5.0, np.nan], "B": [np.nan, 2.0, 3.0, np.nan, 6.0]}
   ....: )

In [72]: df2 = pd.DataFrame(
   ....:     {
   ....:         "A": [5.0, 2.0, 4.0, np.nan, 3.0, 7.0],
   ....:         "B": [np.nan, np.nan, 3.0, 4.0, 6.0, 8.0],
   ....:     }
   ....: )

In [73]: df1
Out[73]: 
     A    B
0  1.0  NaN
1  NaN  2.0
2  3.0  3.0
3  5.0  NaN
4  NaN  6.0

In [74]: df2
Out[74]: 
     A    B
0  5.0  NaN
1  2.0  NaN
2  4.0  3.0
3  NaN  4.0
4  3.0  6.0
5  7.0  8.0

In [75]: df1.combine_first(df2)
Out[75]: 
     A    B
0  1.0  NaN
1  2.0  2.0
2  3.0  3.0
3  5.0  4.0
4  3.0  6.0
5  7.0  8.0
```

### 通用 DataFrame 组合[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#general-dataframe-combine)

上面的 [`combine_first()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.combine_first.html#pandas.DataFrame.combine_first) 方法调用了更通用的 [`DataFrame.combine()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.combine.html#pandas.DataFrame.combine)。该方法接受另一个 DataFrame 和一个组合器函数，对齐输入的 DataFrame，然后将成对的 Series（即名称相同的列）传递给组合器函数。

因此，例如，要像上面那样重现 [`combine_first()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.combine_first.html#pandas.DataFrame.combine_first)：

```python
In [76]: def combiner(x, y):
   ....:     return np.where(pd.isna(x), y, x)
   ....: 

In [77]: df1.combine(df2, combiner)
Out[77]: 
     A    B
0  1.0  NaN
1  2.0  2.0
2  3.0  3.0
3  5.0  4.0
4  3.0  6.0
5  7.0  8.0
```

## 描述性统计[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#descriptive-statistics)

有大量方法可用于在 [Series](https://pandas.pydata.org/docs/reference/series.html#api-series-stats) 和 [DataFrame](https://pandas.pydata.org/docs/reference/frame.html#api-dataframe-stats) 上计算描述性统计和其他相关操作。其中大多数是聚合操作（因此产生较低维度的结果），如 [`sum()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sum.html#pandas.DataFrame.sum)、[`mean()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.mean.html#pandas.DataFrame.mean) 和 [`quantile()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.quantile.html#pandas.DataFrame.quantile)，但其中一些，如 [`cumsum()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.cumsum.html#pandas.DataFrame.cumsum) 和 [`cumprod()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.cumprod.html#pandas.DataFrame.cumprod)，产生相同大小的对象。一般来说，这些方法接受一个 **axis** 参数，就像 *ndarray.{sum, std, …}* 一样，但轴可以通过名称或整数指定：

*   **Series**：不需要 axis 参数。
*   **DataFrame**：“index”（axis=0，默认值）或 “columns”（axis=1）。

例如：

```python
In [78]: df
Out[78]: 
        one       two     three
a  1.394981  1.772517       NaN
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435
d       NaN  0.279344 -0.613172

In [79]: df.mean(0)
Out[79]: 
one      0.811094
two      1.360588
three    0.187958
dtype: float64

In [80]: df.mean(1)
Out[80]: 
a    1.583749
b    0.734929
c    1.133683
d   -0.166914
dtype: float64
```

所有这些方法都有一个 `skipna` 选项，指示是否排除缺失数据（默认为 `True`）：

```python
In [81]: df.sum(0, skipna=False)
Out[81]: 
one           NaN
two      5.442353
three         NaN
dtype: float64

In [82]: df.sum(axis=1, skipna=True)
Out[82]: 
a    3.167498
b    2.204786
c    3.401050
d   -0.333828
dtype: float64
```

结合广播/算术行为，可以非常简洁地描述各种统计过程，如标准化（使数据均值为 0，标准差为 1）：

```python
In [83]: ts_stand = (df - df.mean()) / df.std()

In [84]: ts_stand.std()
Out[84]: 
one      1.0
two      1.0
three    1.0
dtype: float64

In [85]: xs_stand = df.sub(df.mean(1), axis=0).div(df.std(1), axis=0)

In [86]: xs_stand.std(1)
Out[86]: 
a    1.0
b    1.0
c    1.0
d    1.0
dtype: float64
```

注意，像 [`cumsum()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.cumsum.html#pandas.DataFrame.cumsum) 和 [`cumprod()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.cumprod.html#pandas.DataFrame.cumprod) 这样的方法会保留 `NaN` 值的位置。这与 [`expanding()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.expanding.html#pandas.DataFrame.expanding) 和 [`rolling()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rolling.html#pandas.DataFrame.rolling) 有些不同，因为 `NaN` 行为还受到 `min_periods` 参数的约束。

```python
In [87]: df.cumsum()
Out[87]: 
        one       two     three
a  1.394981  1.772517       NaN
b  1.738035  3.684640 -0.050390
c  2.433281  5.163008  1.177045
d       NaN  5.442353  0.563873
```

以下是常用函数的快速参考摘要表。每个函数还接受一个可选的 `level` 参数，仅当对象具有[层次化索引](https://pandas.pydata.org/docs/user_guide/advanced.html#advanced-hierarchical)时才适用。

| 函数       | 描述                      |
| ---------- | ------------------------- |
| `count`    | 非 NA 观测值的数量        |
| `sum`      | 值的总和                  |
| `mean`     | 值的平均值                |
| `median`   | 值的算术中位数            |
| `min`      | 最小值                    |
| `max`      | 最大值                    |
| `mode`     | 众数                      |
| `abs`      | 绝对值                    |
| `prod`     | 值的乘积                  |
| `std`      | 贝塞尔校正的样本标准差    |
| `var`      | 无偏方差                  |
| `sem`      | 均值的标准误              |
| `skew`     | 样本偏度（三阶矩）        |
| `kurt`     | 样本峰度（四阶矩）        |
| `quantile` | 样本分位数（在 % 处的值） |
| `cumsum`   | 累积和                    |
| `cumprod`  | 累积乘积                  |
| `cummax`   | 累积最大值                |
| `cummin`   | 累积最小值                |

请注意，NumPy 中的某些方法（如 `mean`、`std` 和 `sum`）在 Series 输入上默认会排除 NA：

```python
In [88]: np.mean(df["one"])
Out[88]: 0.8110935116651192

In [89]: np.mean(df["one"].to_numpy())
Out[89]: nan
```

[`Series.nunique()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.nunique.html#pandas.Series.nunique) 将返回 Series 中唯一非 NA 值的数量：

```python
In [90]: series = pd.Series(np.random.randn(500))

In [91]: series[20:500] = np.nan

In [92]: series[10:20] = 5

In [93]: series.nunique()
Out[93]: 11
```

### 数据摘要：describe[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#summarizing-data-describe)

有一个方便的 [`describe()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.describe.html#pandas.DataFrame.describe) 函数，它可以计算关于 Series 或 DataFrame 各列的各种摘要统计信息（当然会排除 NA）：

```python
In [94]: series = pd.Series(np.random.randn(1000))

In [95]: series[::2] = np.nan

In [96]: series.describe()
Out[96]: 
count    500.000000
mean      -0.021292
std        1.015906
min       -2.683763
25%       -0.699070
50%       -0.069718
75%        0.714483
max        3.160915
dtype: float64

In [97]: frame = pd.DataFrame(np.random.randn(1000, 5), columns=["a", "b", "c", "d", "e"])

In [98]: frame.iloc[::2] = np.nan

In [99]: frame.describe()
Out[99]: 
                a           b           c           d           e
count  500.000000  500.000000  500.000000  500.000000  500.000000
mean     0.033387    0.030045   -0.043719   -0.051686    0.005979
std      1.017152    0.978743    1.025270    1.015988    1.006695
min     -3.000951   -2.637901   -3.303099   -3.159200   -3.188821
25%     -0.647623   -0.576449   -0.712369   -0.691338   -0.691115
50%      0.047578   -0.021499   -0.023888   -0.032652   -0.025363
75%      0.729907    0.775880    0.618896    0.670047    0.649748
max      2.740139    2.752332    3.004229    2.728702    3.240991
```

你可以选择要包含在输出中的特定百分位数：

```python
In [100]: series.describe(percentiles=[0.05, 0.25, 0.75, 0.95])
Out[100]: 
count    500.000000
mean      -0.021292
std        1.015906
min       -2.683763
5%        -1.645423
25%       -0.699070
50%       -0.069718
75%        0.714483
95%        1.711409
max        3.160915
dtype: float64
```

默认情况下，中位数始终包含在内。

对于非数值 Series 对象，[`describe()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.describe.html#pandas.Series.describe) 将给出一个简单摘要，包括唯一值数量和最常出现的值：

```python
In [101]: s = pd.Series(["a", "a", "b", "b", "a", "a", np.nan, "c", "d", "a"])

In [102]: s.describe()
Out[102]: 
count     9
unique    4
top       a
freq      5
dtype: object
```

请注意，在混合类型的 DataFrame 对象上，[`describe()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.describe.html#pandas.DataFrame.describe) 会将摘要限制为仅包括数值列，如果没有数值列，则仅包括分类列：

```python
In [103]: frame = pd.DataFrame({"a": ["Yes", "Yes", "No", "No"], "b": range(4)})

In [104]: frame.describe()
Out[104]: 
              b
count  4.000000
mean   1.500000
std    1.290994
min    0.000000
25%    0.750000
50%    1.500000
75%    2.250000
max    3.000000
```

可以通过提供类型列表作为 `include`/`exclude` 参数来控制此行为。也可以使用特殊值 `all`：

```python
In [105]: frame.describe(include=["object"])
Out[105]: 
          a
count     4
unique    2
top     Yes
freq      2

In [106]: frame.describe(include=["number"])
Out[106]: 
              b
count  4.000000
mean   1.500000
std    1.290994
min    0.000000
25%    0.750000
50%    1.500000
75%    2.250000
max    3.000000

In [107]: frame.describe(include="all")
Out[107]: 
          a         b
count     4  4.000000
unique    2       NaN
top     Yes       NaN
freq      2       NaN
mean    NaN  1.500000
std     NaN  1.290994
min     NaN  0.000000
25%     NaN  0.750000
50%     NaN  1.500000
75%     NaN  2.250000
max     NaN  3.000000
```

该特性依赖于 [select_dtypes](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#basics-selectdtypes)。有关接受的输入的详细信息，请参阅该部分。

### 最小/最大值的索引[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#index-of-min-max-values)

Series 和 DataFrame 上的 [`idxmin()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.idxmin.html#pandas.DataFrame.idxmin) 和 [`idxmax()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.idxmax.html#pandas.DataFrame.idxmax) 函数计算具有最小值和最大值的索引标签：

```python
In [108]: s1 = pd.Series(np.random.randn(5))

In [109]: s1
Out[109]: 
0    1.118076
1   -0.352051
2   -1.242883
3   -1.277155
4   -0.641184
dtype: float64

In [110]: s1.idxmin(), s1.idxmax()
Out[110]: (3, 0)

In [111]: df1 = pd.DataFrame(np.random.randn(5, 3), columns=["A", "B", "C"])

In [112]: df1
Out[112]: 
          A         B         C
0 -0.327863 -0.946180 -0.137570
1 -0.186235 -0.257213 -0.486567
2 -0.507027 -0.871259 -0.111110
3  2.000339 -2.430505  0.089759
4 -0.321434 -0.033695  0.096271

In [113]: df1.idxmin(axis=0)
Out[113]: 
A    2
B    3
C    1
dtype: int64

In [114]: df1.idxmax(axis=1)
Out[114]: 
0    C
1    A
2    C
3    A
4    C
dtype: object
```

当有多个行（或列）匹配最小值或最大值时，[`idxmin()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.idxmin.html#pandas.DataFrame.idxmin) 和 [`idxmax()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.idxmax.html#pandas.DataFrame.idxmax) 返回第一个匹配的索引：

```python
In [115]: df3 = pd.DataFrame([2, 1, 1, 3, np.nan], columns=["A"], index=list("edcba"))

In [116]: df3
Out[116]: 
     A
e  2.0
d  1.0
c  1.0
b  3.0
a  NaN

In [117]: df3["A"].idxmin()
Out[117]: 'd'
```

注意

在 NumPy 中，`idxmin` 和 `idxmax` 被称为 `argmin` 和 `argmax`。

### 值计数（直方图）/ 众数[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#value-counts-histogramming-mode)

Series 的 [`value_counts()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.value_counts.html#pandas.Series.value_counts) 方法计算一维数组值的直方图。它也可以作为普通数组的函数使用：

```python
In [118]: data = np.random.randint(0, 7, size=50)

In [119]: data
Out[119]: 
array([6, 6, 2, 3, 5, 3, 2, 5, 4, 5, 4, 3, 4, 5, 0, 2, 0, 4, 2, 0, 3, 2,
       2, 5, 6, 5, 3, 4, 6, 4, 3, 5, 6, 4, 3, 6, 2, 6, 6, 2, 3, 4, 2, 1,
       6, 2, 6, 1, 5, 4])

In [120]: s = pd.Series(data)

In [121]: s.value_counts()
Out[121]: 
6    10
2    10
4     9
3     8
5     8
0     3
1     2
Name: count, dtype: int64
```

[`value_counts()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.value_counts.html#pandas.DataFrame.value_counts) 方法可用于统计跨多列的组合。默认情况下使用所有列，但可以使用 `subset` 参数选择子集。

```python
In [122]: data = {"a": [1, 2, 3, 4], "b": ["x", "x", "y", "y"]}

In [123]: frame = pd.DataFrame(data)

In [124]: frame.value_counts()
Out[124]: 
a  b
1  x    1
2  x    1
3  y    1
4  y    1
Name: count, dtype: int64
```

类似地，你可以获取 Series 或 DataFrame 中最常出现的值，即众数：

```python
In [125]: s5 = pd.Series([1, 1, 3, 3, 3, 5, 5, 7, 7, 7])

In [126]: s5.mode()
Out[126]: 
0    3
1    7
dtype: int64

In [127]: df5 = pd.DataFrame(
   .....:     {
   .....:         "A": np.random.randint(0, 7, size=50),
   .....:         "B": np.random.randint(-10, 15, size=50),
   .....:     }
   .....: )

In [128]: df5.mode()
Out[128]: 
     A   B
0  1.0  -9
1  NaN  10
2  NaN  13
```

### 离散化和分位数化[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#discretization-and-quantiling)

可以使用 [`cut()`](https://pandas.pydata.org/docs/reference/api/pandas.cut.html#pandas.cut)（基于值的分箱）和 [`qcut()`](https://pandas.pydata.org/docs/reference/api/pandas.qcut.html#pandas.qcut)（基于样本分位数的分箱）函数对连续值进行离散化：

```python
In [129]: arr = np.random.randn(20)

In [130]: factor = pd.cut(arr, 4)

In [131]: factor
Out[131]: 
[(-0.251, 0.464], (-0.968, -0.251], (0.464, 1.179], (-0.251, 0.464], (-0.968, -0.251], ..., (-0.251, 0.464], (-0.968, -0.251], (-0.968, -0.251], (-0.968, -0.251], (-0.968, -0.251]]
Length: 20
Categories (4, interval[float64, right]): [(-0.968, -0.251] < (-0.251, 0.464] < (0.464, 1.179] <
                                           (1.179, 1.893]]

In [132]: factor = pd.cut(arr, [-5, -1, 0, 1, 5])

In [133]: factor
Out[133]: 
[(0, 1], (-1, 0], (0, 1], (0, 1], (-1, 0], ..., (-1, 0], (-1, 0], (-1, 0], (-1, 0], (-1, 0]]
Length: 20
Categories (4, interval[int64, right]): [(-5, -1] < (-1, 0] < (0, 1] < (1, 5]]
```

[`qcut()`](https://pandas.pydata.org/docs/reference/api/pandas.qcut.html#pandas.qcut) 计算样本分位数。例如，我们可以将一些正态分布的数据切成等大小的四分位数：

```python
In [134]: arr = np.random.randn(30)

In [135]: factor = pd.qcut(arr, [0, 0.25, 0.5, 0.75, 1])

In [136]: factor
Out[136]: 
[(0.569, 1.184], (-2.278, -0.301], (-2.278, -0.301], (0.569, 1.184], (0.569, 1.184], ..., (-0.301, 0.569], (1.184, 2.346], (1.184, 2.346], (-0.301, 0.569], (-2.278, -0.301]]
Length: 30
Categories (4, interval[float64, right]): [(-2.278, -0.301] < (-0.301, 0.569] < (0.569, 1.184] <
                                           (1.184, 2.346]]
```

我们也可以传递无穷值来定义分箱：

```python
In [137]: arr = np.random.randn(20)

In [138]: factor = pd.cut(arr, [-np.inf, 0, np.inf])

In [139]: factor
Out[139]: 
[(-inf, 0.0], (0.0, inf], (0.0, inf], (-inf, 0.0], (-inf, 0.0], ..., (-inf, 0.0], (-inf, 0.0], (-inf, 0.0], (0.0, inf], (0.0, inf]]
Length: 20
Categories (2, interval[float64, right]): [(-inf, 0.0] < (0.0, inf]]
```

## 函数应用[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#function-application)

要将你自己的或其他库的函数应用于 pandas 对象，你应该了解以下三种方法。使用哪种方法取决于你的函数是期望操作整个 `DataFrame` 或 `Series`，还是按行或按列操作，或是按元素操作。

1.  [表级函数应用](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#tablewise-function-application)：[`pipe()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pipe.html#pandas.DataFrame.pipe)
2.  [行级或列级函数应用](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#row-or-column-wise-function-application)：[`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply)
3.  [聚合 API](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#aggregation-api)：[`agg()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.agg.html#pandas.DataFrame.agg) 和 [`transform()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.transform.html#pandas.DataFrame.transform)
4.  [应用逐元素函数](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#applying-elementwise-functions)：[`map()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.map.html#pandas.DataFrame.map)

### 表级函数应用[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#tablewise-function-application)

`DataFrames` 和 `Series` 可以传递给函数。但是，如果需要在链式调用中调用函数，请考虑使用 [`pipe()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pipe.html#pandas.DataFrame.pipe) 方法。

首先进行一些设置：

```python
In [140]: def extract_city_name(df):
   .....:     """
   .....:     Chicago, IL -> Chicago for city_name column
   .....:     """
   .....:     df["city_name"] = df["city_and_code"].str.split(",").str.get(0)
   .....:     return df
   .....: 

In [141]: def add_country_name(df, country_name=None):
   .....:     """
   .....:     Chicago -> Chicago-US for city_name column
   .....:     """
   .....:     col = "city_name"
   .....:     df["city_and_country"] = df[col] + country_name
   .....:     return df
   .....: 

In [142]: df_p = pd.DataFrame({"city_and_code": ["Chicago, IL"]})
```

`extract_city_name` 和 `add_country_name` 是接受并返回 `DataFrames` 的函数。

现在比较以下两种方式：

```python
In [143]: add_country_name(extract_city_name(df_p), country_name="US")
Out[143]: 
  city_and_code city_name city_and_country
0   Chicago, IL   Chicago        ChicagoUS
```

等同于：

```python
In [144]: df_p.pipe(extract_city_name).pipe(add_country_name, country_name="US")
Out[144]: 
  city_and_code city_name city_and_country
0   Chicago, IL   Chicago        ChicagoUS
```

pandas 鼓励第二种风格，即方法链。`pipe` 使你可以在方法链中轻松使用你自己的或其他库的函数，与 pandas 的方法一起使用。

在上面的例子中，函数 `extract_city_name` 和 `add_country_name` 都期望将 `DataFrame` 作为第一个位置参数。如果你想应用的函数将其数据作为第二个参数，比如第二个参数 `data` 呢？在这种情况下，向 `pipe` 提供一个 `(callable, data_keyword)` 元组。`.pipe` 会将 `DataFrame` 路由到元组中指定的参数。

例如，我们可以使用 statsmodels 进行回归分析。他们的 API 期望第一个参数是公式，第二个参数 `data` 是 `DataFrame`。我们将函数、关键字对 `(sm.ols, 'data')` 传递给 `pipe`：

```python
In [147]: import statsmodels.formula.api as sm

In [148]: bb = pd.read_csv("data/baseball.csv", index_col="id")

In [149]: (
   .....:     bb.query("h > 0")
   .....:     .assign(ln_h=lambda df: np.log(df.h))
   .....:     .pipe((sm.ols, "data"), "hr ~ ln_h + year + g + C(lg)")
   .....:     .fit()
   .....:     .summary()
   .....: )
   .....:
Out[149]:
<class 'statsmodels.iolib.summary.Summary'>
"""
                           OLS Regression Results
==============================================================================
Dep. Variable:                     hr   R-squared:                       0.685
Model:                            OLS   Adj. R-squared:                  0.665
Method:                 Least Squares   F-statistic:                     34.28
Date:                Tue, 22 Nov 2022   Prob (F-statistic):           3.48e-15
Time:                        05:34:17   Log-Likelihood:                -205.92
No. Observations:                  68   AIC:                             421.8
Df Residuals:                      63   BIC:                             432.9
Df Model:                           4
Covariance Type:            nonrobust
===============================================================================
                  coef    std err          t      P>|t|      [0.025      0.975]
-------------------------------------------------------------------------------
Intercept   -8484.7720   4664.146     -1.819      0.074   -1.78e+04     835.780
C(lg)[T.NL]    -2.2736      1.325     -1.716      0.091      -4.922       0.375
ln_h           -1.3542      0.875     -1.547      0.127      -3.103       0.395
year            4.2277      2.324      1.819      0.074      -0.417       8.872
g               0.1841      0.029      6.258      0.000       0.125       0.243
==============================================================================
Omnibus:                       10.875   Durbin-Watson:                   1.999
Prob(Omnibus):                  0.004   Jarque-Bera (JB):               17.298
Skew:                           0.537   Prob(JB):                     0.000175
Kurtosis:                       5.225   Cond. No.                     1.49e+07
==============================================================================

Notes:
[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.
[2] The condition number is large, 1.49e+07. This might indicate that there are
strong multicollinearity or other numerical problems.
"""
```

`pipe` 方法的灵感来自 Unix 管道，以及最近引入流行的 `(%>%)`（读作管道）运算符用于 [R](https://www.r-project.org/) 的 [dplyr](https://github.com/tidyverse/dplyr) 和 [magrittr](https://github.com/tidyverse/magrittr)。这里 `pipe` 的实现非常简洁，并且在 Python 中感觉很自然。我们鼓励你查看 [`pipe()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pipe.html#pandas.DataFrame.pipe) 的源代码。

### 行级或列级函数应用[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#row-or-column-wise-function-application)

可以使用 [`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply) 方法将任意函数沿着 DataFrame 的轴应用，该方法像描述性统计方法一样，接受一个可选的 `axis` 参数：

```python
In [145]: df.apply(lambda x: np.mean(x))
Out[145]: 
one      0.811094
two      1.360588
three    0.187958
dtype: float64

In [146]: df.apply(lambda x: np.mean(x), axis=1)
Out[146]: 
a    1.583749
b    0.734929
c    1.133683
d   -0.166914
dtype: float64

In [147]: df.apply(lambda x: x.max() - x.min())
Out[147]: 
one      1.051928
two      1.632779
three    1.840607
dtype: float64

In [148]: df.apply(np.cumsum)
Out[148]: 
        one       two     three
a  1.394981  1.772517       NaN
b  1.738035  3.684640 -0.050390
c  2.433281  5.163008  1.177045
d       NaN  5.442353  0.563873

In [149]: df.apply(np.exp)
Out[149]: 
        one       two     three
a  4.034899  5.885648       NaN
b  1.409244  6.767440  0.950858
c  2.004201  4.385785  3.412466
d       NaN  1.322262  0.541630
```

[`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply) 方法也会根据字符串方法名进行分发。

```python
In [150]: df.apply("mean")
Out[150]: 
one      0.811094
two      1.360588
three    0.187958
dtype: float64

In [151]: df.apply("mean", axis=1)
Out[151]: 
a    1.583749
b    0.734929
c    1.133683
d   -0.166914
dtype: float64
```

传递给 [`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply) 的函数的返回类型会影响 `DataFrame.apply` 的最终输出的默认行为：

*   如果应用的函数返回一个 `Series`，则最终输出是一个 `DataFrame`。其列与应用函数返回的 `Series` 的索引相匹配。
*   如果应用的函数返回任何其他类型，则最终输出是一个 `Series`。

可以使用 `result_type` 覆盖此默认行为，它接受三个选项：`reduce`、`broadcast` 和 `expand`。这些将决定类似列表的返回值如何扩展（或不扩展）为 `DataFrame`。

[`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply) 结合一些巧思可以用来回答关于数据集的许多问题。例如，假设我们想要提取每列最大值出现的日期：

```python
In [152]: tsdf = pd.DataFrame(
   .....:     np.random.randn(1000, 3),
   .....:     columns=["A", "B", "C"],
   .....:     index=pd.date_range("1/1/2000", periods=1000),
   .....: )

In [153]: tsdf.apply(lambda x: x.idxmax())
Out[153]: 
A   2000-08-06
B   2001-01-18
C   2001-07-18
dtype: datetime64[ns]
```

你还可以向 [`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply) 方法传递额外的参数和关键字参数。

```python
In [154]: def subtract_and_divide(x, sub, divide=1):
   .....:     return (x - sub) / divide
   .....: 

In [155]: df_udf = pd.DataFrame(np.ones((2, 2)))

In [156]: df_udf.apply(subtract_and_divide, args=(5,), divide=3)
Out[156]: 
          0         1
0 -1.333333 -1.333333
1 -1.333333 -1.333333
```

另一个有用的特性是能够传递 Series 方法，以便在每个列或行上执行某些 Series 操作：

```python
In [157]: tsdf = pd.DataFrame(
   .....:     np.random.randn(10, 3),
   .....:     columns=["A", "B", "C"],
   .....:     index=pd.date_range("1/1/2000", periods=10),
   .....: )

In [158]: tsdf.iloc[3:7] = np.nan

In [159]: tsdf
Out[159]: 
                   A         B         C
2000-01-01 -0.158131 -0.232466  0.321604
2000-01-02 -1.810340 -3.105758  0.433834
2000-01-03 -1.209847 -1.156793 -0.136794
2000-01-04       NaN       NaN       NaN
2000-01-05       NaN       NaN       NaN
2000-01-06       NaN       NaN       NaN
2000-01-07       NaN       NaN       NaN
2000-01-08 -0.653602  0.178875  1.008298
2000-01-09  1.007996  0.462824  0.254472
2000-01-10  0.307473  0.600337  1.643950

In [160]: tsdf.apply(pd.Series.interpolate)
Out[160]: 
                   A         B         C
2000-01-01 -0.158131 -0.232466  0.321604
2000-01-02 -1.810340 -3.105758  0.433834
2000-01-03 -1.209847 -1.156793 -0.136794
2000-01-04 -1.098598 -0.889659  0.092225
2000-01-05 -0.987349 -0.622526  0.321243
2000-01-06 -0.876100 -0.355392  0.550262
2000-01-07 -0.764851 -0.088259  0.779280
2000-01-08 -0.653602  0.178875  1.008298
2000-01-09  1.007996  0.462824  0.254472
2000-01-10  0.307473  0.600337  1.643950
```

最后，[`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply) 接受一个参数 `raw`，默认为 False，它在应用函数之前将每一行或列转换为 Series。当设置为 True 时，传递的函数将接收一个 ndarray 对象，如果你不需要索引功能，这会带来性能上的优势。

### 聚合 API[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#aggregation-api)

聚合 API 允许以一种简洁的方式表达可能的多个聚合操作。此 API 在 pandas 对象之间是相似的，参见 [groupby API](https://pandas.pydata.org/docs/user_guide/groupby.html#groupby-aggregate)、[窗口 API](https://pandas.pydata.org/docs/user_guide/window.html#window-overview) 和 [重采样 API](https://pandas.pydata.org/docs/user_guide/timeseries.html#timeseries-aggregate)。聚合的入口点是 [`DataFrame.aggregate()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.aggregate.html#pandas.DataFrame.aggregate)，或其别名 [`DataFrame.agg()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.agg.html#pandas.DataFrame.agg)。

我们将使用与上面类似的起始框架：

```python
In [161]: tsdf = pd.DataFrame(
   .....:     np.random.randn(10, 3),
   .....:     columns=["A", "B", "C"],
   .....:     index=pd.date_range("1/1/2000", periods=10),
   .....: )

In [162]: tsdf.iloc[3:7] = np.nan

In [163]: tsdf
Out[163]: 
                   A         B         C
2000-01-01  1.257606  1.004194  0.167574
2000-01-02 -0.749892  0.288112 -0.757304
2000-01-03 -0.207550 -0.298599  0.116018
2000-01-04       NaN       NaN       NaN
2000-01-05       NaN       NaN       NaN
2000-01-06       NaN       NaN       NaN
2000-01-07       NaN       NaN       NaN
2000-01-08  0.814347 -0.257623  0.869226
2000-01-09 -0.250663 -1.206601  0.896839
2000-01-10  2.169758 -1.333363  0.283157
```

使用单个函数等同于 [`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply)。你也可以传递命名方法作为字符串。这些将返回聚合输出的 `Series`：

```python
In [164]: tsdf.agg(lambda x: np.sum(x))
Out[164]: 
A    3.033606
B   -1.803879
C    1.575510
dtype: float64

In [165]: tsdf.agg("sum")
Out[165]: 
A    3.033606
B   -1.803879
C    1.575510
dtype: float64

# 这些等价于 ``.sum()``，因为我们在聚合单个函数
In [166]: tsdf.sum()
Out[166]: 
A    3.033606
B   -1.803879
C    1.575510
dtype: float64
```

在 `Series` 上的单一聚合将返回标量值：

```python
In [167]: tsdf["A"].agg("sum")
Out[167]: 3.033606102414146
```

#### 使用多个函数进行聚合[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#aggregating-with-multiple-functions)

你可以传递多个聚合参数作为列表。每个传递函数的结果将成为结果 `DataFrame` 中的一行。这些行自然地从聚合函数命名。

```python
In [168]: tsdf.agg(["sum"])
Out[168]: 
            A         B        C
sum  3.033606 -1.803879  1.57551
```

多个函数产生多行：

```python
In [169]: tsdf.agg(["sum", "mean"])
Out[169]: 
             A         B         C
sum   3.033606 -1.803879  1.575510
mean  0.505601 -0.300647  0.262585
```

在 `Series` 上，多个函数返回一个 `Series`，由函数名索引：

```python
In [170]: tsdf["A"].agg(["sum", "mean"])
Out[170]: 
sum     3.033606
mean    0.505601
Name: A, dtype: float64
```

传递 `lambda` 函数将产生名为 `<lambda>` 的行：

```python
In [171]: tsdf["A"].agg(["sum", lambda x: x.mean()])
Out[171]: 
sum         3.033606
<lambda>    0.505601
Name: A, dtype: float64
```

传递命名函数将使用该名称作为行名：

```python
In [172]: def mymean(x):
   .....:     return x.mean()
   .....: 

In [173]: tsdf["A"].agg(["sum", mymean])
Out[173]: 
sum       3.033606
mymean    0.505601
Name: A, dtype: float64
```

#### 使用字典进行聚合[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#aggregating-with-a-dict)

向 `DataFrame.agg` 传递一个列名到标量或标量列表的字典，允许你自定义哪些函数应用于哪些列。请注意，结果没有特定的顺序，你可以使用 `OrderedDict` 来保证顺序。

```python
In [174]: tsdf.agg({"A": "mean", "B": "sum"})
Out[174]: 
A    0.505601
B   -1.803879
dtype: float64
```

传递列表将生成 `DataFrame` 输出。你将获得所有聚合器的类矩阵输出。输出将包含所有唯一函数。那些未为特定列注明的函数将是 `NaN`：

```python
In [175]: tsdf.agg({"A": ["mean", "min"], "B": "sum"})
Out[175]: 
             A         B
mean  0.505601       NaN
min  -0.749892       NaN
sum        NaN -1.803879
```

#### 自定义描述[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#custom-describe)

使用 `.agg()` 可以轻松创建自定义描述函数，类似于内置的 [describe 函数](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#basics-describe)。

```python
In [176]: from functools import partial

In [177]: q_25 = partial(pd.Series.quantile, q=0.25)

In [178]: q_25.__name__ = "25%"

In [179]: q_75 = partial(pd.Series.quantile, q=0.75)

In [180]: q_75.__name__ = "75%"

In [181]: tsdf.agg(["count", "mean", "std", "min", q_25, "median", q_75, "max"])
Out[181]: 
               A         B         C
count   6.000000  6.000000  6.000000
mean    0.505601 -0.300647  0.262585
std     1.103362  0.887508  0.606860
min    -0.749892 -1.333363 -0.757304
25%    -0.239885 -0.979600  0.128907
median  0.303398 -0.278111  0.225365
75%     1.146791  0.151678  0.722709
max     2.169758  1.004194  0.896839
```

### Transform API[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#transform-api)

[`transform()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.transform.html#pandas.DataFrame.transform) 方法返回一个索引与原始对象相同（大小相同）的对象。此 API 允许你同时提供*多个*操作，而不是逐个操作。其 API 与 `.agg` API 非常相似。

我们创建一个与上面部分类似的框架。

```python
In [182]: tsdf = pd.DataFrame(
   .....:     np.random.randn(10, 3),
   .....:     columns=["A", "B", "C"],
   .....:     index=pd.date_range("1/1/2000", periods=10),
   .....: )

In [183]: tsdf.iloc[3:7] = np.nan

In [184]: tsdf
Out[184]: 
                   A         B         C
2000-01-01 -0.428759 -0.864890 -0.675341
2000-01-02 -0.168731  1.338144 -1.279321
2000-01-03 -1.621034  0.438107  0.903794
2000-01-04       NaN       NaN       NaN
2000-01-05       NaN       NaN       NaN
2000-01-06       NaN       NaN       NaN
2000-01-07       NaN       NaN       NaN
2000-01-08  0.254374 -1.240447 -0.201052
2000-01-09 -0.157795  0.791197 -1.144209
2000-01-10 -0.030876  0.371900  0.061932
```

转换整个框架。`.transform()` 允许输入函数为：NumPy 函数、字符串函数名或用户定义函数。

```python
In [185]: tsdf.transform(np.abs)
Out[185]: 
                   A         B         C
2000-01-01  0.428759  0.864890  0.675341
2000-01-02  0.168731  1.338144  1.279321
2000-01-03  1.621034  0.438107  0.903794
2000-01-04       NaN       NaN       NaN
2000-01-05       NaN       NaN       NaN
2000-01-06       NaN       NaN       NaN
2000-01-07       NaN       NaN       NaN
2000-01-08  0.254374  1.240447  0.201052
2000-01-09  0.157795  0.791197  1.144209
2000-01-10  0.030876  0.371900  0.061932

In [186]: tsdf.transform("abs")
Out[186]: 
                   A         B         C
2000-01-01  0.428759  0.864890  0.675341
2000-01-02  0.168731  1.338144  1.279321
2000-01-03  1.621034  0.438107  0.903794
2000-01-04       NaN       NaN       NaN
2000-01-05       NaN       NaN       NaN
2000-01-06       NaN       NaN       NaN
2000-01-07       NaN       NaN       NaN
2000-01-08  0.254374  1.240447  0.201052
2000-01-09  0.157795  0.791197  1.144209
2000-01-10  0.030876  0.371900  0.061932

In [187]: tsdf.transform(lambda x: x.abs())
Out[187]: 
                   A         B         C
2000-01-01  0.428759  0.864890  0.675341
2000-01-02  0.168731  1.338144  1.279321
2000-01-03  1.621034  0.438107  0.903794
2000-01-04       NaN       NaN       NaN
2000-01-05       NaN       NaN       NaN
2000-01-06       NaN       NaN       NaN
2000-01-07       NaN       NaN       NaN
2000-01-08  0.254374  1.240447  0.201052
2000-01-09  0.157795  0.791197  1.144209
2000-01-10  0.030876  0.371900  0.061932
```

这里 [`transform()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.transform.html#pandas.DataFrame.transform) 接收了一个单一函数；这等价于一个 [ufunc](https://numpy.org/doc/stable/reference/ufuncs.html) 应用。

```python
In [188]: np.abs(tsdf)
Out[188]: 
                   A         B         C
2000-01-01  0.428759  0.864890  0.675341
2000-01-02  0.168731  1.338144  1.279321
2000-01-03  1.621034  0.438107  0.903794
2000-01-04       NaN       NaN       NaN
2000-01-05       NaN       NaN       NaN
2000-01-06       NaN       NaN       NaN
2000-01-07       NaN       NaN       NaN
2000-01-08  0.254374  1.240447  0.201052
2000-01-09  0.157795  0.791197  1.144209
2000-01-10  0.030876  0.371900  0.061932
```

将单个函数传递给具有 `Series` 的 `.transform()` 将返回单个 `Series`。

```python
In [189]: tsdf["A"].transform(np.abs)
Out[189]: 
2000-01-01    0.428759
2000-01-02    0.168731
2000-01-03    1.621034
2000-01-04         NaN
2000-01-05         NaN
2000-01-06         NaN
2000-01-07         NaN
2000-01-08    0.254374
2000-01-09    0.157795
2000-01-10    0.030876
Freq: D, Name: A, dtype: float64
```

#### 使用多个函数进行转换[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#transform-with-multiple-functions)

传递多个函数将产生一个列 MultiIndexed DataFrame。第一级将是原始框架的列名；第二级将是转换函数的名称。

```python
In [190]: tsdf.transform([np.abs, lambda x: x + 1])
Out[190]: 
                   A                   B                   C        
            absolute  <lambda>  absolute  <lambda>  absolute  <lambda>
2000-01-01  0.428759  0.571241  0.864890  0.135110  0.675341  0.324659
2000-01-02  0.168731  0.831269  1.338144  2.338144  1.279321 -0.279321
2000-01-03  1.621034 -0.621034  0.438107  1.438107  0.903794  1.903794
2000-01-04       NaN       NaN       NaN       NaN       NaN       NaN
2000-01-05       NaN       NaN       NaN       NaN       NaN       NaN
2000-01-06       NaN       NaN       NaN       NaN       NaN       NaN
2000-01-07       NaN       NaN       NaN       NaN       NaN       NaN
2000-01-08  0.254374  1.254374  1.240447 -0.240447  0.201052  0.798948
2000-01-09  0.157795  0.842205  0.791197  1.791197  1.144209 -0.144209
2000-01-10  0.030876  0.969124  0.371900  1.371900  0.061932  1.061932
```

将多个函数传递给 Series 将产生一个 DataFrame。结果列名将是转换函数。

```python
In [191]: tsdf["A"].transform([np.abs, lambda x: x + 1])
Out[191]: 
            absolute  <lambda>
2000-01-01  0.428759  0.571241
2000-01-02  0.168731  0.831269
2000-01-03  1.621034 -0.621034
2000-01-04       NaN       NaN
2000-01-05       NaN       NaN
2000-01-06       NaN       NaN
2000-01-07       NaN       NaN
2000-01-08  0.254374  1.254374
2000-01-09  0.157795  0.842205
2000-01-10  0.030876  0.969124
```

#### 使用字典进行转换[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#transforming-with-a-dict)

传递函数字典将允许按列选择性转换。

```python
In [192]: tsdf.transform({"A": np.abs, "B": lambda x: x + 1})
Out[192]: 
                   A         B
2000-01-01  0.428759  0.135110
2000-01-02  0.168731  2.338144
2000-01-03  1.621034  1.438107
2000-01-04       NaN       NaN
2000-01-05       NaN       NaN
2000-01-06       NaN       NaN
2000-01-07       NaN       NaN
2000-01-08  0.254374 -0.240447
2000-01-09  0.157795  1.791197
2000-01-10  0.030876  1.371900
```

传递列表字典将生成具有这些选择性转换的 MultiIndexed DataFrame。

```python
In [193]: tsdf.transform({"A": np.abs, "B": [lambda x: x + 1, "sqrt"]})
Out[193]: 
                   A         B        
            absolute  <lambda>      sqrt
2000-01-01  0.428759  0.135110       NaN
2000-01-02  0.168731  2.338144  1.156782
2000-01-03  1.621034  1.438107  0.661897
2000-01-04       NaN       NaN       NaN
2000-01-05       NaN       NaN       NaN
2000-01-06       NaN       NaN       NaN
2000-01-07       NaN       NaN       NaN
2000-01-08  0.254374 -0.240447       NaN
2000-01-09  0.157795  1.791197  0.889493
2000-01-10  0.030876  1.371900  0.609836
```

### 应用逐元素函数[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#applying-elementwise-functions)

由于并非所有函数都可以向量化（接受 NumPy 数组并返回另一个数组或值），DataFrame 上的 [`map()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.map.html#pandas.DataFrame.map) 方法和 Series 上类似的 [`map()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.map.html#pandas.Series.map) 方法接受任何接受单个值并返回单个值的 Python 函数。例如：

```python
In [194]: df4 = df.copy()

In [195]: df4
Out[195]: 
        one       two     three
a  1.394981  1.772517       NaN
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435
d       NaN  0.279344 -0.613172

In [196]: def f(x):
   .....:     return len(str(x))
   .....: 

In [197]: df4["one"].map(f)
Out[197]: 
a    18
b    19
c    18
d     3
Name: one, dtype: int64

In [198]: df4.map(f)
Out[198]: 
   one  two  three
a   18   17      3
b   19   18     20
c   18   18     16
d    3   19     19
```

[`Series.map()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.map.html#pandas.Series.map) 还有一个额外的特性；它可以用来轻松地“链接”或“映射”由次要 Series 定义的值。这与[合并/连接功能](https://pandas.pydata.org/docs/user_guide/merging.html#merging)密切相关：

```python
In [199]: s = pd.Series(
   .....:     ["six", "seven", "six", "seven", "six"], index=["a", "b", "c", "d", "e"]
   .....: )

In [200]: t = pd.Series({"six": 6.0, "seven": 7.0})

In [201]: s
Out[201]: 
a      six
b    seven
c      six
d    seven
e      six
dtype: object

In [202]: s.map(t)
Out[202]: 
a    6.0
b    7.0
c    6.0
d    7.0
e    6.0
dtype: float64
```

## 重新索引和更改标签[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#reindexing-and-altering-labels)

[`reindex()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.reindex.html#pandas.Series.reindex) 是 pandas 中基本的数据对齐方法。它用于实现几乎所有其他依赖于标签对齐功能的功能。*重新索引*意味着使数据符合给定的一组标签沿着特定轴。这实现了几个目的：

*   重新排序现有数据以匹配一组新标签。
*   在标签位置插入缺失值（NA）标记，该位置没有该标签的数据。
*   如果指定，使用逻辑**填充**缺失标签的数据（与处理时间序列数据高度相关）。

以下是一个简单的例子：

```python
In [203]: s = pd.Series(np.random.randn(5), index=["a", "b", "c", "d", "e"])

In [204]: s
Out[204]: 
a    1.695148
b    1.328614
c    1.234686
d   -0.385845
e   -1.326508
dtype: float64

In [205]: s.reindex(["e", "b", "f", "d"])
Out[205]: 
e   -1.326508
b    1.328614
f         NaN
d   -0.385845
dtype: float64
```

这里，`f` 标签不包含在 Series 中，因此在结果中显示为 `NaN`。

对于 DataFrame，你可以同时重新索引行和列：

```python
In [206]: df
Out[206]: 
        one       two     three
a  1.394981  1.772517       NaN
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435
d       NaN  0.279344 -0.613172

In [207]: df.reindex(index=["c", "f", "b"], columns=["three", "two", "one"])
Out[207]: 
      three       two       one
c  1.227435  1.478369  0.695246
f       NaN       NaN       NaN
b -0.050390  1.912123  0.343054
```

请注意，包含实际轴标签的 `Index` 对象可以在对象之间**共享**。因此，如果我们有一个 Series 和一个 DataFrame，可以进行以下操作：

```python
In [208]: rs = s.reindex(df.index)

In [209]: rs
Out[209]: 
a    1.695148
b    1.328614
c    1.234686
d   -0.385845
dtype: float64

In [210]: rs.index is df.index
Out[210]: True
```

这意味着重新索引后的 Series 的索引与 DataFrame 的索引是同一个 Python 对象。

[`DataFrame.reindex()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.reindex.html#pandas.DataFrame.reindex) 也支持“轴式”调用约定，你指定单个 `labels` 参数及其应用的 `axis`。

```python
In [211]: df.reindex(["c", "f", "b"], axis="index")
Out[211]: 
        one       two     three
c  0.695246  1.478369  1.227435
f       NaN       NaN       NaN
b  0.343054  1.912123 -0.050390

In [212]: df.reindex(["three", "two", "one"], axis="columns")
Out[212]: 
      three       two       one
a       NaN  1.772517  1.394981
b -0.050390  1.912123  0.343054
c  1.227435  1.478369  0.695246
d -0.613172  0.279344       NaN
```

注意

在编写性能敏感的代码时，花时间成为重新索引高手是有充分理由的：**许多操作在对齐的数据上更快**。添加两个未对齐的 DataFrame 会在内部触发重新索引步骤。在探索性分析中，你几乎不会注意到差异（因为 `reindex` 已经过大量优化），但当 CPU 周期很重要时，在这里和那里撒一些显式的 `reindex` 调用会产生影响。

### 重新索引以与另一个对象对齐[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#reindexing-to-align-with-another-object)

你可能希望获取一个对象并将其轴重新索引为与另一个对象相同的标签。虽然此操作的语法直接但冗长，但这是一个足够常见的操作，因此提供了 [`reindex_like()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.reindex_like.html#pandas.DataFrame.reindex_like) 方法来简化此操作：

```python
In [213]: df2 = df.reindex(["a", "b", "c"], columns=["one", "two"])

In [214]: df3 = df2 - df2.mean()

In [215]: df2
Out[215]: 
        one       two
a  1.394981  1.772517
b  0.343054  1.912123
c  0.695246  1.478369

In [216]: df3
Out[216]: 
        one       two
a  0.583888  0.051514
b -0.468040  0.191120
c -0.115848 -0.242634

In [217]: df.reindex_like(df2)
Out[217]: 
        one       two
a  1.394981  1.772517
b  0.343054  1.912123
c  0.695246  1.478369
```

### 使用 `align` 使对象彼此对齐[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#aligning-objects-with-each-other-with-align)

[`align()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.align.html#pandas.Series.align) 方法是同时对齐两个对象的最快方式。它支持一个 `join` 参数（与[连接和合并](https://pandas.pydata.org/docs/user_guide/merging.html#merging)相关）：

> *   `join='outer'`：取索引的并集（默认值）。
> *   `join='left'`：使用调用对象的索引。
> *   `join='right'`：使用传递对象的索引。
> *   `join='inner'`：取索引的交集。

它返回一个包含两个重新索引后的 Series 的元组：

```python
In [218]: s = pd.Series(np.random.randn(5), index=["a", "b", "c", "d", "e"])

In [219]: s1 = s[:4]

In [220]: s2 = s[1:]

In [221]: s1.align(s2)
Out[221]: 
(a   -0.186646
 b   -1.692424
 c   -0.303893
 d   -1.425662
 e         NaN
 dtype: float64,
 a         NaN
 b   -1.692424
 c   -0.303893
 d   -1.425662
 e    1.114285
 dtype: float64)

In [222]: s1.align(s2, join="inner")
Out[222]: 
(b   -1.692424
 c   -0.303893
 d   -1.425662
 dtype: float64,
 b   -1.692424
 c   -0.303893
 d   -1.425662
 dtype: float64)

In [223]: s1.align(s2, join="left")
Out[223]: 
(a   -0.186646
 b   -1.692424
 c   -0.303893
 d   -1.425662
 dtype: float64,
 a         NaN
 b   -1.692424
 c   -0.303893
 d   -1.425662
 dtype: float64)
```

对于 DataFrames，连接方法默认将应用于索引和列：

```python
In [224]: df.align(df2, join="inner")
Out[224]: 
(        one       two
 a  1.394981  1.772517
 b  0.343054  1.912123
 c  0.695246  1.478369,
         one       two
 a  1.394981  1.772517
 b  0.343054  1.912123
 c  0.695246  1.478369)
```

你也可以传递一个 `axis` 选项来仅对齐指定的轴：

```python
In [225]: df.align(df2, join="inner", axis=0)
Out[225]: 
(        one       two     three
 a  1.394981  1.772517       NaN
 b  0.343054  1.912123 -0.050390
 c  0.695246  1.478369  1.227435,
         one       two
 a  1.394981  1.772517
 b  0.343054  1.912123
 c  0.695246  1.478369)
```

如果你将 Series 传递给 [`DataFrame.align()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.align.html#pandas.DataFrame.align)，你可以使用 `axis` 参数选择在 DataFrame 的索引或列上对齐两个对象：

```python
In [226]: df.align(df2.iloc[0], axis=1)
Out[226]: 
(        one     three       two
 a  1.394981       NaN  1.772517
 b  0.343054 -0.050390  1.912123
 c  0.695246  1.227435  1.478369
 d       NaN -0.613172  0.279344,
 one      1.394981
 three         NaN
 two      1.772517
 Name: a, dtype: float64)
```

### 重新索引时的填充[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#filling-while-reindexing)

[`reindex()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.reindex.html#pandas.Series.reindex) 接受一个可选参数 `method`，它是指从下表中选择的填充方法：

| 方法             | 操作               |
| ---------------- | ------------------ |
| pad / ffill      | 向前填充值         |
| bfill / backfill | 向后填充值         |
| nearest          | 从最近的索引值填充 |

我们在一个简单的 Series 上说明这些填充方法：

```python
In [227]: rng = pd.date_range("1/3/2000", periods=8)

In [228]: ts = pd.Series(np.random.randn(8), index=rng)

In [229]: ts2 = ts.iloc[[0, 3, 6]]

In [230]: ts
Out[230]: 
2000-01-03    0.183051
2000-01-04    0.400528
2000-01-05   -0.015083
2000-01-06    2.395489
2000-01-07    1.414806
2000-01-08    0.118428
2000-01-09    0.733639
2000-01-10   -0.936077
Freq: D, dtype: float64

In [231]: ts2
Out[231]: 
2000-01-03    0.183051
2000-01-06    2.395489
2000-01-09    0.733639
Freq: 3D, dtype: float64

In [232]: ts2.reindex(ts.index)
Out[232]: 
2000-01-03    0.183051
2000-01-04         NaN
2000-01-05         NaN
2000-01-06    2.395489
2000-01-07         NaN
2000-01-08         NaN
2000-01-09    0.733639
2000-01-10         NaN
Freq: D, dtype: float64

In [233]: ts2.reindex(ts.index, method="ffill")
Out[233]: 
2000-01-03    0.183051
2000-01-04    0.183051
2000-01-05    0.183051
2000-01-06    2.395489
2000-01-07    2.395489
2000-01-08    2.395489
2000-01-09    0.733639
2000-01-10    0.733639
Freq: D, dtype: float64

In [234]: ts2.reindex(ts.index, method="bfill")
Out[234]: 
2000-01-03    0.183051
2000-01-04    2.395489
2000-01-05    2.395489
2000-01-06    2.395489
2000-01-07    0.733639
2000-01-08    0.733639
2000-01-09    0.733639
2000-01-10         NaN
Freq: D, dtype: float64

In [235]: ts2.reindex(ts.index, method="nearest")
Out[235]: 
2000-01-03    0.183051
2000-01-04    0.183051
2000-01-05    2.395489
2000-01-06    2.395489
2000-01-07    2.395489
2000-01-08    0.733639
2000-01-09    0.733639
2000-01-10    0.733639
Freq: D, dtype: float64
```

这些方法要求索引**有序**递增或递减。

请注意，除了 `method='nearest'` 外，也可以使用 [ffill](https://pandas.pydata.org/docs/user_guide/missing_data.html#missing-data-fillna) 或 [interpolate](https://pandas.pydata.org/docs/user_guide/missing_data.html#missing-data-interpolate) 实现相同的结果：

```python
In [236]: ts2.reindex(ts.index).ffill()
Out[236]: 
2000-01-03    0.183051
2000-01-04    0.183051
2000-01-05    0.183051
2000-01-06    2.395489
2000-01-07    2.395489
2000-01-08    2.395489
2000-01-09    0.733639
2000-01-10    0.733639
Freq: D, dtype: float64
```

如果索引不是单调递增或递减，[`reindex()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.reindex.html#pandas.Series.reindex) 将引发 ValueError。[`fillna()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.fillna.html#pandas.Series.fillna) 和 [`interpolate()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.interpolate.html#pandas.Series.interpolate) 不会对索引的顺序执行任何检查。

### 重新索引时填充的限制[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#limits-on-filling-while-reindexing)

`limit` 和 `tolerance` 参数提供了对重新索引时填充的额外控制。Limit 指定连续匹配的最大计数：

```python
In [237]: ts2.reindex(ts.index, method="ffill", limit=1)
Out[237]: 
2000-01-03    0.183051
2000-01-04    0.183051
2000-01-05         NaN
2000-01-06    2.395489
2000-01-07    2.395489
2000-01-08         NaN
2000-01-09    0.733639
2000-01-10    0.733639
Freq: D, dtype: float64
```

相比之下，tolerance 指定索引与索引器值之间的最大距离：

```python
In [238]: ts2.reindex(ts.index, method="ffill", tolerance="1 day")
Out[238]: 
2000-01-03    0.183051
2000-01-04    0.183051
2000-01-05         NaN
2000-01-06    2.395489
2000-01-07    2.395489
2000-01-08         NaN
2000-01-09    0.733639
2000-01-10    0.733639
Freq: D, dtype: float64
```

请注意，当在 `DatetimeIndex`、`TimedeltaIndex` 或 `PeriodIndex` 上使用时，如果可能，`tolerance` 将被强制转换为 `Timedelta`。这允许你用适当的字符串指定容差。

### 从轴中删除标签[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#dropping-labels-from-an-axis)

与 `reindex` 密切相关的方法是 [`drop()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.drop.html#pandas.DataFrame.drop) 函数。它从轴中移除一组标签：

```python
In [239]: df
Out[239]: 
        one       two     three
a  1.394981  1.772517       NaN
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435
d       NaN  0.279344 -0.613172

In [240]: df.drop(["a", "d"], axis=0)
Out[240]: 
        one       two     three
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435

In [241]: df.drop(["one"], axis=1)
Out[241]: 
        two     three
a  1.772517       NaN
b  1.912123 -0.050390
c  1.478369  1.227435
d  0.279344 -0.613172
```

请注意，以下操作也有效，但不太明显/简洁：

```python
In [242]: df.reindex(df.index.difference(["a", "d"]))
Out[242]: 
        one       two     three
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435
```

### 重命名 / 映射标签[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#renaming-mapping-labels)

[`rename()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rename.html#pandas.DataFrame.rename) 方法允许你基于某个映射（字典或 Series）或任意函数来重新标记轴。

```python
In [243]: s
Out[243]: 
a   -0.186646
b   -1.692424
c   -0.303893
d   -1.425662
e    1.114285
dtype: float64

In [244]: s.rename(str.upper)
Out[244]: 
A   -0.186646
B   -1.692424
C   -0.303893
D   -1.425662
E    1.114285
dtype: float64
```

如果你传递一个函数，当使用任何标签调用时，它必须返回一个值（并且必须产生一组唯一值）。也可以使用字典或 Series：

```python
In [245]: df.rename(
   .....:     columns={"one": "foo", "two": "bar"},
   .....:     index={"a": "apple", "b": "banana", "d": "durian"},
   .....: )
   .....: 
Out[245]: 
             foo       bar     three
apple   1.394981  1.772517       NaN
banana  0.343054  1.912123 -0.050390
c       0.695246  1.478369  1.227435
durian       NaN  0.279344 -0.613172
```

如果映射中不包含列/索引标签，则不会重命名。请注意，映射中的额外标签不会引发错误。

[`DataFrame.rename()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rename.html#pandas.DataFrame.rename) 还支持“轴式”调用约定，你指定单个 `mapper` 和该映射应用的 `axis`。

```python
In [246]: df.rename({"one": "foo", "two": "bar"}, axis="columns")
Out[246]: 
        foo       bar     three
a  1.394981  1.772517       NaN
b  0.343054  1.912123 -0.050390
c  0.695246  1.478369  1.227435
d       NaN  0.279344 -0.613172

In [247]: df.rename({"a": "apple", "b": "banana", "d": "durian"}, axis="index")
Out[247]: 
             one       two     three
apple   1.394981  1.772517       NaN
banana  0.343054  1.912123 -0.050390
c       0.695246  1.478369  1.227435
durian       NaN  0.279344 -0.613172
```

最后，[`rename()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.rename.html#pandas.Series.rename) 也接受标量或类列表用于更改 `Series.name` 属性。

```python
In [248]: s.rename("scalar-name")
Out[248]: 
a   -0.186646
b   -1.692424
c   -0.303893
d   -1.425662
e    1.114285
Name: scalar-name, dtype: float64
```

[`DataFrame.rename_axis()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rename_axis.html#pandas.DataFrame.rename_axis) 和 [`Series.rename_axis()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.rename_axis.html#pandas.Series.rename_axis) 方法允许更改 `MultiIndex` 的特定名称（与标签相对）。

```python
In [249]: df = pd.DataFrame(
   .....:     {"x": [1, 2, 3, 4, 5, 6], "y": [10, 20, 30, 40, 50, 60]},
   .....:     index=pd.MultiIndex.from_product(
   .....:         [["a", "b", "c"], [1, 2]], names=["let", "num"]
   .....:     ),
   .....: )

In [250]: df
Out[250]: 
         x   y
let num     
a   1    1  10
    2    2  20
b   1    3  30
    2    4  40
c   1    5  50
    2    6  60

In [251]: df.rename_axis(index={"let": "abc"})
Out[251]: 
         x   y
abc num     
a   1    1  10
    2    2  20
b   1    3  30
    2    4  40
c   1    5  50
    2    6  60

In [252]: df.rename_axis(index=str.upper)
Out[252]: 
         x   y
LET NUM     
a   1    1  10
    2    2  20
b   1    3  30
    2    4  40
c   1    5  50
    2    6  60
```

## 迭代[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#iteration)

在 pandas 对象上进行基本迭代的行为取决于类型。在 Series 上迭代时，它被视为类数组，基本迭代产生值。DataFrames 遵循字典类的约定，迭代对象的“键”。

简而言之，基本迭代（`for i in object`）产生：

*   **Series**：值。
*   **DataFrame**：列标签。

因此，例如，迭代 DataFrame 会得到列名：

```python
In [253]: df = pd.DataFrame(
   .....:     {"col1": np.random.randn(3), "col2": np.random.randn(3)}, index=["a", "b", "c"]
   .....: )

In [254]: for col in df:
   .....:     print(col)
   .....: 
col1
col2
```

pandas 对象还具有字典类的 [`items()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.items.html#pandas.DataFrame.items) 方法，用于迭代（键，值）对。

要迭代 DataFrame 的行，可以使用以下方法：

*   [`iterrows()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html#pandas.DataFrame.iterrows)：将 DataFrame 的行迭代为（索引，Series）对。这将行转换为 Series 对象，可能会改变 dtype 并有一些性能影响。
*   [`itertuples()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.itertuples.html#pandas.DataFrame.itertuples)：将 DataFrame 的行迭代为值的命名元组。这比 [`iterrows()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html#pandas.DataFrame.iterrows) 快很多，并且在大多数情况下，对于迭代 DataFrame 的值，建议使用它。

警告

迭代 pandas 对象通常**很慢**。在许多情况下，手动迭代行是不需要的，可以通过以下方法之一避免：

*   寻找*向量化*解决方案：许多操作可以使用内置方法或 NumPy 函数、（布尔）索引等来执行。
*   当你有一个无法一次处理整个 DataFrame/Series 的函数时，最好使用 [`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply) 而不是迭代值。有关详细信息，请参阅[函数应用](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#basics-apply)文档。
*   如果你需要对值进行迭代操作，但性能很重要，请考虑使用 cython 或 numba 编写内部循环。有关此方法的示例，请参见[提升性能](https://pandas.pydata.org/docs/user_guide/enhancingperf.html#enhancingperf)部分。

警告

你永远不应该修改正在迭代的内容。这不能保证在所有情况下都有效。根据数据类型，迭代器返回一个副本而不是视图，写入它不会有任何效果！

例如，在以下情况下设置值无效：

```python
In [255]: df = pd.DataFrame({"a": [1, 2, 3], "b": ["a", "b", "c"]})

In [256]: for index, row in df.iterrows():
   .....:     row["a"] = 10
   .....: 

In [257]: df
Out[257]: 
   a  b
0  1  a
1  2  b
2  3  c
```

### items[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#items)

与字典类接口一致，[`items()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.items.html#pandas.DataFrame.items) 迭代键值对：

*   **Series**：（索引，标量值）对。
*   **DataFrame**：（列，Series）对。

例如：

```python
In [258]: for label, ser in df.items():
   .....:     print(label)
   .....:     print(ser)
   .....: 
a
0    1
1    2
2    3
Name: a, dtype: int64
b
0    a
1    b
2    c
Name: b, dtype: object
```

### iterrows[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#iterrows)

[`iterrows()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html#pandas.DataFrame.iterrows) 允许你将 DataFrame 的行迭代为 Series 对象。它返回一个迭代器，生成每个索引值以及包含每行数据的 Series：

```python
In [259]: for row_index, row in df.iterrows():
   .....:     print(row_index, row, sep="\n")
   .....: 
0
a    1
b    a
Name: 0, dtype: object
1
a    2
b    b
Name: 1, dtype: object
2
a    3
b    c
Name: 2, dtype: object
```

注意

因为 [`iterrows()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html#pandas.DataFrame.iterrows) 为每一行返回一个 Series，所以它**不**会跨行保留 dtypes（对于 DataFrames，dtypes 在列之间保留）。例如，

```python
In [260]: df_orig = pd.DataFrame([[1, 1.5]], columns=["int", "float"])

In [261]: df_orig.dtypes
Out[261]: 
int        int64
float    float64
dtype: object

In [262]: row = next(df_orig.iterrows())[1]

In [263]: row
Out[263]: 
int      1.0
float    1.5
Name: 0, dtype: float64
```

`row` 中作为 Series 返回的所有值现在都被上转型为浮点数，包括列 `x` 中的原始整数值：

```python
In [264]: row["int"].dtype
Out[264]: dtype('float64')

In [265]: df_orig["int"].dtype
Out[265]: dtype('int64')
```

为了在迭代行时保留 dtypes，最好使用 [`itertuples()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.itertuples.html#pandas.DataFrame.itertuples)，它返回值的命名元组，通常比 [`iterrows()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html#pandas.DataFrame.iterrows) 快得多。

例如，转置 DataFrame 的一种人为方式是：

```python
In [266]: df2 = pd.DataFrame({"x": [1, 2, 3], "y": [4, 5, 6]})

In [267]: print(df2)
   x  y
0  1  4
1  2  5
2  3  6

In [268]: print(df2.T)
   0  1  2
x  1  2  3
y  4  5  6

In [269]: df2_t = pd.DataFrame({idx: values for idx, values in df2.iterrows()})

In [270]: print(df2_t)
   0  1  2
x  1  2  3
y  4  5  6
```

### itertuples[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#itertuples)

[`itertuples()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.itertuples.html#pandas.DataFrame.itertuples) 方法将返回一个迭代器，为 DataFrame 中的每一行生成一个命名元组。元组的第一个元素是行的对应索引值，而其余值是行值。

例如：

```python
In [271]: for row in df.itertuples():
   .....:     print(row)
   .....: 
Pandas(Index=0, a=1, b='a')
Pandas(Index=1, a=2, b='b')
Pandas(Index=2, a=3, b='c')
```

此方法不会将行转换为 Series 对象；它只是返回命名元组内的值。因此，[`itertuples()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.itertuples.html#pandas.DataFrame.itertuples) 保留了值的 dtype，并且通常比 [`iterrows()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.iterrows.html#pandas.DataFrame.iterrows) 更快。

注意

如果列名是无效的 Python 标识符、重复或以以下划线开头，它们将被重命名为位置名称。当列数很大（>255）时，将返回常规元组。

## .dt 访问器[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#dt-accessor)

`Series` 有一个访问器，可以简洁地返回 Series *值*的类似日期时间的属性，如果它是一个日期时间/周期类 Series。这将返回一个 Series，其索引与现有 Series 相同。

```python
# datetime
In [272]: s = pd.Series(pd.date_range("20130101 09:10:12", periods=4))

In [273]: s
Out[273]: 
0   2013-01-01 09:10:12
1   2013-01-02 09:10:12
2   2013-01-03 09:10:12
3   2013-01-04 09:10:12
dtype: datetime64[ns]

In [274]: s.dt.hour
Out[274]: 
0    9
1    9
2    9
3    9
dtype: int32

In [275]: s.dt.second
Out[275]: 
0    12
1    12
2    12
3    12
dtype: int32

In [276]: s.dt.day
Out[276]: 
0    1
1    2
2    3
3    4
dtype: int32
```

这可以实现如下很好的表达式：

```python
In [277]: s[s.dt.day == 2]
Out[277]: 
1   2013-01-02 09:10:12
dtype: datetime64[ns]
```

你可以轻松生成带时区的转换：

```python
In [278]: stz = s.dt.tz_localize("US/Eastern")

In [279]: stz
Out[279]: 
0   2013-01-01 09:10:12-05:00
1   2013-01-02 09:10:12-05:00
2   2013-01-03 09:10:12-05:00
3   2013-01-04 09:10:12-05:00
dtype: datetime64[ns, US/Eastern]

In [280]: stz.dt.tz
Out[280]: <DstTzInfo 'US/Eastern' LMT-1 day, 19:04:00 STD>
```

你也可以链接这些类型的操作：

```python
In [281]: s.dt.tz_localize("UTC").dt.tz_convert("US/Eastern")
Out[281]: 
0   2013-01-01 04:10:12-05:00
1   2013-01-02 04:10:12-05:00
2   2013-01-03 04:10:12-05:00
3   2013-01-04 04:10:12-05:00
dtype: datetime64[ns, US/Eastern]
```

你还可以使用 [`Series.dt.strftime()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.dt.strftime.html#pandas.Series.dt.strftime) 将日期时间值格式化为字符串，它支持与标准 [`strftime()`](https://docs.python.org/3/library/datetime.html#datetime.datetime.strftime) 相同的格式。

```python
# DatetimeIndex
In [282]: s = pd.Series(pd.date_range("20130101", periods=4))

In [283]: s
Out[283]: 
0   2013-01-01
1   2013-01-02
2   2013-01-03
3   2013-01-04
dtype: datetime64[ns]

In [284]: s.dt.strftime("%Y/%m/%d")
Out[284]: 
0    2013/01/01
1    2013/01/02
2    2013/01/03
3    2013/01/04
dtype: object
# PeriodIndex
In [285]: s = pd.Series(pd.period_range("20130101", periods=4))

In [286]: s
Out[286]: 
0    2013-01-01
1    2013-01-02
2    2013-01-03
3    2013-01-04
dtype: period[D]

In [287]: s.dt.strftime("%Y/%m/%d")
Out[287]: 
0    2013/01/01
1    2013/01/02
2    2013/01/03
3    2013/01/04
dtype: object
```

`.dt` 访问器也适用于周期和时长 dtypes。

```python
# period
In [288]: s = pd.Series(pd.period_range("20130101", periods=4, freq="D"))

In [289]: s
Out[289]: 
0    2013-01-01
1    2013-01-02
2    2013-01-03
3    2013-01-04
dtype: period[D]

In [290]: s.dt.year
Out[290]: 
0    2013
1    2013
2    2013
3    2013
dtype: int64

In [291]: s.dt.day
Out[291]: 
0    1
1    2
2    3
3    4
dtype: int64
# timedelta
In [292]: s = pd.Series(pd.timedelta_range("1 day 00:00:05", periods=4, freq="s"))

In [293]: s
Out[293]: 
0   1 days 00:00:05
1   1 days 00:00:06
2   1 days 00:00:07
3   1 days 00:00:08
dtype: timedelta64[ns]

In [294]: s.dt.days
Out[294]: 
0    1
1    1
2    1
3    1
dtype: int64

In [295]: s.dt.seconds
Out[295]: 
0    5
1    6
2    7
3    8
dtype: int32

In [296]: s.dt.components
Out[296]: 
   days  hours  minutes  seconds  milliseconds  microseconds  nanoseconds
0     1      0        0        5             0             0            0
1     1      0        0        6             0             0            0
2     1      0        0        7             0             0            0
3     1      0        0        8             0             0            0
```

注意

如果你使用非日期时间类值访问 `Series.dt`，将引发 `TypeError`。

## 向量化字符串方法[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#vectorized-string-methods)

Series 配备了一组字符串处理方法，可以轻松地对数组的每个元素进行操作。也许最重要的是，这些方法会自动排除缺失值/NA 值。这些通过 Series 的 `str` 属性访问，并且通常具有匹配等效（标量）内置字符串方法的名称。例如：

> ```python
> In [297]: s = pd.Series(
> .....:     ["A", "B", "C", "Aaba", "Baca", np.nan, "CABA", "dog", "cat"], dtype="string"
> .....: )
> .....: 
> 
> In [298]: s.str.lower()
> Out[298]: 
> 0       a
> 1       b
> 2       c
> 3    aaba
> 4    baca
> 5    <NA>
> 6    caba
> 7     dog
> 8     cat
> dtype: string
> ```

还提供了强大的模式匹配方法，但请注意，模式匹配通常默认使用[正则表达式](https://docs.python.org/3/library/re.html)（在某些情况下始终使用它们）。

注意

在 pandas 1.0 之前，字符串方法仅适用于 `object` 类型的 `Series`。pandas 1.0 添加了专门用于字符串的 [`StringDtype`](https://pandas.pydata.org/docs/reference/api/pandas.StringDtype.html#pandas.StringDtype)。更多信息请参见[文本数据类型](https://pandas.pydata.org/docs/user_guide/text.html#text-types)。

有关完整描述，请参见[向量化字符串方法](https://pandas.pydata.org/docs/user_guide/text.html#text-string-methods)。

## 排序[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#sorting)

pandas 支持三种排序：按索引标签排序、按列值排序以及按两者的组合排序。

### 按索引排序[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#by-index)

[`Series.sort_index()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.sort_index.html#pandas.Series.sort_index) 和 [`DataFrame.sort_index()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_index.html#pandas.DataFrame.sort_index) 方法用于按索引级别对 pandas 对象进行排序。

```python
In [299]: df = pd.DataFrame(
   .....:     {
   .....:         "one": pd.Series(np.random.randn(3), index=["a", "b", "c"]),
   .....:         "two": pd.Series(np.random.randn(4), index=["a", "b", "c", "d"]),
   .....:         "three": pd.Series(np.random.randn(3), index=["b", "c", "d"]),
   .....:     }
   .....: )

In [300]: unsorted_df = df.reindex(
   .....:     index=["a", "d", "c", "b"], columns=["three", "two", "one"]
   .....: )

In [301]: unsorted_df
Out[301]: 
      three       two       one
a       NaN -1.152244  0.562973
d -0.252916 -0.109597       NaN
c  1.273388 -0.167123  0.640382
b -0.098217  0.009797 -1.299504

# DataFrame
In [302]: unsorted_df.sort_index()
Out[302]: 
      three       two       one
a       NaN -1.152244  0.562973
b -0.098217  0.009797 -1.299504
c  1.273388 -0.167123  0.640382
d -0.252916 -0.109597       NaN

In [303]: unsorted_df.sort_index(ascending=False)
Out[303]: 
      three       two       one
d -0.252916 -0.109597       NaN
c  1.273388 -0.167123  0.640382
b -0.098217  0.009797 -1.299504
a       NaN -1.152244  0.562973

In [304]: unsorted_df.sort_index(axis=1)
Out[304]: 
        one     three       two
a  0.562973       NaN -1.152244
d       NaN -0.252916 -0.109597
c  0.640382  1.273388 -0.167123
b -1.299504 -0.098217  0.009797

# Series
In [305]: unsorted_df["three"].sort_index()
Out[305]: 
a         NaN
b   -0.098217
c    1.273388
d   -0.252916
Name: three, dtype: float64
```

按索引排序还支持一个 `key` 参数，该参数接受一个可调用函数，应用于正在排序的索引。对于 `MultiIndex` 对象，键应用于由 `level` 指定的各级别。

```python
In [306]: s1 = pd.DataFrame({"a": ["B", "a", "C"], "b": [1, 2, 3], "c": [2, 3, 4]}).set_index(
   .....:     list("ab")
   .....: )
   .....: 

In [307]: s1
Out[307]: 
     c
a b 
B 1  2
a 2  3
C 3  4
In [308]: s1.sort_index(level="a")
Out[308]: 
     c
a b 
B 1  2
C 3  4
a 2  3

In [309]: s1.sort_index(level="a", key=lambda idx: idx.str.lower())
Out[309]: 
     c
a b 
a 2  3
B 1  2
C 3  4
```

有关按值进行键排序的信息，请参见[值排序](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#basics-sort-value-key)。

### 按值排序[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#by-values)

[`Series.sort_values()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.sort_values.html#pandas.Series.sort_values) 方法用于按 `Series` 的值排序。[`DataFrame.sort_values()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html#pandas.DataFrame.sort_values) 方法用于按 `DataFrame` 的列或行值排序。可选的 `by` 参数传递给 [`DataFrame.sort_values()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html#pandas.DataFrame.sort_values) 可用于指定一个或多个列来确定排序顺序。

```python
In [310]: df1 = pd.DataFrame(
   .....:     {"one": [2, 1, 1, 1], "two": [1, 3, 2, 4], "three": [5, 4, 3, 2]}
   .....: )

In [311]: df1.sort_values(by="two")
Out[311]: 
   one  two  three
0    2    1      5
2    1    2      3
1    1    3      4
3    1    4      2
```

`by` 参数可以接受列名列表，例如：

```python
In [312]: df1[["one", "two", "three"]].sort_values(by=["one", "two"])
Out[312]: 
   one  two  three
2    1    2      3
1    1    3      4
3    1    4      2
0    2    1      5
```

这些方法通过 `na_position` 参数对 NA 值有特殊处理：

```python
In [313]: s[2] = np.nan

In [314]: s.sort_values()
Out[314]: 
0       A
3    Aaba
1       B
4    Baca
6    CABA
8     cat
7     dog
2    <NA>
5    <NA>
dtype: string

In [315]: s.sort_values(na_position="first")
Out[315]: 
2    <NA>
5    <NA>
0       A
3    Aaba
1       B
4    Baca
6    CABA
8     cat
7     dog
dtype: string
```

排序还支持一个 `key` 参数，该参数接受一个可调用函数，应用于正在排序的值。

```python
In [316]: s1 = pd.Series(["B", "a", "C"])
In [317]: s1.sort_values()
Out[317]: 
0    B
2    C
1    a
dtype: object

In [318]: s1.sort_values(key=lambda x: x.str.lower())
Out[318]: 
1    a
0    B
2    C
dtype: object
```

`key` 将被赋予值的 [`Series`](https://pandas.pydata.org/docs/reference/api/pandas.Series.html#pandas.Series)，并且应该返回一个具有相同形状的 `Series` 或转换值数组。对于 `DataFrame` 对象，键按列应用，因此键仍应期望一个 Series 并返回一个 Series，例如：

```python
In [319]: df = pd.DataFrame({"a": ["B", "a", "C"], "b": [1, 2, 3]})
In [320]: df.sort_values(by="a")
Out[320]: 
   a  b
0  B  1
2  C  3
1  a  2

In [321]: df.sort_values(by="a", key=lambda col: col.str.lower())
Out[321]: 
   a  b
1  a  2
0  B  1
2  C  3
```

每个列的名称或类型可用于将不同的函数应用于不同的列。

### 按索引和值排序[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#by-indexes-and-values)

传递给 [`DataFrame.sort_values()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sort_values.html#pandas.DataFrame.sort_values) 的 `by` 参数的字符串可以引用列名或索引级别名称。

```python
# 构建 MultiIndex
In [322]: idx = pd.MultiIndex.from_tuples(
   .....:     [("a", 1), ("a", 2), ("a", 2), ("b", 2), ("b", 1), ("b", 1)]
   .....: )
   .....: 

In [323]: idx.names = ["first", "second"]

# 构建 DataFrame
In [324]: df_multi = pd.DataFrame({"A": np.arange(6, 0, -1)}, index=idx)

In [325]: df_multi
Out[325]: 
              A
first second 
a     1       6
      2       5
      2       4
b     2       3
      1       2
      1       1
```

按 'second'（索引）和 'A'（列）排序：

```python
In [326]: df_multi.sort_values(by=["second", "A"])
Out[326]: 
              A
first second 
b     1       1
      1       2
a     1       6
b     2       3
a     2       4
      2       5
```

注意

如果一个字符串同时匹配列名和索引级别名，则会发出警告，并且列优先。这将在未来版本中导致歧义错误。

### searchsorted[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#searchsorted)

Series 具有 [`searchsorted()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.searchsorted.html#pandas.Series.searchsorted) 方法，其工作方式类似于 [`numpy.ndarray.searchsorted()`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.searchsorted.html#numpy.ndarray.searchsorted)。

```python
In [327]: ser = pd.Series([1, 2, 3])

In [328]: ser.searchsorted([0, 3])
Out[328]: array([0, 2])

In [329]: ser.searchsorted([0, 4])
Out[329]: array([0, 3])

In [330]: ser.searchsorted([1, 3], side="right")
Out[330]: array([1, 3])

In [331]: ser.searchsorted([1, 3], side="left")
Out[331]: array([0, 2])

In [332]: ser = pd.Series([3, 1, 2])

In [333]: ser.searchsorted([0, 3], sorter=np.argsort(ser))
Out[333]: array([0, 2])
```

### 最小 / 最大值[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#smallest-largest-values)

`Series` 具有 [`nsmallest()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.nsmallest.html#pandas.Series.nsmallest) 和 [`nlargest()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.nlargest.html#pandas.Series.nlargest) 方法，它们返回最小或最大的值。对于大型 `Series`，这比排序整个 Series 并在结果上调用 `head(n)` 要快得多。

```python
In [334]: s = pd.Series(np.random.permutation(10))

In [335]: s
Out[335]: 
0    2
1    0
2    3
3    7
4    1
5    5
6    9
7    6
8    8
9    4
dtype: int64

In [336]: s.sort_values()
Out[336]: 
1    0
4    1
0    2
2    3
9    4
5    5
7    6
3    7
8    8
6    9
dtype: int64

In [337]: s.nsmallest(3)
Out[337]: 
1    0
4    1
0    2
dtype: int64

In [338]: s.nlargest(3)
Out[338]: 
6    9
8    8
3    7
dtype: int64
```

`DataFrame` 也有 `nlargest` 和 `nsmallest` 方法。

```python
In [339]: df = pd.DataFrame(
   .....:     {
   .....:         "a": [-2, -1, 1, 10, 8, 11, -1],
   .....:         "b": list("abdceff"),
   .....:         "c": [1.0, 2.0, 4.0, 3.2, np.nan, 3.0, 4.0],
   .....:     }
   .....: )
   .....: 

In [340]: df.nlargest(3, "a")
Out[340]: 
    a  b    c
5  11  f  3.0
3  10  c  3.2
4   8  e  NaN

In [341]: df.nlargest(5, ["a", "c"])
Out[341]: 
    a  b    c
5  11  f  3.0
3  10  c  3.2
4   8  e  NaN
2   1  d  4.0
6  -1  f  4.0

In [342]: df.nsmallest(3, "a")
Out[342]: 
   a  b    c
0 -2  a  1.0
1 -1  b  2.0
6 -1  f  4.0

In [343]: df.nsmallest(5, ["a", "c"])
Out[343]: 
   a  b    c
0 -2  a  1.0
1 -1  b  2.0
6 -1  f  4.0
2  1  d  4.0
4  8  e  NaN
```

### 按 MultiIndex 列排序[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#sorting-by-a-multiindex-column)

当列是 MultiIndex 时，你必须明确指定排序，并在 `by` 中完全指定所有级别。

```python
In [344]: df1.columns = pd.MultiIndex.from_tuples(
   .....:     [("a", "one"), ("a", "two"), ("b", "three")]
   .....: )
   .....: 

In [345]: df1.sort_values(by=("a", "two"))
Out[345]: 
    a         b
  one two three
0   2   1     5
2   1   2     3
1   1   3     4
3   1   4     2
```

## 复制[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#copying)

pandas 对象上的 [`copy()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.copy.html#pandas.DataFrame.copy) 方法复制底层数据（尽管不复制轴索引，因为它们是不可变的）并返回一个新对象。注意，**很少需要复制对象**。例如，只有少数几种方式可以*就地*修改 DataFrame：

*   插入、删除或修改列。
*   赋值给 `index` 或 `columns` 属性。
*   对于同构数据，通过 `values` 属性或高级索引直接修改值。

需要明确的是，没有 pandas 方法具有修改数据的副作用；几乎所有方法都返回一个新对象，原始对象保持不变。如果数据被修改，那是因为你明确地这样做了。

## dtypes[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#dtypes)

在大多数情况下，pandas 使用 NumPy 数组和 dtype 作为 Series 或 DataFrame 的单个列。NumPy 支持 `float`、`int`、`bool`、`timedelta64[ns]` 和 `datetime64[ns]`（注意 NumPy 不支持带时区的日期时间）。

pandas 和第三方库在几个地方*扩展*了 NumPy 的类型系统。本节描述了 pandas 内部所做的扩展。有关如何编写与 pandas 一起使用的扩展，请参见[扩展类型](https://pandas.pydata.org/docs/development/extending.html#extending-extension-types)。有关实现了扩展的第三方库列表，请参见[生态系统页面](https://pandas.pydata.org/community/ecosystem.html)。

下表列出了所有 pandas 扩展类型。对于需要 `dtype` 参数的方法，可以按照指示指定字符串。有关每种类型的更多信息，请参见相应的文档部分。

| 数据类型种类                                                 | 数据类型                                                     | 标量                                                         | 数组                                                         | 字符串别名                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [带时区日期时间](https://pandas.pydata.org/docs/user_guide/timeseries.html#timeseries-timezone) | [`DatetimeTZDtype`](https://pandas.pydata.org/docs/reference/api/pandas.DatetimeTZDtype.html#pandas.DatetimeTZDtype) | [`Timestamp`](https://pandas.pydata.org/docs/reference/api/pandas.Timestamp.html#pandas.Timestamp) | [`arrays.DatetimeArray`](https://pandas.pydata.org/docs/reference/api/pandas.arrays.DatetimeArray.html#pandas.arrays.DatetimeArray) | `'datetime64[ns, <tz>]'`                                     |
| [分类型](https://pandas.pydata.org/docs/user_guide/categorical.html#categorical) | [`CategoricalDtype`](https://pandas.pydata.org/docs/reference/api/pandas.CategoricalDtype.html#pandas.CategoricalDtype) | (无)                                                         | [`Categorical`](https://pandas.pydata.org/docs/reference/api/pandas.Categorical.html#pandas.Categorical) | `'category'`                                                 |
| [周期（时间跨度）](https://pandas.pydata.org/docs/user_guide/timeseries.html#timeseries-periods) | [`PeriodDtype`](https://pandas.pydata.org/docs/reference/api/pandas.PeriodDtype.html#pandas.PeriodDtype) | [`Period`](https://pandas.pydata.org/docs/reference/api/pandas.Period.html#pandas.Period) | [`arrays.PeriodArray`](https://pandas.pydata.org/docs/reference/api/pandas.arrays.PeriodArray.html#pandas.arrays.PeriodArray) `'Period[<freq>]'` | `'period[<freq>]'`,                                          |
| [稀疏类型](https://pandas.pydata.org/docs/user_guide/sparse.html#sparse) | [`SparseDtype`](https://pandas.pydata.org/docs/reference/api/pandas.SparseDtype.html#pandas.SparseDtype) | (无)                                                         | [`arrays.SparseArray`](https://pandas.pydata.org/docs/reference/api/pandas.arrays.SparseArray.html#pandas.arrays.SparseArray) | `'Sparse'`, `'Sparse[int]'`, `'Sparse[float]'`               |
| [区间类型](https://pandas.pydata.org/docs/user_guide/advanced.html#advanced-intervalindex) | [`IntervalDtype`](https://pandas.pydata.org/docs/reference/api/pandas.IntervalDtype.html#pandas.IntervalDtype) | [`Interval`](https://pandas.pydata.org/docs/reference/api/pandas.Interval.html#pandas.Interval) | [`arrays.IntervalArray`](https://pandas.pydata.org/docs/reference/api/pandas.arrays.IntervalArray.html#pandas.arrays.IntervalArray) | `'interval'`, `'Interval'`, `'Interval[<numpy_dtype>]'`, `'Interval[datetime64[ns, <tz>]]'`, `'Interval[timedelta64[<freq>]]'` |
| [可空整数](https://pandas.pydata.org/docs/user_guide/integer_na.html#integer-na) | [`Int64Dtype`](https://pandas.pydata.org/docs/reference/api/pandas.Int64Dtype.html#pandas.Int64Dtype) 等 | (无)                                                         | [`arrays.IntegerArray`](https://pandas.pydata.org/docs/reference/api/pandas.arrays.IntegerArray.html#pandas.arrays.IntegerArray) | `'Int8'`, `'Int16'`, `'Int32'`, `'Int64'`, `'UInt8'`, `'UInt16'`, `'UInt32'`, `'UInt64'` |
| [可空浮点数](https://pandas.pydata.org/docs/reference/arrays.html#api-arrays-float-na) | [`Float64Dtype`](https://pandas.pydata.org/docs/reference/api/pandas.Float64Dtype.html#pandas.Float64Dtype) 等 | (无)                                                         | [`arrays.FloatingArray`](https://pandas.pydata.org/docs/reference/api/pandas.arrays.FloatingArray.html#pandas.arrays.FloatingArray) | `'Float32'`, `'Float64'`                                     |
| [字符串](https://pandas.pydata.org/docs/user_guide/text.html#text) | [`StringDtype`](https://pandas.pydata.org/docs/reference/api/pandas.StringDtype.html#pandas.StringDtype) | [`str`](https://docs.python.org/3/library/stdtypes.html#str) | [`arrays.StringArray`](https://pandas.pydata.org/docs/reference/api/pandas.arrays.StringArray.html#pandas.arrays.StringArray) | `'string'`                                                   |
| [布尔值（带 NA）](https://pandas.pydata.org/docs/reference/arrays.html#api-arrays-bool) | [`BooleanDtype`](https://pandas.pydata.org/docs/reference/api/pandas.BooleanDtype.html#pandas.BooleanDtype) | [`bool`](https://docs.python.org/3/library/functions.html#bool) | [`arrays.BooleanArray`](https://pandas.pydata.org/docs/reference/api/pandas.arrays.BooleanArray.html#pandas.arrays.BooleanArray) | `'boolean'`                                                  |

pandas 有两种存储字符串的方式。

1.  `object` 类型，可以容纳任何 Python 对象，包括字符串。
2.  [`StringDtype`](https://pandas.pydata.org/docs/reference/api/pandas.StringDtype.html#pandas.StringDtype)，专门用于字符串。

通常，我们建议使用 [`StringDtype`](https://pandas.pydata.org/docs/reference/api/pandas.StringDtype.html#pandas.StringDtype)。更多信息请参见[文本数据类型](https://pandas.pydata.org/docs/user_guide/text.html#text-types)。

最后，任意对象可以使用 `object` 类型存储，但应尽可能避免（出于性能和与其他库及方法的互操作性考虑）。请参见[对象转换](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#basics-object-conversion)。

DataFrame 有一个方便的 [`dtypes`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.dtypes.html#pandas.DataFrame.dtypes) 属性，返回一个包含每列数据类型的 Series。

```python
In [346]: dft = pd.DataFrame(
   .....:     {
   .....:         "A": np.random.rand(3),
   .....:         "B": 1,
   .....:         "C": "foo",
   .....:         "D": pd.Timestamp("20010102"),
   .....:         "E": pd.Series([1.0] * 3).astype("float32"),
   .....:         "F": False,
   .....:         "G": pd.Series([1] * 3, dtype="int8"),
   .....:     }
   .....: )
   .....: 

In [347]: dft
Out[347]: 
          A  B    C          D    E      F  G
0  0.035962  1  foo 2001-01-02  1.0  False  1
1  0.701379  1  foo 2001-01-02  1.0  False  1
2  0.281885  1  foo 2001-01-02  1.0  False  1

In [348]: dft.dtypes
Out[348]: 
A          float64
B            int64
C           object
D    datetime64[s]
E          float32
F             bool
G             int8
dtype: object
```

在 `Series` 对象上，使用 [`dtype`](https://pandas.pydata.org/docs/reference/api/pandas.Series.dtype.html#pandas.Series.dtype) 属性。

```python
In [349]: dft["A"].dtype
Out[349]: dtype('float64')
```

如果一个 pandas 对象在单个列中包含多种 dtype 的数据，则该列的 dtype 将被选择以适应所有数据类型（`object` 是最通用的）。

```python
# 这些整数被强制转换为浮点数
In [350]: pd.Series([1, 2, 3, 4, 5, 6.0])
Out[350]: 
0    1.0
1    2.0
2    3.0
3    4.0
4    5.0
5    6.0
dtype: float64

# 字符串数据强制为 ``object`` 类型
In [351]: pd.Series([1, 2, 3, 6.0, "foo"])
Out[351]: 
0      1
1      2
2      3
3    6.0
4    foo
dtype: object
```

可以通过调用 `DataFrame.dtypes.value_counts()` 来查找 `DataFrame` 中每种类型的列数。

```python
In [352]: dft.dtypes.value_counts()
Out[352]: 
float64          1
int64            1
object           1
datetime64[s]    1
float32          1
bool             1
int8             1
Name: count, dtype: int64
```

数值类型将被传播，并且可以在 DataFrame 中共存。如果传递了 dtype（直接通过 `dtype` 关键字、传递的 `ndarray` 或传递的 `Series`），则它将在 DataFrame 操作中保留。此外，不同的数值类型**不会**合并。以下示例将让你感受一下。

```python
In [353]: df1 = pd.DataFrame(np.random.randn(8, 1), columns=["A"], dtype="float32")

In [354]: df1
Out[354]: 
          A
0  0.224364
1  1.890546
2  0.182879
3  0.787847
4 -0.188449
5  0.667715
6 -0.011736
7 -0.399073

In [355]: df1.dtypes
Out[355]: 
A    float32
dtype: object

In [356]: df2 = pd.DataFrame(
   .....:     {
   .....:         "A": pd.Series(np.random.randn(8), dtype="float16"),
   .....:         "B": pd.Series(np.random.randn(8)),
   .....:         "C": pd.Series(np.random.randint(0, 255, size=8), dtype="uint8"),  # [0,255] (uint8 的范围)
   .....:     }
   .....: )
   .....: 

In [357]: df2
Out[357]: 
          A         B    C
0  0.823242  0.256090   26
1  1.607422  1.426469   86
2 -0.333740 -0.416203   46
3 -0.063477  1.139976  212
4 -1.014648 -1.193477   26
5  0.678711  0.096706    7
6 -0.040863 -1.956850  184
7 -0.357422 -0.714337  206

In [358]: df2.dtypes
Out[358]: 
A    float16
B    float64
C      uint8
dtype: object
```

### 默认值[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#defaults)

默认情况下，整数类型是 `int64`，浮点类型是 `float64`，*无论*平台如何（32 位或 64 位）。以下所有情况都将导致 `int64` 类型。

```python
In [359]: pd.DataFrame([1, 2], columns=["a"]).dtypes
Out[359]: 
a    int64
dtype: object

In [360]: pd.DataFrame({"a": [1, 2]}).dtypes
Out[360]: 
a    int64
dtype: object

In [361]: pd.DataFrame({"a": 1}, index=list(range(2))).dtypes
Out[361]: 
a    int64
dtype: object
```

请注意，NumPy 在创建数组时会选择*平台相关*的类型。以下情况在 32 位平台上**将**导致 `int32` 类型。

```python
In [362]: frame = pd.DataFrame(np.array([1, 2]))
```

### 向上转型[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#upcasting)

当与其他类型组合时，类型可能会被*向上转型*，这意味着它们会从当前类型（例如 `int` 到 `float`）提升。

```python
In [363]: df3 = df1.reindex_like(df2).fillna(value=0.0) + df2

In [364]: df3
Out[364]: 
          A         B      C
0  1.047606  0.256090   26.0
1  3.497968  1.426469   86.0
2 -0.150862 -0.416203   46.0
3  0.724370  1.139976  212.0
4 -1.203098 -1.193477   26.0
5  1.346426  0.096706    7.0
6 -0.052599 -1.956850  184.0
7 -0.756495 -0.714337  206.0

In [365]: df3.dtypes
Out[365]: 
A    float32
B    float64
C    float64
dtype: object
```

[`DataFrame.to_numpy()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.to_numpy.html#pandas.DataFrame.to_numpy) 将返回 dtypes 的*最小公分母*，即能够容纳结果同类型 NumPy 数组中**所有**类型的 dtype。这可能会强制进行一些*向上转型*。

```python
In [366]: df3.to_numpy().dtype
Out[366]: dtype('float64')
```

### astype[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#astype)

你可以使用 [`astype()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.astype.html#pandas.DataFrame.astype) 方法显式地将 dtype 从一种转换为另一种。默认情况下，这些将返回一个副本，即使 dtype 未更改（传递 `copy=False` 可以更改此行为）。此外，如果 astype 操作无效，它们将引发异常。

向上转型始终遵循 **NumPy** 规则。如果两个不同的 dtype 参与操作，则更*通用*的 dtype 将用作操作的结果。

```python
In [367]: df3
Out[367]: 
          A         B      C
0  1.047606  0.256090   26.0
1  3.497968  1.426469   86.0
2 -0.150862 -0.416203   46.0
3  0.724370  1.139976  212.0
4 -1.203098 -1.193477   26.0
5  1.346426  0.096706    7.0
6 -0.052599 -1.956850  184.0
7 -0.756495 -0.714337  206.0

In [368]: df3.dtypes
Out[368]: 
A    float32
B    float64
C    float64
dtype: object

# 转换 dtype
In [369]: df3.astype("float32").dtypes
Out[369]: 
A    float32
B    float32
C    float32
dtype: object
```

使用 [`astype()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.astype.html#pandas.DataFrame.astype) 将列的子集转换为指定类型。

```python
In [370]: dft = pd.DataFrame({"a": [1, 2, 3], "b": [4, 5, 6], "c": [7, 8, 9]})

In [371]: dft[["a", "b"]] = dft[["a", "b"]].astype(np.uint8)

In [372]: dft
Out[372]: 
   a  b  c
0  1  4  7
1  2  5  8
2  3  6  9

In [373]: dft.dtypes
Out[373]: 
a    uint8
b    uint8
c    int64
dtype: object
```

通过向 [`astype()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.astype.html#pandas.DataFrame.astype) 传递一个字典来将某些列转换为特定 dtype。

```python
In [374]: dft1 = pd.DataFrame({"a": [1, 0, 1], "b": [4, 5, 6], "c": [7, 8, 9]})

In [375]: dft1 = dft1.astype({"a": np.bool_, "c": np.float64})

In [376]: dft1
Out[376]: 
       a  b    c
0   True  4  7.0
1  False  5  8.0
2   True  6  9.0

In [377]: dft1.dtypes
Out[377]: 
a       bool
b      int64
c    float64
dtype: object
```

注意

当尝试使用 [`astype()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.astype.html#pandas.DataFrame.astype) 和 [`loc()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.loc.html#pandas.DataFrame.loc) 将列的子集转换为指定类型时，会发生向上转型。

[`loc()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.loc.html#pandas.DataFrame.loc) 尝试将我们赋值的内容适应当前的 dtypes，而 `[]` 会覆盖它们，从右侧获取 dtype。因此，以下代码段会产生非预期的结果。

```python
In [378]: dft = pd.DataFrame({"a": [1, 2, 3], "b": [4, 5, 6], "c": [7, 8, 9]})

In [379]: dft.loc[:, ["a", "b"]].astype(np.uint8).dtypes
Out[379]: 
a    uint8
b    uint8
dtype: object

In [380]: dft.loc[:, ["a", "b"]] = dft.loc[:, ["a", "b"]].astype(np.uint8)

In [381]: dft.dtypes
Out[381]: 
a    int64
b    int64
c    int64
dtype: object
```

### 对象转换[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#object-conversion)

pandas 提供了各种函数，尝试将 `object` 类型的类型强制转换为其他类型。在数据已经正确类型但存储在 `object` 数组中的情况下，可以使用 [`DataFrame.infer_objects()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.infer_objects.html#pandas.DataFrame.infer_objects) 和 [`Series.infer_objects()`](https://pandas.pydata.org/docs/reference/api/pandas.Series.infer_objects.html#pandas.Series.infer_objects) 方法将其软转换为正确类型。

> ```python
> In [382]: import datetime
> 
> In [383]: df = pd.DataFrame(
> .....:     [
> .....:         [1, 2],
> .....:         ["a", "b"],
> .....:         [datetime.datetime(2016, 3, 2), datetime.datetime(2016, 3, 2)],
> .....:     ]
> .....: )
> .....: 
> 
> In [384]: df = df.T
> 
> In [385]: df
> Out[385]: 
> 0  1                    2
> 0  1  a  2016-03-02 00:00:00
> 1  2  b  2016-03-02 00:00:00
> 
> In [386]: df.dtypes
> Out[386]: 
> 0    object
> 1    object
> 2    object
> dtype: object
> ```

由于数据被转置，原始推断将所有列存储为 object，而 `infer_objects` 将进行更正。

> ```python
> In [387]: df.infer_objects().dtypes
> Out[387]: 
> 0             int64
> 1            object
> 2    datetime64[ns]
> dtype: object
> ```

以下函数适用于一维对象数组或标量，用于将对象硬转换为指定类型：

*   [`to_numeric()`](https://pandas.pydata.org/docs/reference/api/pandas.to_numeric.html#pandas.to_numeric)（转换为数值类型）

  ```python
  In [388]: m = ["1.1", 2, 3]

  In [389]: pd.to_numeric(m)
  Out[389]: array([1.1, 2. , 3. ])
  ```

*   [`to_datetime()`](https://pandas.pydata.org/docs/reference/api/pandas.to_datetime.html#pandas.to_datetime)（转换为日期时间对象）

  ```python
  In [390]: import datetime

  In [391]: m = ["2016-07-09", datetime.datetime(2016, 3, 2)]

  In [392]: pd.to_datetime(m)
  Out[392]: DatetimeIndex(['2016-07-09', '2016-03-02'], dtype='datetime64[ns]', freq=None)
  ```

*   [`to_timedelta()`](https://pandas.pydata.org/docs/reference/api/pandas.to_timedelta.html#pandas.to_timedelta)（转换为时长对象）

  ```python
  In [393]: m = ["5us", pd.Timedelta("1day")]
  
  In [394]: pd.to_timedelta(m)
  Out[394]: TimedeltaIndex(['0 days 00:00:00.000005', '1 days 00:00:00'], dtype='timedelta64[ns]', freq=None)
  ```

要强制执行转换，我们可以传入一个 `errors` 参数，该参数指定 pandas 应如何处理无法转换为所需 dtype 或对象的元素。默认情况下，`errors='raise'`，意味着在转换过程中遇到的任何错误都将被引发。但是，如果 `errors='coerce'`，这些错误将被忽略，pandas 会将有问题的元素转换为 `pd.NaT`（对于日期时间和时长）或 `np.nan`（对于数值）。如果你读取的数据主要是所需 dtype（例如数值、日期时间），但偶尔混合了不符合要求的元素，你希望将其表示为缺失值，这可能很有用：

```python
In [395]: import datetime

In [396]: m = ["apple", datetime.datetime(2016, 3, 2)]

In [397]: pd.to_datetime(m, errors="coerce")
Out[397]: DatetimeIndex(['NaT', '2016-03-02'], dtype='datetime64[ns]', freq=None)

In [398]: m = ["apple", 2, 3]

In [399]: pd.to_numeric(m, errors="coerce")
Out[399]: array([nan,  2.,  3.])

In [400]: m = ["apple", pd.Timedelta("1day")]

In [401]: pd.to_timedelta(m, errors="coerce")
Out[401]: TimedeltaIndex([NaT, '1 days'], dtype='timedelta64[ns]', freq=None)
```

除了对象转换，[`to_numeric()`](https://pandas.pydata.org/docs/reference/api/pandas.to_numeric.html#pandas.to_numeric) 还提供了另一个参数 `downcast`，它可以选择将新（或已经）数值数据向下转换为较小的 dtype，这可以节省内存：

```python
In [402]: m = ["1", 2, 3]

In [403]: pd.to_numeric(m, downcast="integer")  # 最小的有符号整数类型
Out[403]: array([1, 2, 3], dtype=int8)

In [404]: pd.to_numeric(m, downcast="signed")  # 同 'integer'
Out[404]: array([1, 2, 3], dtype=int8)

In [405]: pd.to_numeric(m, downcast="unsigned")  # 最小的无符号整数类型
Out[405]: array([1, 2, 3], dtype=uint8)

In [406]: pd.to_numeric(m, downcast="float")  # 最小的浮点类型
Out[406]: array([1., 2., 3.], dtype=float32)
```

由于这些方法仅适用于一维数组、列表或标量；它们不能直接用于多维对象，如 DataFrames。但是，使用 [`apply()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html#pandas.DataFrame.apply)，我们可以“应用”函数到每一列：

```python
In [407]: import datetime

In [408]: df = pd.DataFrame([["2016-07-09", datetime.datetime(2016, 3, 2)]] * 2, dtype="O")

In [409]: df
Out[409]: 
            0                    1
0  2016-07-09  2016-03-02 00:00:00
1  2016-07-09  2016-03-02 00:00:00

In [410]: df.apply(pd.to_datetime)
Out[410]: 
           0          1
0 2016-07-09 2016-03-02
1 2016-07-09 2016-03-02

In [411]: df = pd.DataFrame([["1.1", 2, 3]] * 2, dtype="O")

In [412]: df
Out[412]: 
     0  1  2
0  1.1  2  3
1  1.1  2  3

In [413]: df.apply(pd.to_numeric)
Out[413]: 
     0  1  2
0  1.1  2  3
1  1.1  2  3

In [414]: df = pd.DataFrame([["5us", pd.Timedelta("1day")]] * 2, dtype="O")

In [415]: df
Out[415]: 
     0                1
0  5us  1 days 00:00:00
1  5us  1 days 00:00:00

In [416]: df.apply(pd.to_timedelta)
Out[416]: 
                       0      1
0 0 days 00:00:00.000005 1 days
1 0 days 00:00:00.000005 1 days
```

### 陷阱[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#gotchas)

对 `integer` 类型数据执行选择操作很容易将数据向上转型为 `floating`。在不引入 `nans` 的情况下，输入数据的 dtype 将被保留。另请参见[对整数 NA 的支持](https://pandas.pydata.org/docs/user_guide/gotchas.html#gotchas-intna)。

```python
In [417]: dfi = df3.astype("int32")

In [418]: dfi["E"] = 1

In [419]: dfi
Out[419]: 
   A  B    C  E
0  1  0   26  1
1  3  1   86  1
2  0  0   46  1
3  0  1  212  1
4 -1 -1   26  1
5  1  0    7  1
6  0 -1  184  1
7  0  0  206  1

In [420]: dfi.dtypes
Out[420]: 
A    int32
B    int32
C    int32
E    int64
dtype: object

In [421]: casted = dfi[dfi > 0]

In [422]: casted
Out[422]: 
     A    B    C  E
0  1.0  NaN   26  1
1  3.0  1.0   86  1
2  NaN  NaN   46  1
3  NaN  1.0  212  1
4  NaN  NaN   26  1
5  1.0  NaN    7  1
6  NaN  NaN  184  1
7  NaN  NaN  206  1

In [423]: casted.dtypes
Out[423]: 
A    float64
B    float64
C      int32
E      int64
dtype: object
```

而浮点类型保持不变。

```python
In [424]: dfa = df3.copy()

In [425]: dfa["A"] = dfa["A"].astype("float32")

In [426]: dfa.dtypes
Out[426]: 
A    float32
B    float64
C    float64
dtype: object

In [427]: casted = dfa[df2 > 0]

In [428]: casted
Out[428]: 
          A         B      C
0  1.047606  0.256090   26.0
1  3.497968  1.426469   86.0
2       NaN       NaN   46.0
3       NaN  1.139976  212.0
4       NaN       NaN   26.0
5  1.346426  0.096706    7.0
6       NaN       NaN  184.0
7       NaN       NaN  206.0

In [429]: casted.dtypes
Out[429]: 
A    float32
B    float64
C    float64
dtype: object
```

## 根据 `dtype` 选择列[#](about:reader?url=https%3A%2F%2Fpandas.pydata.org%2Fdocs%2Fuser_guide%2Fbasics.html#selecting-columns-based-on-dtype)

[`select_dtypes()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.select_dtypes.html#pandas.DataFrame.select_dtypes) 方法实现了基于 dtype 选择列的子集。

首先，让我们创建一个具有大量不同 dtypes 的 [`DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html#pandas.DataFrame)：

```python
In [430]: df = pd.DataFrame(
   .....:     {
   .....:         "string": list("abc"),
   .....:         "int64": list(range(1, 4)),
   .....:         "uint8": np.arange(3, 6).astype("u1"),
   .....:         "float64": np.arange(4.0, 7.0),
   .....:         "bool1": [True, False, True],
   .....:         "bool2": [False, True, False],
   .....:         "dates": pd.date_range("now", periods=3),
   .....:         "category": pd.Series(list("ABC")).astype("category"),
   .....:     }
   .....: )
   .....: 

In [431]: df["tdeltas"] = df.dates.diff()

In [432]: df["uint64"] = np.arange(3, 6).astype("u8")

In [433]: df["other_dates"] = pd.date_range("20130101", periods=3)

In [434]: df["tz_aware_dates"] = pd.date_range("20130101", periods=3, tz="US/Eastern")

In [435]: df
Out[435]: 
  string  int64  uint8  ...  uint64  other_dates            tz_aware_dates
0      a      1      3  ...       3   2013-01-01 2013-01-01 00:00:00-05:00
1      b      2      4  ...       4   2013-01-02 2013-01-02 00:00:00-05:00
2      c      3      5  ...       5   2013-01-03 2013-01-03 00:00:00-05:00

[3 rows x 12 columns]
```

以及 dtypes：

```python
In [436]: df.dtypes
Out[436]: 
string                                object
int64                                  int64
uint8                                  uint8
float64                              float64
bool1                                   bool
bool2                                   bool
dates                         datetime64[ns]
category                            category
tdeltas                      timedelta64[ns]
uint64                                uint64
other_dates                   datetime64[ns]
tz_aware_dates    datetime64[ns, US/Eastern]
dtype: object
```

[`select_dtypes()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.select_dtypes.html#pandas.DataFrame.select_dtypes) 有两个参数 `include` 和 `exclude`，允许你指定“给我**有**这些 dtypes 的列”（`include`）和/或“给我**没有**这些 dtypes 的列”（`exclude`）。

例如，要选择 `bool` 列：

```python
In [437]: df.select_dtypes(include=[bool])
Out[437]: 
   bool1  bool2
0   True  False
1  False   True
2   True  False
```

你也可以传递 [NumPy 类型层次结构](https://numpy.org/doc/stable/reference/arrays.scalars.html)中的 dtype 名称：

```python
In [438]: df.select_dtypes(include=["bool"])
Out[438]: 
   bool1  bool2
0   True  False
1  False   True
2   True  False
```

[`select_dtypes()`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.select_dtypes.html#pandas.DataFrame.select_dtypes) 也适用于通用 dtypes。

例如，选择所有数值和布尔列，同时排除无符号整数：

```python
In [439]: df.select_dtypes(include=["number", "bool"], exclude=["unsignedinteger"])
Out[439]: 
   int64  float64  bool1  bool2 tdeltas
0      1      4.0   True  False     NaT
1      2      5.0  False   True  1 days
2      3      6.0   True  False  1 days
```

要选择字符串列，你必须使用 `object` 类型：

```python
In [440]: df.select_dtypes(include=["object"])
Out[440]: 
  string
0      a
1      b
2      c
```

要查看通用 `dtype` 如 `numpy.number` 的所有子 dtype，你可以定义一个返回子 dtype 树的函数：

```python
In [441]: def subdtypes(dtype):
   .....:     subs = dtype.__subclasses__()
   .....:     if not subs:
   .....:         return dtype
   .....:     return [dtype, [subdtypes(dt) for dt in subs]]
   .....: 
```

所有 NumPy dtype 都是 `numpy.generic` 的子类：

```python
In [442]: subdtypes(np.generic)
Out[442]: 
[numpy.generic,
 [[numpy.number,
   [[numpy.integer,
     [[numpy.signedinteger,
       [numpy.int8,
        numpy.int16,
        numpy.int32,
        numpy.int64,
        numpy.longlong,
        numpy.timedelta64]],
      [numpy.unsignedinteger,
       [numpy.uint8,
        numpy.uint16,
        numpy.uint32,
        numpy.uint64,
        numpy.ulonglong]]]],
    [numpy.inexact,
     [[numpy.floating,
       [numpy.float16, numpy.float32, numpy.float64, numpy.longdouble]],
      [numpy.complexfloating,
       [numpy.complex64, numpy.complex128, numpy.clongdouble]]]]]],
  [numpy.flexible,
   [[numpy.character, [numpy.bytes_, numpy.str_]],
    [numpy.void, [numpy.record]]]],
  numpy.bool_,
  numpy.datetime64,
  numpy.object_]]
```

注意

pandas 还定义了类型 `category` 和 `datetime64[ns, tz]`，它们未集成到正常的 NumPy 层次结构中，并且不会出现在上述函数中。