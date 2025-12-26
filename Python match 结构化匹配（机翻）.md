# **结构模式匹配**

原文链接：[PEP 636 – Structural Pattern Matching: Tutorial ](https://peps.python.org/pep-0636/)

## **摘要**

本 PEP 是 :pep:`634` 引入的模式匹配的教程。

:pep:`622` 提出了模式匹配的语法，该提案在社区和指导委员会中引发了详细讨论。一个常见的担忧是如何解释（和学习）这个功能。本 PEP 通过提供开发者可以用来学习 Python 中模式匹配的文档来解决这个担忧。

这被视为 :pep:`634`（模式匹配的技术规范）和 :pep:`635`（拥有模式匹配的动机、原理和设计考虑）的支持材料。

对于寻求快速回顾而非教程的读者，请参阅 `附录 A <PEP 636 Appendix A_>`_。

## **教程**

作为本教程的动机示例，你将编写一个文字冒险游戏。这是一种互动小说形式，用户输入文字命令与虚构世界互动，并接收对发生事件的文字描述。命令将是自然语言的简化形式，如 `get sword`、`attack dragon`、`go north`、`enter shop` 或 `buy cheese`。

### **匹配序列**

你的主循环需要从用户那里获取输入并将其拆分为单词，假设是一个字符串列表，如下所示：:

```python
command = input("What are you doing next? ")
# analyze the result of command.split()
```



下一步是解释这些单词。我们的大多数命令将有两个单词：一个动作和一个对象。因此你可能会尝试这样做：:

```
[action, obj] = command.split()
... # interpret action, obj
```

那行代码的问题是它遗漏了一些东西：如果用户输入超过或少于 2 个单词怎么办？为了防止这个问题，你可以检查单词列表的长度，或者捕获上面语句会引发的 `ValueError`。

你可以改用匹配语句：:

```
match command.split():
    case [action, obj]:
        ... # interpret action, obj
```



匹配语句评估 **"主体"**（`match` 关键字后的值），并根据 **模式**（`case` 旁边的代码）进行检查。模式能够做两件不同的事情：

* 验证主体是否具有某种结构。在你的例子中，`[action, obj]` 模式匹配任何恰好有两个元素的序列。这被称为 **匹配**
* 它将绑定模式中的一些名称到主体的组件元素。在这种情况下，如果列表有两个元素，它将绑定 `action = subject[0]` 和 `obj = subject[1]`。

如果匹配成功，将执行 case 块内的语句，并可使用绑定的变量。如果没有匹配，则什么也不会发生，并继续执行 `match` 后的语句。

请注意，与解包赋值类似，你可以使用括号、方括号或仅用逗号分隔作为同义词。因此你可以写 `case action, obj` 或 `case (action, obj)`，含义相同。所有形式都将匹配任何序列（例如列表或元组）。

### **匹配多个模式**

即使大多数命令具有动作/对象形式，你可能也希望有不同长度的用户命令。例如，你可能希望添加没有对象的单个动词，如 `look` 或 `quit`。一个匹配语句可以（并且很可能）有多个 `case`：:

```
match command.split():
    case [action]:
        ... # interpret single-verb action
    case [action, obj]:
        ... # interpret action, obj
```



匹配语句将从上到下检查模式。如果模式与主体不匹配，将尝试下一个模式。然而，一旦找到 *第一个* 匹配的模式，该 case 的主体就会被执行，所有后续的 case 都会被忽略。这类似于 `if/elif/elif/...` 语句的工作方式。

### **匹配特定值**

你的代码仍然需要查看特定的动作，并根据特定动作（例如 `quit`、`attack` 或 `buy`）有条件地执行不同的逻辑。你可以使用一连串的 `if/elif/elif/...` 或使用函数字典来完成这个任务，但在这里我们将利用模式匹配来解决这个任务。你可以使用模式中的字面值（如 `"quit"`、`42` 或 `None`）来代替变量。这允许你编写：:

```
match command.split():
    case ["quit"]:
        print("Goodbye!")
        quit_game()
    case ["look"]:
        current_room.describe()
    case ["get", obj]:
        character.get(obj, current_room)
    case ["go", direction]:
        current_room = current_room.neighbor(direction)
    # The rest of your commands go here
```



像 `["get", obj]` 这样的模式将仅匹配第一个元素等于 `"get"` 的 2 元素序列。它还将绑定 `obj = subject[1]`。

如你在 `go` case 中所见，我们也可以在不同的模式中使用不同的变量名。

字面值使用 `==` 运算符进行比较，除了常量 `True`、`False` 和 `None`，它们使用 `is` 运算符进行比较。

### **匹配多个值**

玩家可能能够通过一系列命令 `drop key`、`drop sword`、`drop cheese` 来丢弃多个物品。这个界面可能很繁琐，你可能希望允许在单个命令中丢弃多个物品，如 `drop key sword cheese`。在这种情况下，你事先不知道命令中有多少个单词，但你可以像在赋值中允许的那样，在模式中使用扩展解包：:

```
match command.split():
    case ["drop", *objects]:
        for obj in objects:
            character.drop(obj, current_room)
    # The rest of your commands go here
```



这将匹配任何第一个元素为 "drop" 的序列。所有剩余的元素将被捕获在一个 `list` 对象中，该对象将被绑定到 `objects` 变量。

这个语法与序列解包有类似的限制：你不能在一个模式中有超过一个星号名称。

### **添加通配符**

当所有模式都失败时，你可能希望打印一条错误消息，说明命令未被识别。你可以使用我们刚刚学到的功能，将 `case [*ignored_words]` 作为你的最后一个模式。然而，有一个更简单的方法：:

```
match command.split():
    case ["quit"]: ... # Code omitted for brevity
    case ["go", direction]: ...
    case ["drop", *objects]: ...
    ... # Other cases
    case _:
        print(f"Sorry, I couldn't understand {command!r}")
```



这种特殊模式写为 `_`（称为通配符）总是匹配，但不绑定任何变量。

请注意，这将匹配任何对象，而不仅仅是序列。因此，仅将其单独作为最后一个模式才有意义（为了防止错误，Python 会阻止你在之前使用它）。

### **组合模式**

这是从示例中退一步，理解你一直在使用的模式是如何构建的好时机。模式可以相互嵌套，我们在上面的示例中已经隐式地做到了这一点。

我们见过一些"简单"模式（这里的"简单"意味着它们不包含其他模式）：

* **捕获模式**（独立名称，如 `direction`、`action`、`objects`）。我们从未单独讨论过这些，但将它们用作其他模式的一部分。
* **字面模式**（字符串字面量、数字字面量、`True`、`False` 和 `None`）
* **通配符模式** `_`

到目前为止，我们试验过的唯一非简单模式是序列模式。序列模式中的每个元素实际上可以是任何其他模式。这意味着你可以编写像 `["first", (left, right), _, *rest]` 这样的模式。这将匹配那些至少有三个元素的主体，其中第一个等于 `"first"`，第二个又是一个两个元素的序列。它还将绑定 `left=subject[1][0]`、`right=subject[1][1]` 和 `rest = subject[3:]`。

### **或模式**

回到冒险游戏示例，你可能会发现你希望有几个模式产生相同的结果。例如，你可能希望命令 `north` 和 `go north` 是等价的。你可能还希望为任何 X 设置 `get X`、`pick up X` 和 `pick X up` 的别名。

模式中的 `|` 符号将它们组合为替代方案。例如，你可以写：:

```
match command.split():
    ... # Other cases
    case ["north"] | ["go", "north"]:
        current_room = current_room.neighbor("north")
    case ["get", obj] | ["pick", "up", obj] | ["pick", obj, "up"]:
        ... # Code for picking up the given object
```



这被称为 **或模式**，将产生预期结果。模式从左到右尝试；如果多个替代方案匹配，这可能与知道绑定什么有关。编写或模式时的一个重要限制是所有替代方案应绑定相同的变量。因此模式 `[1, x] | [2, y]` 是不允许的，因为它会使成功匹配后不清楚会绑定哪个变量。`[1, x] | [2, x]` 是完全可以的，并且如果成功将始终绑定 `x`。

### **捕获匹配的子模式**

我们"go"命令的第一个版本是用 `["go", direction]` 模式编写的。我们在上一个版本中使用模式 `["north"] | ["go", "north"]` 所做的更改有一些好处，但也有一些缺点：最新版本允许别名，但也有硬编码的方向，这将迫使我们实际上为 north/south/east/west 设置单独的模式。这导致了一些代码重复，但与此同时我们获得了更好的输入验证，如果用户输入的命令是 `"go figure!"` 而不是一个方向，我们将不会进入那个分支。

我们可以尝试通过以下方式获得两全其美的效果（为了简洁，我省略了没有 "go" 的别名版本）：:

```
match command.split():
    case ["go", ("north" | "south" | "east" | "west")]:
        current_room = current_room.neighbor(...)
        # how do I know which direction to go?
```



这段代码是单个分支，它验证 "go" 后面的单词确实是一个方向。但是移动玩家的代码需要知道选择了哪个方向，并且没有办法做到这一点。我们需要的是一个行为像或模式但同时进行捕获的模式。我们可以使用 **as 模式** 来做到这一点：:

```
match command.split():
    case ["go", ("north" | "south" | "east" | "west") as direction]:
        current_room = current_room.neighbor(direction)
```



as 模式匹配其左侧的任何模式，但也将该值绑定到一个名称。

### **向模式添加条件**

我们上面探索的模式可以进行一些强大的数据过滤，但有时你可能希望拥有布尔表达式的全部功能。假设你实际上希望仅基于当前房间可能的出口，在有限的方向集合中允许 "go" 命令。我们可以通过向我们的 case 添加 **守卫** 来实现这一点。守卫由 `if` 关键字后跟任何表达式组成：:

```
match command.split():
    case ["go", direction] if direction in current_room.exits:
        current_room = current_room.neighbor(direction)
    case ["go", _]:
        print("Sorry, you can't go that way")
```



守卫不是模式的一部分，它是 case 的一部分。仅在模式匹配时检查，并且在所有模式变量都已绑定之后（这就是为什么条件可以在上面的示例中使用 `direction` 变量）。如果模式匹配且条件为真值，则 case 的主体正常执行。如果模式匹配但条件为假值，则匹配语句继续检查下一个 case，就像模式没有匹配一样（可能产生已经绑定了一些变量的副作用）。

### **添加 UI：匹配对象**

你的冒险游戏正在取得成功，你被要求实现一个图形界面。你选择的 UI 工具包允许你编写一个事件循环，你可以通过调用 `event.get()` 获取一个新的事件对象。生成的对象根据用户操作可以具有不同的类型和属性，例如：

* 当用户按下按键时生成 `KeyPress` 对象。它有一个 `key_name` 属性，其中包含按下的键的名称，以及一些关于修饰键的其他属性。
* 当用户点击鼠标时生成 `Click` 对象。它有一个 `position` 属性，包含指针的坐标。
* 当用户点击游戏窗口的关闭按钮时生成 `Quit` 对象。

与其编写多个 `isinstance()` 检查，你可以使用模式来识别不同类型的对象，并将模式应用于其属性：:

    match event.get():
        case Click(position=(x, y)):
            handle_click_at(x, y)
        case KeyPress(key_name="Q") | Quit():
            game.quit()
        case KeyPress(key_name="up arrow"):
            game.go_north()
        ...
        case KeyPress():
            pass # Ignore other keystrokes
        case other_event:
            raise ValueError(f"Unrecognized event: {other_event}")

像 `Click(position=(x, y))` 这样的模式仅匹配事件的类型是 `Click` 类的子类。它还将要求事件具有与 `(x, y)` 模式匹配的 `position` 属性。如果有匹配，局部变量 `x` 和 `y` 将获得预期的值。

像 `KeyPress()` 这样的模式，没有参数，将匹配任何是 `KeyPress` 类实例的对象。只有你在模式中指定的属性被匹配，任何其他属性都被忽略。

### **匹配位置属性**

上一节描述了在进行对象匹配时如何匹配命名属性。对于某些对象，通过位置描述匹配的参数可能更方便（特别是如果只有几个属性并且它们具有"标准"顺序）。如果你使用的类是命名元组或数据类，你可以按照构造对象时使用的相同顺序来做到这一点。例如，如果上面的 UI 框架这样定义它们的类：:

    from dataclasses import dataclass
    
    @dataclass
    class Click:
        position: tuple
        button: Button

那么你可以将上面的匹配语句重写为：:

    match event.get():
        case Click((x, y)):
            handle_click_at(x, y)

`(x, y)` 模式将自动与 `position` 属性匹配，因为模式中的第一个参数对应于数据类定义中的第一个属性。

其他类没有其属性的自然排序，因此你需要在模式中使用显式名称来匹配它们的属性。然而，可以手动指定属性的顺序以允许位置匹配，就像在这个替代定义中：:

    class Click:
        __match_args__ = ("position", "button")
        def __init__(self, pos, btn):
            self.position = pos
            self.button = btn
            ...

特殊属性 `__match_args__` 定义了属性的显式顺序，可以在像 `case Click((x,y))` 这样的模式中使用。

### **匹配常量和枚举**

你上面的模式将所有鼠标按钮视为相同，你决定要接受左键单击，并忽略其他按钮。在执行此操作时，你注意到 `button` 属性的类型是 `Button`，它是使用 `enum.Enum` 构建的枚举。你实际上可以像这样匹配枚举值：:

    match event.get():
        case Click((x, y), button=Button.LEFT):  # 这是一个左键单击
            handle_click_at(x, y)
        case Click():
            pass  # 忽略其他单击

这将适用于任何带点的名称（如 `math.pi`）。然而，一个非限定名称（即没有点的纯名称）将始终被解释为捕获模式，因此请始终在模式中使用限定常量以避免这种歧义。

### **迈向云端：映射**

你决定制作游戏的在线版本。所有逻辑将位于服务器中，UI 位于客户端，客户端将使用 JSON 消息进行通信。通过 `json` 模块，这些消息将被映射到 Python 字典、列表和其他内置对象。

我们的客户端将接收一个要执行的动作字典列表（从 JSON 解析而来），每个元素看起来像这样：

* `{"text": "店主说'啊！我们有卡门贝尔奶酪，是的先生'", "color": "blue"}`
* 如果客户端应该暂停 `{"sleep": 3}`
* 播放声音 `{"sound": "filename.ogg", "format": "ogg"}`

到目前为止，我们的模式已经处理了序列，但是有基于其存在键来匹配映射的模式。在这种情况下，你可以使用：:

    for action in actions:
        match action:
            case {"text": message, "color": c}:
                ui.set_text_color(c)
                ui.display(message)
            case {"sleep": duration}:
                ui.wait(duration)
            case {"sound": url, "format": "ogg"}:
                ui.play(url)
            case {"sound": _, "format": _}:
                warning("不支持的音频格式")

映射模式中的键需要是字面量，但值可以是任何模式。与序列模式一样，所有子模式都必须匹配，整个模式才能匹配。

你可以在映射模式中使用 `**rest` 来捕获主体中的额外键。请注意，如果你省略这一点，主体中的额外键在匹配时将被忽略，即消息 `{"text": "foo", "color": "red", "style": "bold"}` 将匹配上面示例中的第一个模式。

### **匹配内置类**

上面的代码可以进行一些验证。由于消息来自外部源，字段的类型可能错误，导致错误或安全问题。

任何类都是有效的匹配目标，这包括像 `bool`、`str` 或 `int` 这样的内置类。这允许我们将上面的代码与类模式结合起来。所以不用写 `{"text": message, "color": c}`，我们可以使用 `{"text": str() as message, "color": str() as c}` 来确保 `message` 和 `c` 都是字符串。对于许多内置类（有关完整列表，请参阅 :pep:`634`），你可以使用位置参数作为简写，写 `str(c)` 而不是 `str() as c`。完全重写的版本如下所示：:

    for action in actions:
        match action:
            case {"text": str(message), "color": str(c)}:
                ui.set_text_color(c)
                ui.display(message)
            case {"sleep": float(duration)}:
                ui.wait(duration)
            case {"sound": str(url), "format": "ogg"}:
                ui.play(url)
            case {"sound": _, "format": _}:
                warning("不支持的音频格式")

## **附录 A -- 快速介绍**

match 语句接受一个表达式，并将其值与一个或多个 case 块中给出的连续模式进行比较。这在表面上类似于 C、Java 或 JavaScript（以及许多其他语言）中的 switch 语句，但功能更强大。

最简单的形式将主体值与一个或多个字面量进行比较：:

    def http_error(status):
        match status:
            case 400:
                return "错误的请求"
            case 404:
                return "未找到"
            case 418:
                return "我是一个茶壶"
            case _:
                return "互联网出问题了"

注意最后一个块："变量名" `_` 充当 *通配符*，永远不会匹配失败。

你可以使用 `|`（"或"）将几个字面量组合在一个模式中：:

            case 401 | 403 | 404:
                return "不允许"

模式可以看起来像解包赋值，并且可以用来绑定变量：:

    # point 是一个 (x, y) 元组
    match point:
        case (0, 0):
            print("原点")
        case (0, y):
            print(f"Y={y}")
        case (x, 0):
            print(f"X={x}")
        case (x, y):
            print(f"X={x}, Y={y}")
        case _:
            raise ValueError("不是一个点")

仔细研究这个例子！第一个模式有两个字面量，可以被认为是上面显示的字面量模式的扩展。但接下来的两个模式结合了一个字面量和一个变量，并且该变量 *绑定* 了主体（`point`）中的一个值。第四个模式捕获两个值，这使其在概念上类似于解包赋值 `(x, y) = point`。

如果你使用类来组织数据，你可以使用类名后跟类似于构造函数的参数列表，但能够将属性捕获到变量中：:

    from dataclasses import dataclass
    
    @dataclass
    class Point:
        x: int
        y: int
    
    def where_is(point):
        match point:
            case Point(x=0, y=0):
                print("原点")
            case Point(x=0, y=y):
                print(f"Y={y}")
            case Point(x=x, y=0):
                print(f"X={x}")
            case Point():
                print("在别处")
            case _:
                print("不是一个点")

你可以对某些为其属性提供排序的内置类（例如数据类）使用位置参数。你也可以通过在类中设置特殊属性 `__match_args__` 来定义模式中属性的特定位置。如果将其设置为 ("x", "y")，以下模式都是等价的（并且都将 `y` 属性绑定到 `var` 变量）：:

    Point(1, var)
    Point(1, y=var)
    Point(x=1, y=var)
    Point(y=var, x=1)

模式可以任意嵌套。例如，如果我们有一个简短的点列表，我们可以像这样匹配它：:

    match points:
        case []:
            print("没有点")
        case [Point(0, 0)]:
            print("原点")
        case [Point(x, y)]:
            print(f"单点 {x}, {y}")
        case [Point(0, y1), Point(0, y2)]:
            print(f"Y 轴上的两点 {y1}, {y2}")
        case _:
            print("其他东西")

我们可以向模式添加一个 `if` 子句，称为"守卫"。如果守卫为假，`match` 会继续尝试下一个 case 块。请注意，值捕获发生在守卫求值之前：:

    match point:
        case Point(x, y) if x == y:
            print(f"Y=X 于 {x}")
        case Point(x, y):
            print(f"不在对角线上")

其他几个关键特性：

- 与解包赋值类似，元组和列表模式具有完全相同的含义，并且实际上匹配任意序列。一个重要的例外是它们不匹配迭代器或字符串。（从技术上讲，主体必须是 `collections.abc.Sequence` 的实例。）

- 序列模式支持通配符：`[x, y, *rest]` 和 `(x, y, *rest)` 的工作方式类似于解包赋值中的通配符。`*` 后面的名称也可以是 `_`，因此 `(x, y, *_)` 匹配至少两个项目的序列而不绑定剩余项目。

- 映射模式：`{"bandwidth": b, "latency": l}` 从字典中捕获 `"bandwidth"` 和 `"latency"` 值。与序列模式不同，额外的键被忽略。也支持通配符 `**rest`。（但 `**_` 将是多余的，因此不允许。）

- 可以使用 `as` 关键字捕获子模式：:

      case (Point(x1, y1), Point(x2, y2) as p2): ...

- 大多数字面量通过相等性进行比较，但是单例 `True`、`False` 和 `None` 通过身份进行比较。

- 模式可以使用命名常量。这些必须是带点的名称，以防止它们被解释为捕获变量：:

      from enum import Enum
      class Color(Enum):
          RED = 0
          GREEN = 1
          BLUE = 2
      
      match color:
          case Color.RED:
              print("我看到了红色！")
          case Color.GREEN:
              print("草是绿色的")
          case Color.BLUE:
              print("我感到忧郁 :(")
