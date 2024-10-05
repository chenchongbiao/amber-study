# 介绍

记录下 Amber 编程语言学习，Amber 通过编译器将源代码转化为 Bash 可执行代码，可以利用已有的 Bash 环境运行Amber程序。既有高级编程语言的编程体验，也能兼容 Bash 的便捷，其他编程语言虽然也可以调用 Shell，但是需要花费更多的行数，并且调用命令时中间的输出也会被吞掉，调试 Shell 并不方便。终于可以用 LSP 调试代码了，Shell 项目过大时定位变量和函数及不方便，有时候甚至不知道变量从何引入，并且为了方便项目管理和调试，又添加了很大一部分代码，极大的不方便开发使用。用 Amber 重构 Shell 项目。

Amber 项目使用 Rust 开发（Rust 真是一直在重构的路上）

# 安装

* 系统需要 Bash 环境
* 基本计算器 bc 命令 `sudo apt install bc`
* 用于下载安装脚本的 Curl 工具

```bash
bash <(curl -s "https://raw.githubusercontent.com/amber-lang/amber/master/setup/install.sh")
```

## 插件

可以在 VScode 中安装 Amber Language 插件

# 用法

## 命令行界面

### help

`--help` 查看帮助信息

### 文档生成

`--docs` 选项从 Amber 文件中提取前缀为  （三斜杠） 的注释，并生成用于文档的 Markdown 文件。

### 禁用格式设置

`--disable-format` 选项可防止在 Bash 编译过程后执行外部格式化工具。

### 缩小代码

--minify 选项会压缩生成的 Bash 代码以减小其大小。

### 运行脚本

```bash
amber file.ab
```

如果只是运行简单的表达式

```bash
amber -e 'echo upper("hello world!")'
```

### 编译

```bash
amber input.ab output.sh
```

## 基础语法

### 数据类型

Amber 支持 5 种数据类型：

* Text 文本数据类型。在其他编程语言中，它也可以称为 “string”。
* Number - 数值数据类型。它基本上是任何数字。
* Bool - 布尔数据类型。它的值可以是 true 或 false。
* Null - nothing 数据类型。
* [] - 数组数据类型。

#### Text

Text 数据类型是 Amber 中最基本的数据类型。它只是一串字符，并且在后台也存储为一串字符。

```bash
// `Text` literal:
"Welcome to the jungle"
```

#### Number

在后台，它的值存储为字符串 - 与在 Bash 中完成的方式相同。区别在于 Amber 应用标准命令对支持浮点值的数字执行操作，以便您可以简单地编写运算符 sign。

```bash
// `Num` data type
// Can be an integer
42
// or a floating point
-123.456
```

#### Boolean

```bash
// `Bool` data type
true
false
```

在后台，这些值存储为 1 或 0。

#### Null

```bash
// `Null` data type
null
```

此数据类型最常见的用途是当您想要声明某个函数不返回任何值时。没有真实世界的例子为什么有人可能想要 null 字面量，因为它现在没有任何用处。

#### Array

在处理数组时，您可能希望定义要创建数组的数据类型。在这种情况下，只需将数据类型放在方括号内即可。例如，Num 类型的数组是 [Num]（在 C 语言中，它是 Num[]）

```bash
// `[Num]` data type
[1, 2, 3]
```

如果要创建一个空数组，则必须指定要包含的数据类型。为此，只需将类型传递给数组内部，就好像它是一个值一样。

```bash
[Text]
```

由于 bash 的限制，很难实现 2D+ 数组以使其表现为常规数组。截至目前，Amber 不支持嵌套数组

### 变量

创建变量使用 let 关键字。下面是一个示例：

```bash
let name = "John"
```

上面的示例展示了如何初始化变量。如果已经创建了所需的，则可以仅按名称重新分配它（不使用任何关键字）

```bash
name = "Rob"
```

要访问此变量存储的值 - 只需按名称引用它，如下所示：

```bash
echo name // Outputs: "Rob"

```

#### 覆盖变量类型

```bash
// `result` is a `Num`
let result = 123
// `result` is a `Text`
let result = "Hello my friend"
```

#### 保留前缀

Amber 编译器保留所有以双下划线 __ 开头的标识符以及 let、if 等关键字。

### 表达式

#### 加法运算符+

可以对 number、text 和 array 执行加法操作。此运算符应用于不同的数据类型会产生不同的结果：

* Num - 算术总和
* Text - 字符串连接
* [] - 数组联接

```bash
12 + 42 // 54
"Hello " + "World!" // "Hello World!"
[1, 2] + [3, 4] // [1, 2, 3, 4]
```

#### 算术运算

算术运算只能用于 Num 数据类型。以下是所有可用选项的列表：

* +算术总和
* -减法
* *乘法
* /除法
* %模运算

```bash
((12 + 34) * 9) % 4
```

```bash
let value = 12
echo -value // Outputs: -12
```

#### 比较运算

相等 == 和不等 ！= 操作可以应用于任何数据类型，只要双方的类型相同即可。

```bash
"foo" != "bar"
42 == 42
true != false
"equal" == "equal"
```

其余比较操作只能用于 Num 数据类型。这些基本上与其他现代编程语言相同：>、<、>=、<=。

#### 逻辑运算

逻辑运算只能用于 Bool 数据类型。与类似 C 的编程语言系列相反，我们选择采用更多的 Pythonic 方法，使用文字名称而不是符号，因为它更适合脚本编程语言的性质：and、or、not。

```bash
18 >= 12 and not false
```

#### 速记运算符

可以使用加法运算符以及任何与 = 符号组合的算术运算符来自动将结果应用于现有变量：

```bash
let age = 18
age += 5
echo age // Outputs: 23

```

#### 文本插值

将值嵌入到文本中：

```bash
let name = "John"
let age = 18
echo "Hi, I'm {name}. I'm {age} years old."
// Outputs: Hi, I'm John. I'm 18 years old
```

### 条件判断

有三种方法可以执行条件逻辑：

* If 语句 - 这是可以在任何地方使用的常规 if 语句
* If Chain - 这是讨厌的 if-else 链接在一起的语法糖。
* Ternary Expression - 这是一种在表达式中表示条件逻辑的方法。

#### If 语句

```bash
if age >= 16 {
    echo "Welcome"
}

```

将 else 分支添加到组合中

```bash
if age >= 16 {
    echo "Welcome"
} else {
    echo "Entry not allowed"
}
```

这个简短的 “if” 条件占用了大量空间。在 Amber 中，有一条规则允许您使用 ： 符号，您只打算编写一个语句，只要您可以编写代码块。当您想用一个语句执行多个条件时，这可能很方便。

```bash
if age >= 16: echo "Welcome"
else: echo "Entry not allowed"

// Or

if age >= 16:
    echo "Welcome"
else:
    echo "Entry not allowed"
```

#### if-chain

if-chain 是一系列 if-else 块的简化技术。而不是像其他编程语言使用嵌套 if-else。

```bash
if {
    drink == "water" {
        echo "Have a natural, mineralized water"
    }
    drink == "cola" {
        echo "Here is your fresh cola"
    }
    else {
        echo "Sorry, we have none of that"
    }
}

// Alternatively, as previously mentioned:

if {
    drink == "water": echo "Have a natural, mineralized water"
    drink == "cola": echo "Here is your fresh cola"
    else: echo "Sorry, we have none of that"
}
```

#### 三元表达式

需要根据一个简单的条件快速确定要分配的值。这正是三元表达式被证明无价的地方。下面是一个简洁的示例：

```bash
let candy = count > 1
    then "candies"
    else "candy"

echo "I have {count} {candy}"
```

当里面的表达式很短时，当然可以内联 tritrinary 表达式。

```bash
let candy = count > 1 then "candies" else "candy"
```

### 命令

访问 bash shell 的唯一方法是通过 Amber 的命令。命令可以以语句或表达式的形式使用。

关于命令，重要的是它们可能会失败。失败是一个新概念，它强制调用方处理失败。处理失败的方法有很多：

* failed - 处理失败的推荐方法，使您能够编写一些特定逻辑以在命令失败时运行
* ？- 此速记，用于将失败传播给调用方。此运算符只能在主块或函数内部使用。
* unsafe - 不鼓励处理失败的方法。此修饰符会将命令视为已成功完成，并允许无需任何其他步骤即可解析它们。

```bash
// Command statement
$mv file.txt dest.txt$ failed {
    echo "It seems that the file.txt does not exist"
}

// Command expression
let result = $cat file.txt | grep "READY"$ failed {
    echo "Failed to read the file"
}
echo result
```

命令表达式结果将发送到变量，而不是标准输出。

命令表达式也可以与其他表达式和变量进行插值

```bash
let file_path = "/path/to/file"
$cat {file_path}$ failed {
    echo "Could not open '{file_path}'"
}
```

#### 获取退出代码

要获取退出代码，您可以使用 status 关键字。它将始终返回最后一个 bash 命令或可失败函数的退出代码。

```bash
let file_path = "/path/to/file"
$cat {file_path}$ failed {
    echo "Error! Exit code: {status}"
}
echo "The status code is: {status}"
```

#### 错误传递

为了将错误传递到上下文，可以使用语法 ？。

```bash
$test -d /path/to/file$?
// Which is the same as

$test -d /path/to/file$ failed {
    fail status
}
```

要了解有关 fail 关键字的更多信息，请阅读 介绍 [failures](https://docs.amber-lang.com/basic_syntax/functions#failing) 的文章。

#### 命令修饰符

命令修饰符是更改命令行为的关键字。以下是一些示例：

* silent - 阻止命令将结果显示到标准输出。
* unsafe - 禁用要求用户处理故障的 Amber 机制。

```bash
silent unsafe $my command$
```

您可以将命令修饰符用作修饰符范围。

```bash
silent unsafe {
    $first command$
    if isReady:
            $second command$
    $third command$
}
```

#### 不安全的命令执行

```bash
unsafe $test -d /path/to/file$
```

这将以 Bash 处理语句的方式处理。如果失败，请继续执行代码。这种行为是我们在构建 Amber 时试图避免的行为。建议采用此方法的情况如下：

* 完全确定此命令将成功完成
* 不在乎命令会不会失败

#### 静默命令

命令静默操作。下面是一个示例用法：

```bash
silent $very loud command$
```

### 数组使用

要在数组的特定索引处存储或检索值，可以使用以下语法：

```bash
let groceries = ["Apple", "Banana", "Orange"]
groceries[0] = "Almond"
echo groceries[1]
// Outputs: Banana
```

截至目前 （Amber alpha），下标语法不适用于表达式！这意味着你还不能计算像 foo（）[0] 这样的表达式。

可以回显整个数组

```bash
echo groceries
// Outputs: Almond Banana Orange
```

要将元素添加到数组中，可以使用的加法运算符将两个数组合并在一起。

```bash
let capitals = ["London", "Paris"]
capitals += ["Warsaw"]
let cities = capitals + ["Barcelona", "Florence"]

```

要查看有关 array 数据类型的更多操作，请查看标准库文档，其中涵盖了 join、len 或 sum 等函数。

#### 生成数组

生成特定范围的数字数组 [Num]。有两种类型的范围：

* ..不包括从 a 到 b 的 b，不包括 b
* ..= 包括从 a 到 b（包括 b）

```bash
echo 0..10
// Outputs: 0 1 2 3 4 5 6 7 8 9
echo 0..=10
// Outputs: 0 1 2 3 4 5 6 7 8 9 10
```

### 循环

Amber 支持两种类型的循环：

* 只能用 break 关键字打破的无限循环
* 迭代数组的迭代器循环

在循环的上下文中，使用 break 和 continue 关键字来帮助您控制流。

#### 无限循环

```bash
let i = 0
let sum = 0
loop {
    if i == 5:
        break
    i += 1
    sum += i
}
echo sum
// Outputs: 15
```

#### 迭代器循环

```bash
let sum = 0
loop i in 0..5 {
    sum += i
}
echo sum
// Outputs: 10
```

这是另一个显示迭代器循环运行的示例：

```bash
let files = ["config.json", "file.txt", "audio.mp3"]

loop index, file in files {
    $mv {file} {index}{file}$ failed {
        echo "Failed to rename {file}"
    }
}
```

上面的示例将遍历数组中的所有文件，并根据它们在数组中的顺序为它们编制索引。因此，这些文件将重命名为 0config.json、1file.txt 和 2audio.mp3。

### 函数

以下是声明此类函数的方法：

```bash
fun myFunction(arg1, arg2) {
    let result = arg1 + arg2
    return result
}

echo myFunction(2, 3)
// Outputs: 5
echo myFunction("Hello", " World")
// Outputs: Hello World
```

声明一个接受特定类型参数的函数,并且指定返回类型

```bash
fun myFunction(arg1: Num, arg2: Num): Num {
    let result = arg1 + arg2
    return result
}
```

在指定参数类型的条件下，可以指定其默认值 — 如果在调用函数时没有提供其他值，则将使用该值：

```bash
fun addition(a: Num, b: Num = 100): Num {
    return a + b
}
echo addition(10) // Outputs: 110
echo addition(10, 20) // Outputs: 30
```

#### 修饰符

可以将 Command Modifiers 应用于函数调用。使用 silent 修饰符抑制任何输出，或者使用 unsafe 关键字运行可失败的函数。

#### 错误处理

函数也可能失败。我们称它们为 failable 函数。可失败函数是一种可能失败的函数。要使函数失败，请使用 fail 关键字，并在其后跟退出代码。

```bash
fun failing() {
    fail 1
}
```

以下是失败函数的另一个示例：

```bash
fun failing(name) {
    $command$?
    parse(name)?
}
```

请注意，使用 ？运算符会自动失败，并显示失败操作的状态代码。

如果指定可失败函数的返回类型，则还必须将 ？附加到类型名称。

```bash
fun failable(): Num? {
    if 0 > 5 {
        fail 1
    }

    return 1
}
```

不能简单地通过将 ？附加到返回类型来强制函数变为可失败。如果已知函数是可失败的，则 ？只能（并且必须）在函数声明中使用。

#### 状态代码

状态代码包含有关最新失败函数或运行的命令的信息。访问 status 就像使用 status 关键字一样简单。

```bash
fun safeDivision(a: Num, b: Num): Num {
    if b == 0:
        fail 1
    return a / b
}
```

看看这段代码在不同场景中的行为方式：

```bash
let result = unsafe safeDivision(24, 4)
echo "{result}, {status}"
// Outputs: 6, 0
```

当我们除以零时会发生什么：

```bash
let result = safeDivision(15, 0) failed {
    echo "function Failed"
    echo status
}
// Outputs:
// function Failed
// 1
```

#### 变量引用

引用传递的变量可以使用 ref 关键字。

```bash
fun push(ref array, value) {
    array += [value]
}

let groceries = ["apples", "bananas"]
push(groceries, "oranges")
echo groceries
// Outputs: apples bananas oranges
```

### 导入库

在 Amber 中，可以从其他文件导入函数。要使函数可从外部文件访问，需要在定义函数的文件中将其声明为 public。

#### 公共函数

要将函数声明为 public，可以使用 pub 关键字。pub 关键字必须在声明函数的 fun 关键字之前使用：

```bash
pub fun sum(left: Num, right: Num): Num {
    return left + right
}
```

#### 从其他文件导入

可以单独导入每个函数

```bash
import { foo, bar } from "./my-file.ab"

foo()
bar()
```

或者一次导入所有函数

```bash
import * from "./arith.ab"

echo sum(1, sub(2, mul(4, 5)))
```

#### 主区块

Amber 具有此模式的特殊语法。不过，它不仅仅是一个语法糖。Main scope 还允许您使用 ？运算符，在这种情况下，退出代码将简单地传播到外部 shell。

```bash
echo "Running indirectly"

main {
    $some command$?
    echo "Running directly"
}
```

如果我们运行此文件，输出将如下所示：

```bash
Running indirectly
Running directly
```

以下是我们导入文件时的行为。

```bash
import * from "./file.ab"
// Outputs: Running indirectly
```

主块和外部参数

Main 块可以提供传递给此脚本的参数数组（即 [Text] 类型）。

```bash
main (args) {
    loop i, arg in args {
        echo "{i}: {arg}"
    }
}
```

## 高级语法

#### As Cast

因为 cast 是一种工具，最初似乎是一种转换类型的简单方法。那么，为什么此功能在高级类别中占有一席之地呢？嗯，能力越大，责任越大。可以执行一些有意义的强制转换，例如从 Bool 到 Num，但您也可以执行我们称为荒谬的强制转换。这方面的一个例子可能是将 Text 转换为 Num。

##### Regular casts

有时你可能想要将 Bool 变量传递给接受 Num 的函数。由于 Bool 和 Num 是彼此兼容的类型，因此您可以轻松地将一个转换为另一个类型，如下所示：

```bash
let isReady = systemIsReady()
processStatus(isReady as Num)
```

##### Absurd casts

Amber 允许将一种数据类型转换为任何其他数据类型。应避免这样做，并且仅在必要时使用。

```bash
let a = "12"
let b = a as Num
```

我们可以清楚地看到，这可能会导致一些大错误。例如，可以在字符串中传递 “abc” 而不是 “12”，这不是 Num 类型的有效值。要将字符串转换为数字，最好使用标准库中的 parse（） 函数。

```bash
import { parse } from "std"

let a = "12"
let b = parse(a) failed {
    echo "Variable `a` is not a number."
}

echo b + 12
// Outputs: 24
```

#### Builtins

Builtins 是本机方法（内置于编译器本身中），它们也是 Amber 中的保留关键字。

与标准库类似，它们会生成有效的 Shellcheck 代码（尽管 Amber 对此的全面支持仍在进行中）。

##### cd

转译为 cd 会更改当前目录，需要一个 Text 参数。

```bash
cd "/tmp"
```

##### echo

将文本打印到控制台，需要 Text 参数。

```bash
echo "Hello World!"
```

##### mv

如果需要移动文件，可以使用 mv 内置，需要两个 Text 参数。

```
mv "/tmp/a" "/tmp/b"
```

这个内置函数是可失败的，这意味着你可以处理这样的错误：

```bash
mv "/tmp/a" "/tmp/b" failed {
    echo "Error"
}
```

##### nameof

对于更高级的命令，您可能需要编译脚本中的变量名称。nameof 关键字提供此功能。

这允许您执行以下操作：

```bash
let variable = null

unsafe ${nameof variable}=12$
// Which is the same as declaring (but it is more readable in this way)
let variable = 12
```

#### 类型条件

有时，在构建泛型函数时，人们会发现额外验证 pass value 是否是支持的数据类型之一非常方便。

```bash
fun getObject(value) {
    if {
        value is Text: getByName(value)
        value is Num: getById(value)
    }
}
```

### 编译器标志

编译器标志允许您更改编译器在函数的给定范围内的行为。请记住，有些警告是有原因的。如果您不明白为什么会发出某些警告，请继续了解有关基本语法的更多信息。以下是所有可供使用的编译器标志的列表：

* allow_nested_if_else - 关闭鼓励开发人员使用旨在处理 if else 链的语法的警告。
* allow_generic_return - 关闭警告，该警告指示用户在使用具有具体类型的参数时指定具体返回类型
* allow_absurd_cast - 关闭警告，告知用户给定力类型的结果可能很荒谬。

```bash
#[allow_nested_if_else]
fun foo() {
    // ...
}
```

## 标准库

[内置函数](https://docs.amber-lang.com/advanced_syntax/builtins)是包含在 Amber 编译器中的方法，不需要在代码中导入。

相比之下，标准库 （stdlib） 是嵌入在每个 Amber 版本中的 Amber 函数的集合。Amber 的每个版本都可能包含对标准库的更改，您需要在代码中导入这些函数。这些功能更高级，可以接受各种参数。

### 标准库和 Shellcheck

就像 Amber 编译的 Bash 输出一样，所有标准库函数都是从头开始构建的，以符合 shellcheck。这意味着您可以更专注于构建逻辑，而花更少的时间保持代码的可预测性和有效性。

### 如何使用

以下是如何使用标准库生成文档的示例（使用 Amber Documentation 存储库中提供的[脚本](https://github.com/amber-lang/amber-docs/sync-stdlib-doc.ab)）：

```bash
import { download } from "std/http"
import { split, contains } from "std/text"
import { file_exist } from "std/fs"

unsafe $rm -fr /tmp/amber-git$
if silent download("https://github.com/amber-lang/amber/archive/refs/heads/master.zip", "/tmp/amber-git.zip") {
    unsafe $unzip "/tmp/amber-git.zip" -d /tmp/amber-git$

    let std = unsafe $/usr/bin/ls "/tmp/amber-git/amber-master/src/std/"$
    let stdlib = split(std, "\n")

    loop v in stdlib {
        if (contains(v, ".ab") and file_exist("/tmp/amber-git/amber-master/src/std/{v}")) {
            unsafe $amber --docs "/tmp/amber-git/amber-master/src/std/{v}" "./docs/stdlib/doc"$
            echo "\n"
        }
    }
}
```

### 数组

#### `array_first_index`

```bash
pub fun array_first_index(array, value): Num
```

返回在指定数组中找到的第一个值的索引。如果未找到该值，则函数返回 -1。

#### `array_search`

```bash
pub fun array_search(array, value): [Num]
```

在数组中搜索值，并返回包含各种项索引的数组。

#### `includes`

```bash
pub fun includes(array, value)
```

# 参考

[Amber 文档](https://docs.amber-lang.com)
