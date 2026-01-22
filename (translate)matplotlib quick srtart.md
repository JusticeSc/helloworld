# （翻译）Matplotlib快速入门指南

------

本教程涵盖一些基本的使用模式和最佳实践，帮助你开始使用 Matplotlib。

```python
import matplotlib.pyplot as plt
import numpy as np
```

## 一个简单的例子

Matplotlib 将你的数据绘制在 **Figure**（例如窗口、Jupyter 小部件等）上，每个 Figure 可以包含一个或多个 **Axes**（坐标系），这是一个可以指定 x-y 坐标点（或极坐标图中的 theta-r，3D 图中的 x-y-z 等）的区域。创建带有坐标系的图形最简单的方法是使用 `pyplot.subplots`。然后我们可以使用 `Axes.plot` 在坐标系上绘制一些数据，并用 `show` 来显示图形：

```python
fig, ax = plt.subplots()             # 创建一个包含单个坐标系的图形
ax.plot([1, 2, 3, 4], [1, 4, 2, 3])  # 在坐标系上绘制一些数据
plt.show()                           # 显示图形
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_001.png)

根据你工作的环境，有时可以省略 `plt.show()`。例如在 Jupyter notebook 中，它会自动显示代码单元中创建的所有图形。

## 图形的组成部分

下图展示了 Matplotlib 图形的组成部分。

![../../_images/anatomy.png](https://matplotlib.org/stable/_images/anatomy.png)

### **Figure**

完整的图形。Figure 会跟踪所有的子 **Axes**、一组“特殊”的 Artist（标题、图例、颜色条等），甚至是嵌套的子图。

通常，你会通过以下函数之一创建新的图形：

`subplots()` 和 `subplot_mosaic` 是便捷函数，它们会在图形内部创建坐标系对象，但你也可以稍后手动添加坐标系。

关于图形的更多信息，包括平移和缩放，请参阅 [Figure 介绍](https://matplotlib.org/stable/users/explain/figure/figure_intro.html#figure-intro)。

### **Axes**

Axes 是一个附着在 Figure 上的 Artist，它包含一个用于绘制数据的区域，通常包含两个（或在 3D 情况下是三个）**Axis** 对象（请注意 **Axes** 和 **Axis** 的区别），这些 Axis 对象提供刻度线和刻度标签，为坐标系中的数据提供比例尺。每个 **Axes** 还有一个标题（通过 `set_title()` 设置）、一个 x 轴标签（通过 `set_xlabel()` 设置）和一个 y 轴标签（通过 `set_ylabel()` 设置）。

**Axes** 方法是配置图形大部分部分的主要接口（添加数据、控制轴的比例和范围、添加标签等）。

### **Axis**

这些对象设置比例尺、范围，并生成刻度线（轴上的标记）和刻度标签（标注刻度的字符串）。刻度的位置由 `Locator` 对象决定，刻度标签的字符串由 `Formatter` 格式化。正确的 `Locator` 和 `Formatter` 组合可以非常精细地控制刻度的位置和标签。

### **Artist**

基本上，图形上所有可见的东西都是一个 Artist（甚至 **Figure**、**Axes** 和 **Axis** 对象也包括在内）。这包括 `Text` 对象、`Line2D` 对象、`collections` 对象、`Patch` 对象等。当图形被渲染时，所有的 Artist 都被绘制到 **画布（canvas）** 上。大多数 Artist 都绑定在一个坐标系上；这样的 Artist 不能被多个坐标系共享，也不能从一个坐标系移动到另一个。

## 绘图函数的输入类型

绘图函数期望输入为 `numpy.array` 或 `numpy.ma.masked_array`，或者可以传递给 `numpy.asarray` 的对象。类似于数组的类（“array-like”），如 `pandas` 数据对象和 `numpy.matrix`，可能无法按预期工作。通常的约定是在绘图之前将这些对象转换为 `numpy.array`。例如，转换一个 `numpy.matrix`：

```python
b = np.matrix([[1, 2], [3, 4]])
b_asarray = np.asarray(b)
```

大多数方法也会解析像字典、[结构化 numpy 数组](https://numpy.org/doc/stable/user/basics.rec.html#structured-arrays) 或 `pandas.DataFrame` 这样的可字符串索引对象。Matplotlib 允许你提供 `data` 关键字参数，并通过传递与 *x* 和 *y* 变量对应的字符串来生成绘图。

```python
np.random.seed(19680801)  # 设置随机数生成器的种子
data = {'a': np.arange(50),
        'c': np.random.randint(0, 50, 50),
        'd': np.random.randn(50)}
data['b'] = data['a'] + 10 * np.random.randn(50)
data['d'] = np.abs(data['d']) * 100

fig, ax = plt.subplots(figsize=(5, 2.7), layout='constrained')
ax.scatter('a', 'b', c='c', s='d', data=data)
ax.set_xlabel('entry a')
ax.set_ylabel('entry b')
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_002.png)

## 编码风格

### 显式接口与隐式接口

如上所述，使用 Matplotlib 本质上有两种方式：

1.  **显式**创建图形和坐标系，并在它们上面调用方法（“面向对象（OO）风格”）。
2.  **依赖 pyplot 隐式**创建和管理图形和坐标系，并使用 pyplot 函数进行绘图。

关于隐式和显式接口的权衡解释，请参阅 [Matplotlib 应用程序接口（APIs）](https://matplotlib.org/stable/users/explain/figure/api_interfaces.html#api-interfaces)。

因此，可以使用 OO 风格：

```python
x = np.linspace(0, 2, 100)  # 示例数据

# 注意，即使在 OO 风格中，我们也使用 `.pyplot.figure` 来创建图形。
fig, ax = plt.subplots(figsize=(5, 2.7), layout='constrained')
ax.plot(x, x, label='linear')  # 在坐标系上绘制一些数据
ax.plot(x, x**2, label='quadratic')  # 在坐标系上绘制更多数据...
ax.plot(x, x**3, label='cubic')  # ... 以及更多。
ax.set_xlabel('x label')  # 为坐标系添加 x 轴标签
ax.set_ylabel('y label')  # 为坐标系添加 y 轴标签
ax.set_title("Simple Plot")  # 为坐标系添加标题
ax.legend()  # 添加图例
```

![Simple Plot](https://matplotlib.org/stable/_images/sphx_glr_quick_start_003.png)

或 pyplot 风格：

```python
x = np.linspace(0, 2, 100)  # 示例数据

plt.figure(figsize=(5, 2.7), layout='constrained')
plt.plot(x, x, label='linear')  # 在（隐式）坐标系上绘制一些数据
plt.plot(x, x**2, label='quadratic')  # 等等
plt.plot(x, x**3, label='cubic')
plt.xlabel('x label')
plt.ylabel('y label')
plt.title("Simple Plot")
plt.legend()
```

![Simple Plot](https://matplotlib.org/stable/_images/sphx_glr_quick_start_004.png)

（此外，还有一种第三种方法，用于在 GUI 应用程序中嵌入 Matplotlib 的情况，它完全放弃了 pyplot，甚至用于图形创建。更多信息请参阅图库中的相应部分：[在图形用户界面中嵌入 Matplotlib](https://matplotlib.org/stable/gallery/user_interfaces/index.html#user-interfaces)。）

Matplotlib 的文档和示例同时使用了 OO 和 pyplot 风格。总的来说，我们建议使用 OO 风格，特别是对于复杂的图形，以及旨在作为大型项目一部分重复使用的函数和脚本。然而，pyplot 风格对于快速的交互式工作来说非常方便。

**注意**
你可能会发现一些旧的示例使用 `pylab` 接口，通过 `from pylab import *`。我们强烈**不推荐**使用这种方法。

### 创建辅助函数

如果你需要对不同的数据集反复制作相同的图形，或者想轻松地包装 Matplotlib 方法，请使用下面推荐的函数签名。

```python
def my_plotter(ax, data1, data2, param_dict):
    """
    一个辅助函数，用于制作图形
    """
    out = ax.plot(data1, data2, **param_dict)
    return out
```

然后你可以使用它两次来填充两个子图：

```python
data1, data2, data3, data4 = np.random.randn(4, 100)  # 生成 4 个随机数据集
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(5, 2.7))
my_plotter(ax1, data1, data2, {'marker': 'x'})
my_plotter(ax2, data3, data4, {'marker': 'o'})
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_005.png)

注意，如果你想将其安装为一个 Python 包，或者进行任何其他自定义，你可以使用网络上众多的模板；Matplotlib 在 [mpl-cookiecutter](https://github.com/matplotlib/matplotlib-extension-cookiecutter) 提供了一个。

## 设置 Artist 样式

大多数绘图方法都有用于设置 Artist 样式的选项，可以在调用绘图方法时设置，或者通过 Artist 上的“setter”方法设置。在下图中，我们手动设置了 `plot` 创建的 Artist 的 *颜色*、*线宽* 和 *线型*，并在事后用 `set_linestyle` 设置了第二条线的线型。

```python
fig, ax = plt.subplots(figsize=(5, 2.7))
x = np.arange(len(data1))
ax.plot(x, np.cumsum(data1), color='blue', linewidth=3, linestyle='--')
l, = ax.plot(x, np.cumsum(data2), color='orange', linewidth=2)
l.set_linestyle(':')
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_006.png)

### 颜色

Matplotlib 有一套非常灵活的颜色系统，大多数 Artist 都可以接受；有关规范的列表，请参阅 [允许的颜色定义](https://matplotlib.org/stable/users/explain/colors/colors.html#colors-def)。有些 Artist 会接受多种颜色。例如，对于 `scatter` 图，标记的边缘可以与内部是不同的颜色：

```python
fig, ax = plt.subplots(figsize=(5, 2.7))
ax.scatter(data1, data2, s=50, facecolor='C0', edgecolor='k')
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_007.png)

### 线宽、线型和标记大小

线宽通常以印刷点（1 pt = 1/72 英寸）为单位，适用于具有描边线的 Artist。同样，描边线可以具有线型。请参阅 [线型示例](https://matplotlib.org/stable/gallery/lines_bars_and_markers/linestyles.html)。

标记大小取决于使用的方法。`plot` 以点为单位指定 `markersize`，通常是标记的“直径”或宽度。`scatter` 指定的 `markersize` 大致与标记的视觉面积成正比。有一系列可以作为字符串代码使用的标记样式（见 `markers`），或者用户可以定义自己的 `MarkerStyle`（见 [标记参考](https://matplotlib.org/stable/gallery/lines_bars_and_markers/marker_reference.html)）：

```python
fig, ax = plt.subplots(figsize=(5, 2.7))
ax.plot(data1, 'o', label='data1')
ax.plot(data2, 'd', label='data2')
ax.plot(data3, 'v', label='data3')
ax.plot(data4, 's', label='data4')
ax.legend()
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_008.png)

## 标注图形

### 轴标签和文本

`set_xlabel`、`set_ylabel` 和 `set_title` 用于在指定位置添加文本（更多讨论请参阅 [Matplotlib 中的文本](https://matplotlib.org/stable/users/explain/text/text_intro.html#text-intro)）。也可以使用 `text` 直接将文本添加到图形中：

```python
mu, sigma = 115, 15
x = mu + sigma * np.random.randn(10000)
fig, ax = plt.subplots(figsize=(5, 2.7), layout='constrained')
# 数据的直方图
n, bins, patches = ax.hist(x, 50, density=True, facecolor='C0', alpha=0.75)

ax.set_xlabel('Length [cm]')
ax.set_ylabel('Probability')
ax.set_title('Aardvark lengths\n (not really)')
ax.text(75, .025, r'$\mu=115,\ \sigma=15$')
ax.axis([55, 175, 0, 0.03])
ax.grid(True)
```

![Aardvark lengths  (not really)](https://matplotlib.org/stable/_images/sphx_glr_quick_start_009.png)

所有的 `text` 函数都返回一个 `matplotlib.text.Text` 实例。就像上面的线条一样，你可以通过向文本函数传递关键字参数来自定义其属性：

```python
ax.set_xlabel('entry a', fontsize=12, color='red', style='italic')
```

这些属性在 [文本属性和布局](https://matplotlib.org/stable/users/explain/text/text_props.html#text-props) 中有更详细的介绍。

### 在文本中使用数学表达式

Matplotlib 在任何文本表达式中都接受 TeX 公式表达式。例如，要在标题中写表达式 ，你可以写一个用美元符号包围的 TeX 表达式：

```python
ax.set_title(r'$\sigma_i=15$')
```

其中标题字符串前面的 `r` 表示该字符串是*原始*字符串，不要将反斜杠视为 python 转义字符。Matplotlib 内置了一个 TeX 表达式解析器和布局引擎，并附带了自己的数学字体——详情请参阅 [编写数学表达式](https://matplotlib.org/stable/users/explain/text/mathtext.html#mathtext)。你也可以直接使用 LaTeX 格式化文本，并将输出直接合并到你的显示图形或保存的 PostScript 中——请参阅 [使用 LaTeX 渲染文本](https://matplotlib.org/stable/users/explain/text/usetex.html#usetex)。

### 注解

我们还可以在图形上注解点，通常通过连接一个指向 *xy* 的箭头和位于 *xytext* 的一段文本来实现：

```python
fig, ax = plt.subplots(figsize=(5, 2.7))

t = np.arange(0.0, 5.0, 0.01)
s = np.cos(2 * np.pi * t)
line, = ax.plot(t, s, lw=2)

ax.annotate('local max', xy=(2, 1), xytext=(3, 1.5),
            arrowprops=dict(facecolor='black', shrink=0.05))

ax.set_ylim(-2, 2)
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_010.png)

在这个基本示例中，*xy* 和 *xytext* 都使用数据坐标。你可以选择多种其他坐标系——详情请参阅 [基本注解](https://matplotlib.org/stable/users/explain/text/annotations.html#annotations-tutorial) 和 [高级注解](https://matplotlib.org/stable/users/explain/text/annotations.html#plotting-guide-annotation)。更多示例也可以在 [注解图形](https://matplotlib.org/stable/gallery/text_labels_and_annotations/annotation_demo.html) 中找到。

## 坐标轴比例尺和刻度

每个坐标系都有两个（或三个）`Axis` 对象，分别代表 x 轴和 y 轴。它们控制着坐标轴的*比例尺*、刻度*定位器*和刻度*格式化器*。可以附加额外的坐标轴来显示更多的 Axis 对象。

### 比例尺

除了线性比例尺，Matplotlib 还提供了非线性比例尺，例如对数比例尺。由于对数比例尺使用非常广泛，因此也有直接的方法，如 `loglog`、`semilogx` 和 `semilogy`。有多种比例尺可用（其他示例请参阅 [比例尺概览](https://matplotlib.org/stable/gallery/scales/scales.html)）。这里我们手动设置比例尺：

```python
fig, axs = plt.subplots(1, 2, figsize=(5, 2.7), layout='constrained')
xdata = np.arange(len(data1))  # 为示例数据生成 x 值
data = 10**data1
axs[0].plot(xdata, data)
axs[0].set_title('Normal')
axs[0].set_yscale('log')
axs[0].set_title('Log scaled')
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_012.png)

比例尺设置了从数据值到轴上间距的映射。这个过程是双向的，并组合成一个*变换*，这是 Matplotlib 从数据坐标映射到坐标系、图形或屏幕坐标的方式。请参阅 [变换教程](https://matplotlib.org/stable/users/explain/artists/transforms_tutorial.html#transforms-tutorial)。

### 刻度定位器和格式化器

每个坐标轴都有一个刻度*定位器*和*格式化器*，用于选择在轴对象上放置刻度线的位置。一个简单的接口是 `set_xticks`：

```python
fig, axs = plt.subplots(2, 1, layout='constrained')
axs[0].plot(xdata, data1)
axs[0].set_title('Automatic ticks')

axs[1].plot(xdata, data1)
axs[1].set_xticks(np.arange(0, 100, 30), ['zero', '30', 'sixty', '90'])
axs[1].set_yticks([-1.5, 0, 1.5])  # 注意我们不需要指定标签
axs[1].set_title('Manual ticks')
```

![Automatic ticks, Manual ticks](https://matplotlib.org/stable/_images/sphx_glr_quick_start_013.png)

不同的比例尺可以有不同的定位器和格式化器；例如上面的对数比例尺使用了 `LogLocator` 和 `LogFormatter`。其他格式化器和定位器以及编写自定义格式化器和定位器的信息，请参阅 [刻度定位器](https://matplotlib.org/stable/gallery/ticks/tick-locators.html) 和 [刻度格式化器](https://matplotlib.org/stable/gallery/ticks/tick-formatters.html)。

### 绘制日期和字符串

Matplotlib 可以处理日期数组、字符串数组以及浮点数的绘图。这些数据会使用适当的特殊定位器和格式化器。对于日期：

```python
fig, ax = plt.subplots(figsize=(5, 2.7), layout='constrained')
dates = np.arange(np.datetime64('2021-11-15'), np.datetime64('2021-12-25'),
                  np.timedelta64(1, 'h'))
data = np.cumsum(np.random.randn(len(dates)))
ax.plot(dates, data)
cdf = mpl.dates.ConciseDateFormatter(ax.xaxis.get_major_locator())
ax.xaxis.set_major_formatter(cdf)
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_014.png)

更多信息请参阅日期示例（例如 [日期刻度标签](https://matplotlib.org/stable/gallery/text_labels_and_annotations/date.html)）。

对于字符串，我们得到分类绘图（参见：[绘制分类变量](https://matplotlib.org/stable/gallery/lines_bars_and_markers/categorical_variables.html)）。

```python
fig, ax = plt.subplots(figsize=(5, 2.7), layout='constrained')
categories = ['turnips', 'rutabaga', 'cucumber', 'pumpkins']
ax.bar(categories, np.random.rand(len(categories)))
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_015.png)

关于分类绘图的一个注意事项是：某些解析文本文件的方法会返回一个字符串列表，即使这些字符串都代表数字或日期。如果你传递 1000 个字符串，Matplotlib 会认为你指的是 1000 个类别，并会在你的图形中添加 1000 个刻度！

### 附加的坐标轴对象

在一个图表中绘制不同数量级的数据可能需要一个额外的 y 轴。这样的轴可以通过使用 `twinx` 来创建一个新的坐标系，该坐标系具有不可见的 x 轴和一个位于右侧的 y 轴（类似地，`twiny` 用于创建位于顶部的 x 轴）。另一个例子请参阅 [具有不同比例尺的图形](https://matplotlib.org/stable/gallery/subplots_axes_and_figures/two_scales.html)。

类似地，你可以添加一个 `secondary_xaxis` 或 `secondary_yaxis`，使其具有与主坐标轴不同的比例尺，以便以不同的比例尺或单位表示数据。更多示例请参阅 [次坐标轴](https://matplotlib.org/stable/gallery/subplots_axes_and_figures/secondary_axis.html)。

```python
fig, (ax1, ax3) = plt.subplots(1, 2, figsize=(7, 2.7), layout='constrained')
l1, = ax1.plot(t, s)
ax2 = ax1.twinx()
l2, = ax2.plot(t, range(len(t)), 'C1')
ax2.legend([l1, l2], ['Sine (left)', 'Straight (right)'])

ax3.plot(t, s)
ax3.set_xlabel('Angle [rad]')
ax4 = ax3.secondary_xaxis('top', (np.rad2deg, np.deg2rad))
ax4.set_xlabel('Angle [°]')
```

![quick start](https://matplotlib.org/stable/_images/sphx_glr_quick_start_016.png)

## 颜色映射数据

通常我们希望绘图的第三维由颜色映射中的颜色表示。Matplotlib 有许多可以做到这一点的绘图类型：

```python
from matplotlib.colors import LogNorm

X, Y = np.meshgrid(np.linspace(-3, 3, 128), np.linspace(-3, 3, 128))
Z = (1 - X/2 + X**5 + Y**3) * np.exp(-X**2 - Y**2)

fig, axs = plt.subplots(2, 2, layout='constrained')
pc = axs[0, 0].pcolormesh(X, Y, Z, vmin=-1, vmax=1, cmap='RdBu_r')
fig.colorbar(pc, ax=axs[0, 0])
axs[0, 0].set_title('pcolormesh()')

co = axs[0, 1].contourf(X, Y, Z, levels=np.linspace(-1.25, 1.25, 11))
fig.colorbar(co, ax=axs[0, 1])
axs[0, 1].set_title('contourf()')

pc = axs[1, 0].imshow(Z**2 * 100, cmap='plasma', norm=LogNorm(vmin=0.01, vmax=100))
fig.colorbar(pc, ax=axs[1, 0], extend='both')
axs[1, 0].set_title('imshow() with LogNorm()')

pc = axs[1, 1].scatter(data1, data2, c=data3, cmap='RdBu_r')
fig.colorbar(pc, ax=axs[1, 1], extend='both')
axs[1, 1].set_title('scatter()')
```

![pcolormesh(), contourf(), imshow() with LogNorm(), scatter()](https://matplotlib.org/stable/_images/sphx_glr_quick_start_017.png)

### 颜色映射

这些都是从 `ScalarMappable` 对象派生的 Artist 的示例。它们都可以设置从 *vmin* 到 *vmax* 到 *cmap* 指定的颜色映射的线性映射。Matplotlib 提供了许多颜色映射可供选择（[在 Matplotlib 中选择颜色映射](https://matplotlib.org/stable/users/explain/colors/colormaps.html#colormaps)），你可以制作自己的颜色映射（[在 Matplotlib 中创建颜色映射](https://matplotlib.org/stable/users/explain/colors/colormap-manipulation.html#colormap-manipulation)）或下载为[第三方包](https://matplotlib.org/mpl-third-party/#colormaps-and-styles)。

### 归一化

有时我们希望数据到颜色映射的映射是非线性的，就像上面的 `LogNorm` 示例一样。我们通过向 ScalarMappable 提供 *norm* 参数而不是 *vmin* 和 *vmax* 来实现这一点。更多归一化示例请参阅 [颜色映射归一化](https://matplotlib.org/stable/users/explain/colors/colormapnorms.html#colormapnorms)。

### 颜色条

添加 `colorbar` 提供了一个将颜色映射回基础数据的关键。颜色条是图形级别的 Artist，它们附着在 ScalarMappable 上（从那里获取关于归一化和颜色映射的信息），并且通常从父坐标系中“偷取”空间。颜色条的放置可能很复杂：详情请参阅 [放置颜色条](https://matplotlib.org/stable/users/explain/axes/colorbar_placement.html#colorbar-placement)。你还可以使用 *extend* 关键字在颜色条两端添加箭头，并使用 *shrink* 和 *aspect* 来控制大小，从而更改颜色条的外观。最后，颜色条将具有适合归一化的默认定位器和格式化器。这些可以像其他坐标轴对象一样进行更改。

## 处理多个图形和坐标系

你可以通过多次调用 `fig = plt.figure()` 或 `fig2, ax = plt.subplots()` 来打开多个图形。通过保留对象引用，你可以向任意图形添加 Artist。

可以通过多种方式添加多个坐标系，但最基本的是如上所述的 `plt.subplots()`。使用 `subplot_mosaic` 可以实现更复杂的布局，其中坐标系对象可以跨列或跨行。

```python
fig, axd = plt.subplot_mosaic([['upleft', 'right'],
                               ['lowleft', 'right']], layout='constrained')
axd['upleft'].set_title('upleft')
axd['lowleft'].set_title('lowleft')
axd['right'].set_title('right')
```

![upleft, right, lowleft](https://matplotlib.org/stable/_images/sphx_glr_quick_start_018.png)

Matplotlib 有相当复杂的工具来排列坐标系：请参阅 [在图形中排列多个坐标系](https://matplotlib.org/stable/users/explain/axes/arranging_axes.html#arranging-axes) 和 [复杂和语义化的图形组合（subplot_mosaic）](https://matplotlib.org/stable/users/explain/axes/mosaic.html#mosaic)。
