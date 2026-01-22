# （翻译）NumPy 绝对初学者指南 

------

欢迎来到 NumPy 绝对初学者指南！

NumPy（**Num**erical **Py**thon）是一个开源的 Python 库，广泛应用于科学和工程领域。NumPy 库包含多维数组数据结构，例如同质的 N 维 `ndarray`，以及一个能高效操作这些数据结构的庞大函数库。欲了解更多关于 NumPy 的信息，请访问 [什么是 NumPy](https://numpy.org/doc/stable/user/whatisnumpy.html#whatisnumpy)，如果您有任何意见或建议，请[联系我们](https://numpy.org/community/)！

## 如何导入 NumPy[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#how-to-import-numpy)

[安装 NumPy](https://numpy.org/install/) 后，可以按如下方式将其导入 Python 代码：

```
import numpy as np
```

这种广泛使用的约定允许通过一个简短且可识别的前缀（`np.`）来访问 NumPy 功能，同时将 NumPy 功能与其他同名的功能区分开来。

## 如何阅读示例代码[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#reading-the-example-code)

在整个 NumPy 文档中，你会看到如下形式的代码块：

```
>>> a = np.array([[1, 2, 3],
...               [4, 5, 6]])
>>> a.shape
(2, 3)
```

以 `>>>` 或 `...` 开头的文本是 **输入**，即你将在脚本或 Python 提示符下输入的代码。其余一切都是 **输出**，即运行代码的结果。请注意，`>>>` 和 `...` 不是代码的一部分，如果在 Python 提示符下输入，可能会导致错误。

要运行示例中的代码，你可以将其复制并粘贴到 Python 脚本或 REPL 中，或者使用文档中多处提供的浏览器实验性交互式示例。

## 为什么使用 NumPy？[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#why-use-numpy)

Python 列表是优秀的通用容器。它们可以是“异构的”，意味着可以包含多种类型的元素，并且在少量元素上执行单个操作时速度相当快。

根据数据的特性和需要执行的操作类型，其他容器可能更合适；通过利用这些特性，我们可以提高速度、减少内存消耗，并为执行各种常见处理任务提供高级语法。当有大量“同质”（相同类型）数据需要在 CPU 上处理时，NumPy 就大放异彩。

## 什么是“数组”？[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#what-is-an-array)

在计算机编程中，数组是一种用于存储和检索数据的结构。我们通常将数组视为空间中的网格，每个单元格存储数据的一个元素。例如，如果数据的每个元素都是一个数字，我们可以将“一维”数组可视化为一个列表：

1520

二维数组就像一张表格：

152083611729

三维数组就像一组表格，可能像打印在单独页面上一样堆叠起来。在 NumPy 中，这一概念被推广到任意数量的维度，因此基本的数组类被称为 `ndarray`：它代表一个“N 维数组”。

大多数 NumPy 数组都有一些限制。例如：

*   数组的所有元素必须是相同的数据类型。
*   创建后，数组的总大小不能更改。
*   形状必须是“矩形的”，而不是“锯齿状的”；例如，二维数组的每一行必须具有相同数量的列。

当这些条件满足时，NumPy 会利用这些特性，使数组比限制较少的数据结构更快、内存效率更高、使用更方便。

在本文档的其余部分，我们将使用“数组”一词来指代 `ndarray` 的实例。

## 数组基础[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#array-fundamentals)

初始化数组的一种方法是使用 Python 序列，例如列表。例如：

```
>>> a = np.array([1, 2, 3, 4, 5, 6])
>>> a
array([1, 2, 3, 4, 5, 6])
```

数组的元素可以通过[多种方式](https://numpy.org/doc/stable/user/quickstart.html#quickstart-indexing-slicing-and-iterating)访问。例如，我们可以像访问原始列表中的元素一样访问该数组的单个元素：使用方括号内的元素整数索引。

```
>>> a[0]
1
```

注意

与内置的 Python 序列一样，NumPy 数组是“从 0 开始索引的”：数组的第一个元素使用索引 `0` 访问，而不是 `1`。

和原始列表一样，数组是可变的。

```
>>> a[0] = 10
>>> a
array([10,  2,  3,  4,  5,  6])
```

也和原始列表一样，Python 切片符号可以用于索引。

```
>>> a[:3]
array([10, 2, 3])
```

一个主要区别是，对列表进行切片索引会将元素复制到一个新列表中，但对数组进行切片会返回一个 **视图**：一个引用原始数组中数据的对象。原始数组可以通过该视图进行修改。

```
>>> b = a[3:]
>>> b
array([4, 5, 6])
>>> b[0] = 40
>>> a
array([ 10,  2,  3, 40,  5,  6])
```

有关数组操作何时返回视图而不是副本的更全面解释，请参见[副本和视图](https://numpy.org/doc/stable/user/basics.copies.html#basics-copies-and-views)。

二维及更高维的数组可以从嵌套的 Python 序列初始化：

```
>>> a = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
>>> a
array([[ 1,  2,  3,  4],
       [ 5,  6,  7,  8],
       [ 9, 10, 11, 12]])
```

在 NumPy 中，数组的维度有时被称为“轴”。这个术语可能有助于区分数组的维度和数组所表示数据的维度。例如，数组 `a` 可以表示三个点，每个点位于一个四维空间中，但 `a` 只有两个“轴”。

数组与列表的另一个区别是，可以通过在 **单组** 方括号内指定每个轴的索引来访问数组的元素，索引之间用逗号分隔。例如，元素 `8` 位于第 `1` 行第 `3` 列：

```
>>> a[1, 3]
8
```

注意

在数学中，习惯于先引用行索引，再引用列索引来指代矩阵的元素。对于二维数组来说这恰好是对的，但更好的思维模型是认为列索引在 **最后**，行索引在 **倒数第二**。这可以推广到具有 **任意** 维度的数组。

注意

你可能会听到 0-D（零维）数组被称为“标量”，1-D（一维）数组被称为“向量”，2-D（二维）数组被称为“矩阵”，或者 N-D（N 维，其中“N”通常是大于 2 的整数）数组被称为“张量”。为了清晰起见，最好在指代数组时避免使用这些数学术语，因为具有这些名称的数学对象的行为与数组不同（例如，“矩阵”乘法与“数组”乘法根本不同），并且科学 Python 生态系统中还有其他具有这些名称的对象（例如，PyTorch 的基本数据结构是“张量”）。

## 数组属性[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#array-attributes)

*本节介绍数组的* `ndim`、`shape`、`size` *和* `dtype` *属性。*

------

数组的维度数包含在 `ndim` 属性中。

```
>>> a.ndim
2
```

数组的形状是一个非负整数的元组，用于指定每个维度上的元素数量。

```
>>> a.shape
(3, 4)
>>> len(a.shape) == a.ndim
True
```

数组中固定的元素总数包含在 `size` 属性中。

```
>>> a.size
12
>>> import math
>>> a.size == math.prod(a.shape)
True
```

数组通常是“同质的”，意味着它们只包含一种“数据类型”的元素。数据类型记录在 `dtype` 属性中。

```
>>> a.dtype
dtype('int64')  # "int" 表示整数，"64" 表示 64 位
```

[在此处阅读有关数组属性的更多信息](https://numpy.org/doc/stable/reference/arrays.ndarray.html#arrays-ndarray)，并了解[数组对象](https://numpy.org/doc/stable/reference/arrays.html#arrays)。

## 如何创建基本数组[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#how-to-create-a-basic-array)

*本节介绍* `np.zeros()`、`np.ones()`、`np.empty()`、`np.arange()`、`np.linspace()`

------

除了从元素序列创建数组外，你还可以轻松创建一个填充 `0` 的数组：

```
>>> np.zeros(2)
array([0., 0.])
```

或者一个填充 `1` 的数组：

```
>>> np.ones(2)
array([1., 1.])
```

甚至是一个空数组！函数 `empty` 创建一个数组，其初始内容是随机的，取决于内存的状态。使用 `empty` 而不是 `zeros`（或类似函数）的原因是速度——只需确保之后填充每个元素！

```
>>> # 创建一个包含 2 个元素的空数组
>>> np.empty(2) 
array([3.14, 42.  ])  # 可能变化
```

你可以创建一个包含一系列元素的数组：

```
>>> np.arange(4)
array([0, 1, 2, 3])
```

甚至可以创建一个包含均匀间隔区间的数组。为此，你需要指定 **第一个数字**、**最后一个数字** 和 **步长**。

```
>>> np.arange(2, 9, 2)
array([2, 4, 6, 8])
```

你也可以使用 `np.linspace()` 创建一个在指定区间内线性间隔值的数组：

```
>>> np.linspace(0, 10, num=5)
array([ 0. ,  2.5,  5. ,  7.5, 10. ])
```

**指定数据类型**

虽然默认数据类型是浮点数（`np.float64`），但你可以使用 `dtype` 关键字显式指定所需的数据类型。

```
>>> x = np.ones(2, dtype=np.int64)
>>> x
array([1, 1])
```

[在此处了解更多关于创建数组的信息](https://numpy.org/doc/stable/user/quickstart.html#quickstart-array-creation)

## 添加、删除和排序元素[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#adding-removing-and-sorting-elements)

*本节介绍* `np.sort()`、`np.concatenate()`

------

使用 `np.sort()` 可以轻松对数组进行排序。调用该函数时，可以指定 `axis`、`kind` 和 `order`。

如果你从这个数组开始：

```
>>> arr = np.array([2, 1, 5, 3, 7, 4, 6, 8])
```

你可以快速按升序对数字进行排序：

```
>>> np.sort(arr)
array([1, 2, 3, 4, 5, 6, 7, 8])
```

除了返回数组排序副本的 `sort` 之外，你还可以使用：

*   [`argsort`](https://numpy.org/doc/stable/reference/generated/numpy.argsort.html#numpy.argsort)，它是沿指定轴的间接排序，
*   [`lexsort`](https://numpy.org/doc/stable/reference/generated/numpy.lexsort.html#numpy.lexsort)，它是基于多个键的间接稳定排序，
*   [`searchsorted`](https://numpy.org/doc/stable/reference/generated/numpy.searchsorted.html#numpy.searchsorted)，它会在已排序数组中查找元素，以及
*   [`partition`](https://numpy.org/doc/stable/reference/generated/numpy.partition.html#numpy.partition)，它是部分排序。

要了解更多关于数组排序的信息，请参阅：[`sort`](https://numpy.org/doc/stable/reference/generated/numpy.sort.html#numpy.sort)。

如果你从这些数组开始：

```
>>> a = np.array([1, 2, 3, 4])
>>> b = np.array([5, 6, 7, 8])
```

你可以使用 `np.concatenate()` 连接它们。

```
>>> np.concatenate((a, b))
array([1, 2, 3, 4, 5, 6, 7, 8])
```

或者，如果你从这些数组开始：

```
>>> x = np.array([[1, 2], [3, 4]])
>>> y = np.array([[5, 6]])
```

你可以这样连接它们：

```
>>> np.concatenate((x, y), axis=0)
array([[1, 2],
       [3, 4],
       [5, 6]])
```

要从数组中删除元素，使用索引选择要保留的元素很简单。

要了解更多关于 `concatenate` 的信息，请参阅：[`concatenate`](https://numpy.org/doc/stable/reference/generated/numpy.concatenate.html#numpy.concatenate)。

## 如何知道数组的形状和大小？[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#how-do-you-know-the-shape-and-size-of-an-array)

*本节介绍* `ndarray.ndim`、`ndarray.size`、`ndarray.shape`

------

`ndarray.ndim` 会告诉你数组的轴数或维数。

`ndarray.size` 会告诉你数组的元素总数。这是数组形状各元素的 **乘积**。

`ndarray.shape` 将显示一个整数元组，表示数组每个维度存储的元素数量。例如，如果你有一个包含 2 行 3 列的 2-D 数组，那么你的数组形状是 `(2, 3)`。

例如，如果你创建这个数组：

```
>>> array_example = np.array([[[0, 1, 2, 3],
...                            [4, 5, 6, 7]],
...
...                           [[0, 1, 2, 3],
...                            [4, 5, 6, 7]],
...
...                           [[0 ,1 ,2, 3],
...                            [4, 5, 6, 7]]])
```

要查找数组的维数，请运行：

```
>>> array_example.ndim
3
```

要查找数组中的元素总数，请运行：

```
>>> array_example.size
24
```

要查找数组的形状，请运行：

```
>>> array_example.shape
(3, 2, 4)
```

## 可以重塑数组吗？[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#can-you-reshape-an-array)

*本节介绍* `arr.reshape()`

------

**是的！**

使用 `arr.reshape()` 将为数组赋予新的形状，而不改变数据。只需记住，当你使用 `reshape` 方法时，要生成的新数组必须与原始数组具有相同数量的元素。如果你从一个有 12 个元素的数组开始，你需要确保新数组总共也有 12 个元素。

如果你从这个数组开始：

```
>>> a = np.arange(6)
>>> print(a)
[0 1 2 3 4 5]
```

你可以使用 `reshape()` 来重塑你的数组。例如，你可以将这个数组重塑为一个有三行两列的数组：

```
>>> b = a.reshape(3, 2)
>>> print(b)
[[0 1]
 [2 3]
 [4 5]]
```

使用 `np.reshape`，你可以指定一些可选参数：

```
>>> np.reshape(a, shape=(1, 6), order='C')
array([[0, 1, 2, 3, 4, 5]])
```

`a` 是要被重塑的数组。

`shape` 是你想要的新形状。你可以指定一个整数或一个整数元组。如果指定一个整数，结果将是一个该长度的数组。形状应与原始形状兼容。

`order`：`C` 表示使用类似 C 语言的索引顺序读写元素，`F` 表示使用类似 Fortran 的索引顺序读写元素，`A` 表示如果 `a` 在内存中是 Fortran 连续的，则使用类似 Fortran 的索引顺序读写元素，否则使用类似 C 的索引顺序。（这是一个可选参数，无需指定。）

如果你想了解更多关于 C 和 Fortran 顺序的信息，可以[在此处阅读有关 NumPy 数组内部组织的更多信息](https://numpy.org/doc/stable/dev/internals.html#numpy-internals)。本质上，C 和 Fortran 顺序与索引如何对应数组在内存中的存储顺序有关。在 Fortran 中，当按数组在内存中的存储顺序遍历二维数组的元素时，**第一个** 索引是变化最快的索引。随着第一个索引变化到下一行，矩阵按列存储。这就是为什么 Fortran 被认为是 **列优先语言**。另一方面，在 C 中，**最后一个** 索引变化最快。矩阵按行存储，使其成为 **行优先语言**。选择 C 还是 Fortran 取决于保持索引约定重要还是不重新排序数据重要。

[在此处了解更多关于形状操作的信息](https://numpy.org/doc/stable/user/quickstart.html#quickstart-shape-manipulation)。

## 如何将 1D 数组转换为 2D 数组（如何向数组添加新轴）[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#how-to-convert-a-1d-array-into-a-2d-array-how-to-add-a-new-axis-to-an-array)

*本节介绍* `np.newaxis`、`np.expand_dims`

------

你可以使用 `np.newaxis` 和 `np.expand_dims` 来增加现有数组的维度。

使用 `np.newaxis` 一次将使数组的维度增加一维。这意味着 **1D** 数组将变为 **2D** 数组，**2D** 数组将变为 **3D** 数组，依此类推。

例如，如果你从这个数组开始：

```
>>> a = np.array([1, 2, 3, 4, 5, 6])
>>> a.shape
(6,)
```

你可以使用 `np.newaxis` 添加一个新轴：

```
>>> a2 = a[np.newaxis, :]
>>> a2.shape
(1, 6)
```

你可以使用 `np.newaxis` 将 1D 数组显式转换为行向量或列向量。例如，你可以通过沿第一个维度插入一个轴将 1D 数组转换为行向量：

```
>>> row_vector = a[np.newaxis, :]
>>> row_vector.shape
(1, 6)
```

或者，对于列向量，你可以沿第二个维度插入一个轴：

```
>>> col_vector = a[:, np.newaxis]
>>> col_vector.shape
(6, 1)
```

你也可以使用 `np.expand_dims` 在指定位置插入新轴来扩展数组。

例如，如果你从这个数组开始：

```
>>> a = np.array([1, 2, 3, 4, 5, 6])
>>> a.shape
(6,)
```

你可以使用 `np.expand_dims` 在索引位置 1 添加一个轴：

```
>>> b = np.expand_dims(a, axis=1)
>>> b.shape
(6, 1)
```

你可以在索引位置 0 添加一个轴：

```
>>> c = np.expand_dims(a, axis=0)
>>> c.shape
(1, 6)
```

在[此处](https://numpy.org/doc/stable/reference/routines.indexing.html#arrays-indexing)查找关于 `newaxis` 的更多信息，在 [`expand_dims`](https://numpy.org/doc/stable/reference/generated/numpy.expand_dims.html#numpy.expand_dims) 查找关于 `expand_dims` 的信息。

## 索引和切片[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#indexing-and-slicing)

你可以用与切片 Python 列表相同的方式对 NumPy 数组进行索引和切片。

```
>>> data = np.array([1, 2, 3])

>>> data[1]
2
>>> data[0:2]
array([1, 2])
>>> data[1:]
array([2, 3])
>>> data[-2:]
array([2, 3])
```

你可以这样可视化：

![../_images/np_indexing.png](https://numpy.org/doc/stable/_images/np_indexing.png)

你可能希望获取数组的一部分或特定的数组元素，以便在进一步分析或其他操作中使用。为此，你需要对数组进行子集选择、切片和/或索引。

如果你想从数组中选取满足某些条件的值，使用 NumPy 非常简单。

例如，如果你从这个数组开始：

```
>>> a = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
```

你可以轻松打印数组中所有小于 5 的值。

```
>>> print(a[a < 5])
[1 2 3 4]
```

你也可以选择例如等于或大于 5 的数字，并使用该条件索引数组。

```
>>> five_up = (a >= 5)
>>> print(a[five_up])
[ 5  6  7  8  9 10 11 12]
```

你可以选择能被 2 整除的元素：

```
>>> divisible_by_2 = a[a%2==0]
>>> print(divisible_by_2)
[ 2  4  6  8 10 12]
```

或者，你可以使用 `&` 和 `|` 运算符选择满足两个条件的元素：

```
>>> c = a[(a > 2) & (a < 11)]
>>> print(c)
[ 3  4  5  6  7  8  9 10]
```

你还可以利用逻辑运算符 **&** 和 **|** 来返回布尔值，指定数组中的值是否满足特定条件。这对于包含名称或其他类别值的数组很有用。

```
>>> five_up = (a > 5) | (a == 5)
>>> print(five_up)
[[False False False False]
 [ True  True  True  True]
 [ True  True  True True]]
```

你也可以使用 `np.nonzero()` 从数组中选择元素或索引。

从这个数组开始：

```
>>> a = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
```

你可以使用 `np.nonzero()` 打印例如小于 5 的元素的索引：

```
>>> b = np.nonzero(a < 5)
>>> print(b)
(array([0, 0, 0, 0]), array([0, 1, 2, 3]))
```

在这个例子中，返回了一个数组的元组：每个维度一个。第一个数组代表找到这些值的行索引，第二个数组代表找到这些值的列索引。

如果你想生成元素存在的坐标列表，可以将数组压缩在一起，迭代坐标列表并打印它们。例如：

```
>>> list_of_coordinates= list(zip(b[0], b[1]))

>>> for coord in list_of_coordinates:
...     print(coord)
(np.int64(0), np.int64(0))
(np.int64(0), np.int64(1))
(np.int64(0), np.int64(2))
(np.int64(0), np.int64(3))
```

你也可以使用 `np.nonzero()` 打印数组中所有小于 5 的元素：

```
>>> print(a[b])
[1 2 3 4]
```

如果你要查找的元素不存在于数组中，则返回的索引数组将为空。例如：

```
>>> not_there = np.nonzero(a == 42)
>>> print(not_there)
(array([], dtype=int64), array([], dtype=int64))
```

了解更多关于[索引和切片的信息请点此](https://numpy.org/doc/stable/user/quickstart.html#quickstart-indexing-slicing-and-iterating)和[此处](https://numpy.org/doc/stable/user/basics.indexing.html#basics-indexing)。

在 [`nonzero`](https://numpy.org/doc/stable/reference/generated/numpy.nonzero.html#numpy.nonzero) 阅读关于使用 `nonzero` 函数的更多信息。

## 如何从现有数据创建数组[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#how-to-create-an-array-from-existing-data)

*本节介绍* `slicing and indexing`、`np.vstack()`、`np.hstack()`、`np.hsplit()`、`.view()`、`copy()`

------

你可以轻松地从现有数组的一部分创建新数组。

假设你有这个数组：

```
>>> a = np.array([1,  2,  3,  4,  5,  6,  7,  8,  9, 10])
```

你随时可以通过指定想要切片数组的位置，从数组的某一部分创建一个新数组。

```
>>> arr1 = a[3:8]
>>> arr1
array([4, 5, 6, 7, 8])
```

在这里，你获取了数组从索引位置 3 到索引位置 8（但不包括位置 8 本身）的部分。

*提醒：数组索引从 0 开始。这意味着数组的第一个元素在索引 0，第二个元素在索引 1，依此类推。*

你也可以将两个现有数组垂直或水平堆叠。假设你有两个数组，`a1` 和 `a2`：

```
>>> a1 = np.array([[1, 1],
...                [2, 2]])

>>> a2 = np.array([[3, 3],
...                [4, 4]])
```

你可以使用 `vstack` 将它们垂直堆叠：

```
>>> np.vstack((a1, a2))
array([[1, 1],
       [2, 2],
       [3, 3],
       [4, 4]])
```

或者使用 `hstack` 将它们水平堆叠：

```
>>> np.hstack((a1, a2))
array([[1, 1, 3, 3],
       [2, 2, 4, 4]])
```

你可以使用 `hsplit` 将一个数组分割成几个较小的数组。你可以指定要返回的等形状数组的数量，或者指定分割应发生的列 **之后** 的位置。

假设你有这个数组：

```
>>> x = np.arange(1, 25).reshape(2, 12)
>>> x
array([[ 1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12],
       [13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24]])
```

如果你想将这个数组分割成三个等形状的数组，可以运行：

```
>>> np.hsplit(x, 3)
  [array([[ 1,  2,  3,  4],
         [13, 14, 15, 16]]), array([[ 5,  6,  7,  8],
         [17, 18, 19, 20]]), array([[ 9, 10, 11, 12],
         [21, 22, 23, 24]])]
```

如果你想在第三列和第四列之后分割你的数组，可以运行：

```
>>> np.hsplit(x, (3, 4))
  [array([[ 1,  2,  3],
         [13, 14, 15]]), array([[ 4],
         [16]]), array([[ 5,  6,  7,  8,  9, 10, 11, 12],
         [17, 18, 19, 20, 21, 22, 23, 24]])]
```

[在此处了解更多关于堆叠和分割数组的信息](https://numpy.org/doc/stable/user/quickstart.html#quickstart-stacking-arrays)。

你可以使用 `view` 方法创建一个新的数组对象，该对象查看与原始数组相同的数据（一个 **浅拷贝**）。

视图是 NumPy 的一个重要概念！NumPy 函数以及像索引和切片这样的操作，只要有可能就会返回视图。这可以节省内存并且速度更快（因为无需制作数据的副本）。但重要的是要意识到这一点——修改视图中的数据也会修改原始数组！

假设你创建这个数组：

```
>>> a = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
```

现在我们通过切片 `a` 创建一个数组 `b1` 并修改 `b1` 的第一个元素。这也会修改 `a` 中的相应元素！

```
>>> b1 = a[0, :]
>>> b1
array([1, 2, 3, 4])
>>> b1[0] = 99
>>> b1
array([99,  2,  3,  4])
>>> a
array([[99,  2,  3,  4],
       [ 5,  6,  7,  8],
       [ 9, 10, 11, 12]])
```

使用 `copy` 方法将创建数组及其数据的完整副本（一个 **深拷贝**）。要在你的数组上使用这个，可以运行：

```
>>> b2 = a.copy()
```

[在此处了解更多关于副本和视图的信息](https://numpy.org/doc/stable/user/quickstart.html#quickstart-copies-and-views)。

## 基本数组操作[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#basic-array-operations)

*本节介绍加法、减法、乘法、除法等*

------

一旦创建了数组，你就可以开始使用它们。例如，假设你创建了两个数组，一个叫“data”，另一个叫“ones”

![../_images/np_array_dataones.png](https://numpy.org/doc/stable/_images/np_array_dataones.png)

你可以用加号将数组相加。

```
>>> data = np.array([1, 2])
>>> ones = np.ones(2, dtype=int)
>>> data + ones
array([2, 3])
```

![../_images/np_data_plus_ones.png](https://numpy.org/doc/stable/_images/np_data_plus_ones.png)

当然，你除了加法还可以做更多操作！

```
>>> data - ones
array([0, 1])
>>> data * data
array([1, 4])
>>> data / data
array([1., 1.])
```

![../_images/np_sub_mult_divide.png](https://numpy.org/doc/stable/_images/np_sub_mult_divide.png)

使用 NumPy 进行基本操作很简单。如果你想求数组中所有元素的和，可以使用 `sum()`。这适用于 1D 数组、2D 数组以及更高维度的数组。

```
>>> a = np.array([1, 2, 3, 4])

>>> a.sum()
10
```

要对 2D 数组的行或列求和，需要指定 `axis`。

如果你从这个数组开始：

```
>>> b = np.array([[1, 1], [2, 2]])
```

你可以按行的轴求和：

```
>>> b.sum(axis=0)
array([3, 3])
```

你可以按列的轴求和：

```
>>> b.sum(axis=1)
array([2, 4])
```

[在此处了解更多关于基本操作的信息](https://numpy.org/doc/stable/user/quickstart.html#quickstart-basic-operations)。

## 广播[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#broadcasting)

有时你可能希望在数组和单个数字（也称为向量和标量之间的操作）或两个不同大小的数组之间执行操作。例如，你的数组（我们称之为“data”）可能包含以英里为单位的距离信息，但你想将其转换为公里。你可以执行以下操作：

```
>>> data = np.array([1.0, 2.0])
>>> data * 1.6
array([1.6, 3.2])
```

![../_images/np_multiply_broadcasting.png](https://numpy.org/doc/stable/_images/np_multiply_broadcasting.png)

NumPy 理解乘法应该在每个单元格上进行。这个概念被称为 **广播**。广播是一种允许 NumPy 对不同形状的数组执行操作的机制。数组的维度必须兼容，例如，当两个数组的维度相等或其中一个维度为 1 时。如果维度不兼容，你会得到 `ValueError`。

[在此处了解更多关于广播的信息](https://numpy.org/doc/stable/user/basics.broadcasting.html#basics-broadcasting)。

## 更多有用的数组操作[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#more-useful-array-operations)

*本节介绍最大值、最小值、总和、平均值、乘积、标准差等*

------

NumPy 还执行聚合函数。除了 `min`、`max` 和 `sum`，你还可以轻松运行 `mean` 来获取平均值，运行 `prod` 来获取元素相乘的结果，运行 `std` 来获取标准差等等。

```
>>> data = np.array([1, 2, 3])
>>> data.max()
3
>>> data.min()
1
>>> data.sum()
6
```

![../_images/np_aggregation.png](https://numpy.org/doc/stable/_images/np_aggregation.png)

让我们从这个名为“a”的数组开始

```
>>> a = np.array([[0.45053314, 0.17296777, 0.34376245, 0.5510652],
...               [0.54627315, 0.05093587, 0.40067661, 0.55645993],
...               [0.12697628, 0.82485143, 0.26590556, 0.56917101]])
```

沿行或列聚合是非常常见的。默认情况下，每个 NumPy 聚合函数都会返回整个数组的聚合值。要查找数组中元素的总和或最小值，请运行：

```
>>> a.sum()
4.8595784
```

或者：

```
>>> a.min()
0.05093587
```

你可以指定在哪个轴上计算聚合函数。例如，通过指定 `axis=0`，可以查找每列中的最小值。

```
>>> a.min(axis=0)
array([0.12697628, 0.05093587, 0.26590556, 0.5510652 ])
```

上面列出的四个值对应于数组中的列数。对于一个有四列的数组，你将得到四个值作为结果。

在此处阅读关于[数组方法的更多信息](https://numpy.org/doc/stable/reference/arrays.ndarray.html#array-ndarray-methods)。

## 创建矩阵[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#creating-matrices)

你可以传递 Python 列表的列表来创建一个 2-D 数组（或“矩阵”）在 NumPy 中表示它们。

```
>>> data = np.array([[1, 2], [3, 4], [5, 6]])
>>> data
array([[1, 2],
       [3, 4],
       [5, 6]])
```

![../_images/np_create_matrix.png](https://numpy.org/doc/stable/_images/np_create_matrix.png)

索引和切片操作在处理矩阵时非常有用：

```
>>> data[0, 1]
2
>>> data[1:3]
array([[3, 4],
       [5, 6]])
>>> data[0:2, 0]
array([1, 3])
```

![../_images/np_matrix_indexing.png](https://numpy.org/doc/stable/_images/np_matrix_indexing.png)

你可以像聚合向量一样聚合矩阵：

```
>>> data.max()
6
>>> data.min()
1
>>> data.sum()
21
```

![../_images/np_matrix_aggregation.png](https://numpy.org/doc/stable/_images/np_matrix_aggregation.png)

你可以聚合矩阵中的所有值，也可以使用 `axis` 参数跨列或行进行聚合。为了说明这一点，让我们看一个稍微修改过的数据集：

```
>>> data = np.array([[1, 2], [5, 3], [4, 6]])
>>> data
array([[1, 2],
       [5, 3],
       [4, 6]])
>>> data.max(axis=0)
array([5, 6])
>>> data.max(axis=1)
array([2, 5, 6])
```

![../_images/np_matrix_aggregation_row.png](https://numpy.org/doc/stable/_images/np_matrix_aggregation_row.png)

创建矩阵后，如果两个矩阵大小相同，可以使用算术运算符对它们进行加法和乘法。

```
>>> data = np.array([[1, 2], [3, 4]])
>>> ones = np.array([[1, 1], [1, 1]])
>>> data + ones
array([[2, 3],
       [4, 5]])
```

![../_images/np_matrix_arithmetic.png](https://numpy.org/doc/stable/_images/np_matrix_arithmetic.png)

你可以在不同大小的矩阵上执行这些算术运算，但前提是其中一个矩阵只有一列或一行。在这种情况下，NumPy 将对其操作使用广播规则。

```
>>> data = np.array([[1, 2], [3, 4], [5, 6]])
>>> ones_row = np.array([[1, 1]])
>>> data + ones_row
array([[2, 3],
       [4, 5],
       [6, 7]])
```

![../_images/np_matrix_broadcasting.png](https://numpy.org/doc/stable/_images/np_matrix_broadcasting.png)

请注意，当 NumPy 打印 N 维数组时，最后一个轴循环最快，而第一个轴循环最慢。例如：

```
>>> np.ones((4, 3, 2))
array([[[1., 1.],
        [1., 1.],
        [1., 1.]],

       [[1., 1.],
        [1., 1.],
        [1., 1.]],

       [[1., 1.],
        [1., 1.],
        [1., 1.]],

       [[1., 1.],
        [1., 1.],
        [1., 1.]]])
```

我们经常希望 NumPy 初始化数组的值。NumPy 为此提供了像 `ones()` 和 `zeros()` 这样的函数，以及用于随机数生成的 `random.Generator` 类。你只需传入想要生成元素的数量：

```
>>> np.ones(3)
array([1., 1., 1.])
>>> np.zeros(3)
array([0., 0., 0.])
>>> rng = np.random.default_rng()  # 生成随机数的最简单方式
>>> rng.random(3) 
array([0.63696169, 0.26978671, 0.04097352])
```

![../_images/np_ones_zeros_random.png](https://numpy.org/doc/stable/_images/np_ones_zeros_random.png)

如果你给 `ones()`、`zeros()` 和 `random()` 一个描述矩阵维度的元组，它们也可以用来创建 2D 数组：

```
>>> np.ones((3, 2))
array([[1., 1.],
       [1., 1.],
       [1., 1.]])
>>> np.zeros((3, 2))
array([[0., 0.],
       [0., 0.],
       [0., 0.]])
>>> rng.random((3, 2)) 
array([[0.01652764, 0.81327024],
       [0.91275558, 0.60663578],
       [0.72949656, 0.54362499]])  # 可能变化
```

![../_images/np_ones_zeros_matrix.png](https://numpy.org/doc/stable/_images/np_ones_zeros_matrix.png)

阅读更多关于创建数组的信息，包括填充 `0`、`1`、其他值或未初始化的数组，请参阅[数组创建例程](https://numpy.org/doc/stable/reference/routines.array-creation.html#routines-array-creation)。

## 生成随机数[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#generating-random-numbers)

随机数生成的使用是许多数值和机器学习算法配置和评估的重要组成部分。无论是需要随机初始化人工神经网络中的权重、将数据随机分成几组，还是随机打乱数据集，能够生成随机数（实际上，是可重复的伪随机数）都是至关重要的。

使用 `Generator.integers`，你可以生成从低值（记住 NumPy 中是包含的）到高值（不包含）的随机整数。你可以设置 `endpoint=True` 以使高值包含在内。

你可以生成一个 2 x 4 的随机整数数组，数值在 0 到 4 之间（包含 0，不包含 4）：

```
>>> rng.integers(5, size=(2, 4)) 
array([[2, 1, 1, 0],
       [0, 0, 0, 4]])  # 可能变化
```

[在此处阅读有关随机数生成的更多信息](https://numpy.org/doc/stable/reference/random/index.html#numpyrandom)。

## 如何获取唯一项及其计数[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#how-to-get-unique-items-and-counts)

*本节介绍* `np.unique()`

------

你可以使用 `np.unique` 轻松查找数组中的唯一元素。

例如，如果你从这个数组开始：

```
>>> a = np.array([11, 11, 12, 13, 14, 15, 16, 17, 12, 13, 11, 14, 18, 19, 20])
```

你可以使用 `np.unique` 打印数组中的唯一值：

```
>>> unique_values = np.unique(a)
>>> print(unique_values)
[11 12 13 14 15 16 17 18 19 20]
```

要获取 NumPy 数组中唯一值的索引（一个包含数组中唯一值首次出现位置的数组），只需将 `return_index` 参数以及你的数组传递给 `np.unique()`。

```
>>> unique_values, indices_list = np.unique(a, return_index=True)
>>> print(indices_list)
[ 0  2  3  4  5  6  7 12 13 14]
```

你可以将 `return_counts` 参数与你的数组一起传递给 `np.unique()`，以获取 NumPy 数组中唯一值的频率计数。

```
>>> unique_values, occurrence_count = np.unique(a, return_counts=True)
>>> print(occurrence_count)
[3 2 2 2 1 1 1 1 1 1]
```

这也适用于 2D 数组！如果你从这个数组开始：

```
>>> a_2d = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12], [1, 2, 3, 4]])
```

你可以找到唯一值：

```
>>> unique_values = np.unique(a_2d)
>>> print(unique_values)
[ 1  2  3  4  5  6  7  8  9 10 11 12]
```

如果没有传递 `axis` 参数，你的 2D 数组将被展平。

如果你想获取唯一的行或列，请确保传递 `axis` 参数。要查找唯一的行，指定 `axis=0`；对于列，指定 `axis=1`。

```
>>> unique_rows = np.unique(a_2d, axis=0)
>>> print(unique_rows)
[[ 1  2  3  4]
 [ 5  6  7  8]
 [ 9 10 11 12]]
```

要获取唯一的行、索引位置和出现次数，你可以使用：

```
>>> unique_rows, indices, occurrence_count = np.unique(
...      a_2d, axis=0, return_counts=True, return_index=True)
>>> print(unique_rows)
[[ 1  2  3  4]
 [ 5  6  7  8]
 [ 9 10 11 12]]
>>> print(indices)
[0 1 2]
>>> print(occurrence_count)
[2 1 1]
```

要了解更多关于在数组中查找唯一元素的信息，请参阅 [`unique`](https://numpy.org/doc/stable/reference/generated/numpy.unique.html#numpy.unique)。

## 转置和重塑矩阵[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#transposing-and-reshaping-a-matrix)

*本节介绍* `arr.reshape()`、`arr.transpose()`、`arr.T`

------

通常需要转置矩阵。NumPy 数组有一个属性 `T`，允许你转置矩阵。

![../_images/np_transposing_reshaping.png](https://numpy.org/doc/stable/_images/np_transposing_reshaping.png)

你可能还需要切换矩阵的维度。例如，当你的模型期望的输入形状与数据集不同时，就可能发生这种情况。这就是 `reshape` 方法可以派上用场的地方。你只需传入想要的新矩阵维度。

```
>>> data.reshape(2, 3)
array([[1, 2, 3],
       [4, 5, 6]])
>>> data.reshape(3, 2)
array([[1, 2],
       [3, 4],
       [5, 6]])
```

![../_images/np_reshape.png](https://numpy.org/doc/stable/_images/np_reshape.png)

你也可以使用 `.transpose()` 根据指定的值反转或更改数组的轴。

如果你从这个数组开始：

```
>>> arr = np.arange(6).reshape((2, 3))
>>> arr
array([[0, 1, 2],
       [3, 4, 5]])
```

你可以使用 `arr.transpose()` 转置你的数组。

```
>>> arr.transpose()
array([[0, 3],
       [1, 4],
       [2, 5]])
```

你也可以使用 `arr.T`：

```
>>> arr.T
array([[0, 3],
       [1, 4],
       [2, 5]])
```

要了解更多关于转置和重塑数组的信息，请参阅 [`transpose`](https://numpy.org/doc/stable/reference/generated/numpy.transpose.html#numpy.transpose) 和 [`reshape`](https://numpy.org/doc/stable/reference/generated/numpy.reshape.html#numpy.reshape)。

## 如何反转数组[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#how-to-reverse-an-array)

*本节介绍* `np.flip()`

------

NumPy 的 `np.flip()` 函数允许你沿轴翻转或反转数组的内容。使用 `np.flip()` 时，指定要反转的数组和轴。如果不指定轴，NumPy 将沿输入数组的所有轴反转内容。

**反转 1D 数组**

如果你从这个 1D 数组开始：

```
>>> arr = np.array([1, 2, 3, 4, 5, 6, 7, 8])
```

你可以这样反转它：

```
>>> reversed_arr = np.flip(arr)
```

如果你想打印反转后的数组，可以运行：

```
>>> print('Reversed Array: ', reversed_arr)
Reversed Array:  [8 7 6 5 4 3 2 1]
```

**反转 2D 数组**

2D 数组的工作方式大致相同。

如果你从这个数组开始：

```
>>> arr_2d = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
```

你可以反转所有行和所有列的内容：

```
>>> reversed_arr = np.flip(arr_2d)
>>> print(reversed_arr)
[[12 11 10  9]
 [ 8  7  6  5]
 [ 4  3  2  1]]
```

你可以轻松地仅反转 **行**：

```
>>> reversed_arr_rows = np.flip(arr_2d, axis=0)
>>> print(reversed_arr_rows)
[[ 9 10 11 12]
 [ 5  6  7  8]
 [ 1  2  3  4]]
```

或者仅反转 **列**：

```
>>> reversed_arr_columns = np.flip(arr_2d, axis=1)
>>> print(reversed_arr_columns)
[[ 4  3  2  1]
 [ 8  7  6  5]
 [12 11 10  9]]
```

你也可以只反转一列或一行的内容。例如，你可以反转索引位置 1（第二行）的行内容：

```
>>> arr_2d[1] = np.flip(arr_2d[1])
>>> print(arr_2d)
[[ 1  2  3  4]
 [ 8  7  6  5]
 [ 9 10 11 12]]
```

你也可以反转索引位置 1（第二列）的列内容：

```
>>> arr_2d[:,1] = np.flip(arr_2d[:,1])
>>> print(arr_2d)
[[ 1 10  3  4]
 [ 8  7  6  5]
 [ 9  2 11 12]]
```

在 [`flip`](https://numpy.org/doc/stable/reference/generated/numpy.flip.html#numpy.flip) 阅读有关反转数组的更多信息。

## 重塑和展平多维数组[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#reshaping-and-flattening-multidimensional-arrays)

*本节介绍* `.flatten()`、`ravel()`

------

有两种流行的展平数组的方法：`.flatten()` 和 `.ravel()`。两者之间的主要区别在于，使用 `ravel()` 创建的新数组实际上是对父数组的引用（即，一个“视图”）。这意味着对新数组的任何更改也会影响父数组。由于 `ravel` 不创建副本，因此内存效率高。

如果你从这个数组开始：

```
>>> x = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
```

你可以使用 `flatten` 将数组展平为 1D 数组。

```
>>> x.flatten()
array([ 1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12])
```

当你使用 `flatten` 时，对新数组的更改不会改变父数组。

例如：

```
>>> a1 = x.flatten()
>>> a1[0] = 99
>>> print(x)  # 原始数组
[[ 1  2  3  4]
 [ 5  6  7  8]
 [ 9 10 11 12]]
>>> print(a1)  # 新数组
[99  2  3  4  5  6  7  8  9 10 11 12]
```

但是当你使用 `ravel` 时，对新数组所做的更改会影响父数组。

例如：

```
>>> a2 = x.ravel()
>>> a2[0] = 98
>>> print(x)  # 原始数组
[[98  2  3  4]
 [ 5  6  7  8]
 [ 9 10 11 12]]
>>> print(a2)  # 新数组
[98  2  3  4  5  6  7  8  9 10 11 12]
```

在 [`ndarray.flatten`](https://numpy.org/doc/stable/reference/generated/numpy.ndarray.flatten.html#numpy.ndarray.flatten) 阅读有关 `flatten` 的更多信息，在 [`ravel`](https://numpy.org/doc/stable/reference/generated/numpy.ravel.html#numpy.ravel) 阅读有关 `ravel` 的信息。

## 如何访问文档字符串以获取更多信息[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#how-to-access-the-docstring-for-more-information)

*本节介绍* `help()`、`?`、`??`

------

在数据科学生态系统中，Python 和 NumPy 是为用户着想而构建的。最好的例子之一就是内置的文档访问功能。每个对象都包含对一个字符串的引用，该字符串称为 **文档字符串（docstring）**。在大多数情况下，这个文档字符串包含对对象及其使用方式的快速而简洁的摘要。Python 有一个内置的 `help()` 函数，可以帮助你访问这些信息。这意味着几乎在任何需要更多信息的时候，你都可以使用 `help()` 来快速找到所需的信息。

例如：

```
>>> help(max)
Help on built-in function max in module builtins:

max(...)
    max(iterable, *[, default=obj, key=func]) -> value
    max(arg1, arg2, *args, *[, key=func]) -> value

    With a single iterable argument, return its biggest item. The
    default keyword-only argument specifies an object to return if
    the provided iterable is empty.
    With two or more arguments, return the largest argument.
```

因为访问额外信息非常有用，所以 IPython 使用 `?` 字符作为访问此文档以及其他相关信息的快捷方式。IPython 是一个用于多种语言交互式计算命令的外壳。[你可以在此处找到有关 IPython 的更多信息](https://ipython.org/)。

例如：

```
In [0]: max?
max(iterable, *[, default=obj, key=func]) -> value
max(arg1, arg2, *args, *[, key=func]) -> value

With a single iterable argument, return its biggest item. The
default keyword-only argument specifies an object to return if
the provided iterable is empty.
With two or more arguments, return the largest argument.
Type:      builtin_function_or_method
```

你甚至可以对自己的对象方法和对象本身使用这种表示法。

假设你创建这个数组：

```
>>> a = np.array([1, 2, 3, 4, 5, 6])
```

然后你可以获取大量有用的信息（首先是关于 `a` 本身的详细信息，然后是 `ndarray` 的文档字符串，`a` 是其一个实例）：

```
In [1]: a?
Type:            ndarray
String form:     [1 2 3 4 5 6]
Length:          6
File:            ~/anaconda3/lib/python3.9/site-packages/numpy/__init__.py
Docstring:       <no docstring>
Class docstring:
ndarray(shape, dtype=float, buffer=None, offset=0,
        strides=None, order=None)

An array object represents a multidimensional, homogeneous array
of fixed-size items.  An associated data-type object describes the
format of each element in the array (its byte-order, how many bytes it
occupies in memory, whether it is an integer, a floating point number,
or something else, etc.)

Arrays should be constructed using `array`, `zeros` or `empty` (refer
to the See Also section below).  The parameters given here refer to
a low-level method (`ndarray(...)`) for instantiating an array.

For more information, refer to the `numpy` module and examine the
methods and attributes of an array.

Parameters
----------
(for the __new__ method; see Notes below)

shape : tuple of ints
        Shape of created array.
...
```

这也适用于 **你** 创建的函数和其他对象。只需记住使用字符串字面量（`""" """` 或 `''' '''` 包裹你的文档）在你的函数中包含一个文档字符串。

例如，如果你创建这个函数：

```
>>> def double(a):
...   '''Return a * 2'''
...   return a * 2
```

你可以获取有关该函数的信息：

```
In [2]: double?
Signature: double(a)
Docstring: Return a * 2
File:      ~/Desktop/<ipython-input-23-b5adf20be596>
Type:      function
```

通过阅读你感兴趣的对象的源代码，你可以获取另一层次的信息。使用双问号（`??`）可以访问源代码。

例如：

```
In [3]: double??
Signature: double(a)
Source:
def double(a):
    '''Return a * 2'''
    return a * 2
File:      ~/Desktop/<ipython-input-23-b5adf20be596>
Type:      function
```

如果相关对象是用 Python 以外的语言编译的，使用 `??` 将返回与 `?` 相同的信息。你会发现许多内置对象和类型都是如此，例如：

```
In [4]: len?
Signature: len(obj, /)
Docstring: Return the number of items in a container.
Type:      builtin_function_or_method
```

和：

```
In [5]: len??
Signature: len(obj, /)
Docstring: Return the number of items in a container.
Type:      builtin_function_or_method
```

具有相同的输出，因为它们是用 Python 以外的编程语言编译的。

## 使用数学公式[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#working-with-mathematical-formulas)

能够轻松实现适用于数组的数学公式，是 NumPy 在科学 Python 社区中如此广泛使用的原因之一。

例如，这是均方误差公式（处理回归的监督机器学习模型中使用的核心公式）：

![../_images/np_MSE_formula.png](https://numpy.org/doc/stable/_images/np_MSE_formula.png)

在 NumPy 中实现这个公式简单明了：

![../_images/np_MSE_implementation.png](https://numpy.org/doc/stable/_images/np_MSE_implementation.png)

之所以能如此有效地工作，是因为 `predictions` 和 `labels` 可以包含一个或一千个值。它们只需要大小相同。

你可以这样可视化：

![../_images/np_mse_viz1.png](https://numpy.org/doc/stable/_images/np_mse_viz1.png)

在这个例子中，预测和标签向量都包含三个值，这意味着 `n` 的值为 3。执行减法后，向量中的值被平方。然后 NumPy 对这些值求和，结果就是该预测的误差值和模型质量的评分。

![../_images/np_mse_viz2.png](https://numpy.org/doc/stable/_images/np_mse_viz2.png)![../_images/np_MSE_explanation2.png](https://numpy.org/doc/stable/_images/np_MSE_explanation2.png)

## 如何保存和加载 NumPy 对象[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#how-to-save-and-load-numpy-objects)

*本节介绍* `np.save`、`np.savez`、`np.savetxt`、`np.load`、`np.loadtxt`

------

在某些时候，你会希望将数组保存到磁盘并加载它们，而不必重新运行代码。幸运的是，有几种方法可以保存和加载 NumPy 对象。`ndarray` 对象可以使用 `loadtxt` 和 `savetxt` 函数保存到磁盘文件和从磁盘文件加载，这些函数处理普通文本文件；`load` 和 `save` 函数处理具有 **.npy** 文件扩展名的 NumPy 二进制文件；以及 `savez` 函数处理具有 **.npz** 文件扩展名的 NumPy 文件。

**.npy** 和 **.npz** 文件以允许正确检索数组的方式存储数据、形状、dtype 和其他必需信息，即使文件在具有不同架构的另一台机器上也是如此。

如果你想存储单个 `ndarray` 对象，使用 `np.save` 将其存储为 .npy 文件。如果你想在单个文件中存储多个 `ndarray` 对象，使用 `np.savez` 将其存储为 .npz 文件。你也可以使用 [`savez_compressed`](https://numpy.org/doc/stable/reference/generated/numpy.savez_compressed.html#numpy.savez_compressed) 将多个数组以压缩的 npz 格式保存到单个文件中。

使用 `np.save()` 保存和加载数组很容易。只需确保指定要保存的数组和文件名。例如，如果你创建这个数组：

```
>>> a = np.array([1, 2, 3, 4, 5, 6])
```

你可以将其保存为“filename.npy”：

```
>>> np.save('filename', a)
```

你可以使用 `np.load()` 重建你的数组。

```
>>> b = np.load('filename.npy')
```

如果你想检查你的数组，可以运行：

```
>>> print(b)
[1 2 3 4 5 6]
```

你可以使用 `np.savetxt` 将 NumPy 数组保存为纯文本文件，如 **.csv** 或 **.txt** 文件。

例如，如果你创建这个数组：

```
>>> csv_arr = np.array([1, 2, 3, 4, 5, 6, 7, 8])
```

你可以轻松地将其保存为名为“new_file.csv”的 .csv 文件，如下所示：

```
>>> np.savetxt('new_file.csv', csv_arr)
```

你可以使用 `loadtxt()` 快速轻松地加载保存的文本文件：

```
>>> np.loadtxt('new_file.csv')
array([1., 2., 3., 4., 5., 6., 7., 8.])
```

`savetxt()` 和 `loadtxt()` 函数接受额外的可选参数，如页眉、页脚和分隔符。虽然文本文件可能更易于共享，但 .npy 和 .npz 文件更小且读取速度更快。如果你需要对文本文件进行更复杂的处理（例如，如果你需要处理包含缺失值的行），你将需要使用 [`genfromtxt`](https://numpy.org/doc/stable/reference/generated/numpy.genfromtxt.html#numpy.genfromtxt) 函数。

使用 [`savetxt`](https://numpy.org/doc/stable/reference/generated/numpy.savetxt.html#numpy.savetxt)，你可以指定页眉、页脚、注释等。

在此处了解关于[输入和输出例程的更多信息](https://numpy.org/doc/stable/reference/routines.io.html#routines-io)。

## 导入和导出 CSV[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#importing-and-exporting-a-csv)

读取包含现有信息的 CSV 文件很简单。最好也是最简单的方法是使用 [Pandas](https://pandas.pydata.org/)。

```
>>> import pandas as pd

>>> # 如果你的所有列都是相同类型：
>>> x = pd.read_csv('music.csv', header=0).values
>>> print(x)
[['Billie Holiday' 'Jazz' 1300000 27000000]
 ['Jimmie Hendrix' 'Rock' 2700000 70000000]
 ['Miles Davis' 'Jazz' 1500000 48000000]
 ['SIA' 'Pop' 2000000 74000000]]

>>> # 你也可以简单地选择你需要的列：
>>> x = pd.read_csv('music.csv', usecols=['Artist', 'Plays']).values
>>> print(x)
[['Billie Holiday' 27000000]
 ['Jimmie Hendrix' 70000000]
 ['Miles Davis' 48000000]
 ['SIA' 74000000]]
```

![../_images/np_pandas.png](https://numpy.org/doc/stable/_images/np_pandas.png)

使用 Pandas 导出数组也很简单。如果你是 NumPy 新手，可能希望从数组中的值创建一个 Pandas 数据框，然后使用 Pandas 将数据框写入 CSV 文件。

如果你创建了这个数组“a”

```
>>> a = np.array([[-2.58289208,  0.43014843, -1.24082018, 1.59572603],
...               [ 0.99027828, 1.17150989,  0.94125714, -0.14692469],
...               [ 0.76989341,  0.81299683, -0.95068423, 0.11769564],
...               [ 0.20484034,  0.34784527,  1.96979195, 0.51992837]])
```

你可以创建一个 Pandas 数据框

```
>>> df = pd.DataFrame(a)
>>> print(df)
          0         1         2         3
0 -2.582892  0.430148 -1.240820  1.595726
1  0.990278  1.171510  0.941257 -0.146925
2  0.769893  0.812997 -0.950684  0.117696
3  0.204840  0.347845  1.969792  0.519928
```

你可以轻松保存数据框：

```
>>> df.to_csv('pd.csv')
```

并读取你的 CSV：

```
>>> data = pd.read_csv('pd.csv')
```

![../_images/np_readcsv.png](https://numpy.org/doc/stable/_images/np_readcsv.png)

你也可以使用 NumPy 的 `savetxt` 方法保存数组。

```
>>> np.savetxt('np.csv', a, fmt='%.2f', delimiter=',', header='1,  2,  3,  4')
```

如果你使用命令行，可以随时使用如下命令读取保存的 CSV：

```
$ cat np.csv
#  1,  2,  3,  4
-2.58,0.43,-1.24,1.60
0.99,1.17,0.94,-0.15
0.77,0.81,-0.95,0.12
0.20,0.35,1.97,0.52
```

或者你可以随时用文本编辑器打开文件！

如果你有兴趣了解更多关于 Pandas 的信息，请查看[官方 Pandas 文档](https://pandas.pydata.org/index.html)。了解如何使用[官方 Pandas 安装信息](https://pandas.pydata.org/pandas-docs/stable/install.html)安装 Pandas。

## 使用 Matplotlib 绘制数组[#](about:reader?url=https%3A%2F%2Fnumpy.org%2Fdoc%2Fstable%2Fuser%2Fabsolute_beginners.html#plotting-arrays-with-matplotlib)

如果你需要为你的值生成图表，使用 [Matplotlib](https://matplotlib.org/) 非常简单。

例如，你可能有这样的数组：

```
>>> a = np.array([2, 1, 5, 7, 4, 6, 8, 14, 10, 9, 18, 20, 22])
```

如果你已经安装了 Matplotlib，可以这样导入：

```
>>> import matplotlib.pyplot as plt

# 如果你在使用 Jupyter Notebook，可能还需要运行以下代码行以在笔记本中显示你的代码：

%matplotlib inline
```

绘制你的值只需运行：

```
>>> plt.plot(a)

# 如果你从命令行运行，可能需要这样做：
# >>> plt.show()
```

![../_images/matplotlib1.png](https://numpy.org/doc/stable/_images/matplotlib1.png)

例如，你可以像这样绘制 1D 数组：

```
>>> x = np.linspace(0, 5, 20)
>>> y = np.linspace(0, 10, 20)
>>> plt.plot(x, y, 'purple') # 线条
>>> plt.plot(x, y, 'o')      # 点
```

![../_images/matplotlib2.png](https://numpy.org/doc/stable/_images/matplotlib2.png)

使用 Matplotlib，你可以访问大量的可视化选项。

```
>>> fig = plt.figure()
>>> ax = fig.add_subplot(projection='3d')
>>> X = np.arange(-5, 5, 0.15)
>>> Y = np.arange(-5, 5, 0.15)
>>> X, Y = np.meshgrid(X, Y)
>>> R = np.sqrt(X**2 + Y**2)
>>> Z = np.sin(R)

>>> ax.plot_surface(X, Y, Z, rstride=1, cstride=1, cmap='viridis')
```

![../_images/matplotlib3.png](https://numpy.org/doc/stable/_images/matplotlib3.png)

要了解更多关于 Matplotlib 及其功能的信息，请查看[官方文档](https://matplotlib.org/)。有关安装 Matplotlib 的说明，请参阅官方[安装部分](https://matplotlib.org/users/installing.html)。

