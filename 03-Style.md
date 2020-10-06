# 风格

一致性是代码风格最重要的方面，第二重要的方面是符合一般 C++ 程序员的惯用法

C ++允许使用任意长度的标识符名称，因此命名时没有理由太简洁。 使用描述性名称，并保持样式一致。

 * `CamelCase` 驼峰命名法
 * `snake_case` 蛇身命名法

是常见的例子。 *snake_case* 的优点是，如果需要，它也可以与拼写检查器一起使用。


## 建立一个风格指南

无论您建立什么样式指南，请务必实现一个 `.clang-format` 文件，该文件指定所需的样式。 尽管这无助于命名，但对于开源项目而言，保持一致的风格尤为重要。

每个IDE和许多编辑器都支持内置的”C/C++语言风格“， 也可通过插件轻松安装。

 * VSCode: [Microsoft C/C++ extension for VS Code](https://github.com/Microsoft/vscode-cpptools)
 * CLion: https://www.jetbrains.com/help/clion/clangformat-as-alternative-formatter.html
 * VisualStudio https://marketplace.visualstudio.com/items?itemName=LLVMExtensions.ClangFormat#review-details
 * Resharper++: https://www.jetbrains.com/help/resharper/2017.2/Using_Clang_Format.html
 * Vim
     * https://github.com/rhysd/vim-clang-format
     * https://github.com/chiel92/vim-autoformat
 * XCode: https://github.com/travisjeffery/ClangFormat-Xcode



## 通用的 C++ 命名惯例

 * 类型以大写字母开头: `MyClass`.
 * 函数和变量以小写字母开头: `myMethod`.
 * 常量一律大写: `const double PI=3.14159265358979323;`.

C++ 标准库 (和其他一些知名的 C++ 软件库，例如 [Boost](http://www.boost.org/)) 使用这些风格:

 * 宏命名使用大写字母结合下划线: `INT_MAX`.  
 * 模板参数命名使用驼峰命名法: `InputIterator`. 
 * 所有其它的名字使用蛇身命名法: `unordered_map`. 

## 区分私有对象数据

私有的成员添加前缀 `m_` 以便于区分于公有成员， `m_` 表示 "member" 数据.

## 区分函数参数

最重要的是代码库中的一致性。 这是帮助保持一致性的一种可能性。

用 `t_` 前缀命名函数参数。 可以将 `t_` 视为 `the`，但含义是任意的。 关键是要在范围内将函数参数与其他变量区分开来，同时为我们提供一致的命名策略。

可以为你的组织选择任意的前缀或后缀。这只是一个例子，*这个建议有争议，关于它的讨论参见[#11](https://github.com/lefticus/cppbestpractices/issues/11).*


```cpp
struct Size
{
  int width;
  int height;

  Size(int t_width, int t_height) : width(t_width), height(t_height) {}
};

// This version might make sense for thread safety or something,
// but more to the point, sometimes we need to hide data, sometimes we don't.
class PrivateSize
{
  public:
    int width() const { return m_width; }
    int height() const { return m_height; }
    PrivateSize(int t_width, int t_height) : m_width(t_width), m_height(t_height) {}

  private:
    int m_width;
    int m_height;
};
```


如非必要，尽量不要公开内部数据，隐藏数据是一个好习惯.

## 不要以 `_` 开头命名任何东西

为了不和编译器与标准库发生冲突，不要以 `_` 开头

参见[StackOverlfow 上的讨论](http://stackoverflow.com/questions/228783/what-are-the-rules-about-using-an-underscore-in-a-c-identifier)



## 格式正确的示例

```cpp
class MyClass
{
public:
  MyClass(int t_data)
    : m_data(t_data)
  {
  }

  int getData() const
  {
    return m_data;
  }

private:
  int m_data;
};
```



## 启用源目录外(Out-of-Source-Directory)构建


确保生成的文件放到一个与源代码分开的输出目录

## 使用 `nullptr`

C++11 引入了一个 `nullprt` 来表示空指针，它应该用来替代 `0` 或 `NULL` 来表示空指针

## 注释

尽量多使用 C++ 的双斜杠注释风格，这样在调试代码更加容易注释掉一大块代码

```cpp
// this function does something
int myFunc()
{
}
```

在调试代码时，我们可以这样注释掉这个函数:

```cpp
/*
// this function does something
int myFunc()
{
}
*/
```

如果用 `/* */` 来注释函数，这样就很麻烦.

## 绝不要在头文件中使用 `using namespace` 

这会使你正在使用的 namespace 被放到包括头文件的所有文件的 namespace 中。
它会污染 namespace，并且将来可能导致名称冲突。

当然，在实现文件中使用 `using namespace` 是没问题的。

## 包含保护

头文件必须包含一个不同名称的包含保护，以避免多次包含同一个头文件的问题，并防止与其他项目的头文件冲突。

头文件必须包含如下的宏卫语句

```cpp
#ifndef MYPROJECT_MYCLASS_HPP
#define MYPROJECT_MYCLASS_HPP

namespace MyProject {
  class MyClass {
  };
}

#endif
```

您也可以考虑使用 `#pragma once` 指令，它在许多编译器中都是准标准的。
它很简短，目的明确。

## {} 对于语句块是必需的

用 {} 来包围语句块，以避免不小心造成可能的语义错误

```cpp
// Bad Idea
// This compiles and does what you want, but can lead to confusing
// errors if modification are made in the future and close attention
// is not paid.
for (int i = 0; i < 15; ++i)
  std::cout << i << std::endl;

// Bad Idea
// The cout is not part of the loop in this case even though it appears to be.
int sum = 0;
for (int i = 0; i < 15; ++i)
  ++sum;
  std::cout << i << std::endl;


// Good Idea
// It's clear which statements are part of the loop (or if block, or whatever).
int sum = 0;
for (int i = 0; i < 15; ++i) {
  ++sum;
  std::cout << i << std::endl;
}
```

## 保持合理的行长度

```cpp
// Bad Idea
// hard to follow
if (x && y && myFunctionThatReturnsBool() && caseNumber3 && (15 > 12 || 2 < 3)) {
}

// Good Idea
// Logical grouping, easier to read
if (x && y && myFunctionThatReturnsBool()
    && caseNumber3
    && (15 > 12 || 2 < 3)) {
}
```

许多项目和编码标准都有一个换行指导，即每行应使用少于80或100个字符。
这样的代码通常更容易阅读。它还可以在一个屏幕上并排放置两个单独的文件，而无需使用很小的字体。


## 使用 "" 来包含本地文件
`<>` 是[保留来用于系统文件包含的](http://blog2.emptycrate.com/content/when-use-include-verses-include).

即用 `“”` 来包含本地的头文件，用 `<>` 来包含系统文件

```cpp
// Bad Idea. Requires extra -I directives to the compiler
// and goes against standards.
#include <string>
#include <includes/MyHeader.hpp>

// Worse Idea
// Requires potentially even more specific -I directives and
// makes code more difficult to package and distribute.
#include <string>
#include <MyHeader.hpp>


// Good Idea
// Requires no extra params and notifies the user that the file
// is a local file.
#include <string>
#include "MyHeader.hpp"
```

## 初始化成员变量

用成员初始化列表来初始化成员变量

对于POD类型，初始化器列表的性能与手动初始化相同，但是对于其他类型，则具有明显的性能提升，请参见下文。

```cpp
// Bad Idea
class MyClass
{
public:
  MyClass(int t_value)
  {
    m_value = t_value;
  }

private:
  int m_value;
};

// Bad Idea
// This leads to an additional constructor call for m_myOtherClass
// before the assignment.
class MyClass
{
public:
  MyClass(MyOtherClass t_myOtherClass)
  {
    m_myOtherClass = t_myOtherClass;
  }

private:
  MyOtherClass m_myOtherClass;
};

// Good Idea
// There is no performance gain here but the code is cleaner.
class MyClass
{
public:
  MyClass(int t_value)
    : m_value(t_value)
  {
  }

private:
  int m_value;
};

// Good Idea
// The default constructor for m_myOtherClass is never called here, so 
// there is a performance gain if MyOtherClass is not is_trivially_default_constructible. 
class MyClass
{
public:
  MyClass(MyOtherClass t_myOtherClass)
    : m_myOtherClass(t_myOtherClass)
  {
  }

private:
  MyOtherClass m_myOtherClass;
};
```

在 C++ 11 中，你可以为每个成员变量设置默认值，就象 Java 那样 (使用 `=` 或 `{}`).

### 用 = 来赋默认值

```cpp
// ... //
private:
  int m_value = 0; // allowed
  unsigned m_value_2 = -1; // narrowing from signed to unsigned allowed
// ... //
```

这样可以确保构造函数不会忘掉初始化一个成员变量

通过等号来初始化成员变量，这种方法允许类型窄化，类型窄化是指一种类型转换

### 用大括号来赋予初始值

通过括号来初始化成员变量，这种方法不允许在编译期将类型窄化

```cpp
// Best Idea

// ... //
private:
  int m_value{ 0 }; // allowed
  unsigned m_value_2 { -1 }; // narrowing from signed to unsigned not allowed, leads to a compile time error
// ... //
```

比起来通过`=`， 更倾向于 `{}` 来进行成员变暖初始化， 除非你有非常合理的原由

忘记初始化是未定义的行为错误之源，通常很难被发现

如果成员变量在初始化后不期望被改变，就标记它为 `const`


```cpp
class MyClass
{
public:
  MyClass(int t_value)
    : m_value{t_value}
  {
  }

private:
  const int m_value{0};
};
```

由于 const 成员变量不能赋予新的值，所以拷贝和赋值函数和操作符意义有所不同。

## 总是使用名称空间 namespace

几乎没有理由在全局名称空间中声明标识符。 相反，函数和类应该存在于适当命名的名称空间中或名称空间内部的类中。 放置在全局名称空间中的标识符可能会与其他库（主要是C，没有名称空间）中的标识符发生冲突。


## 遵循标准库特性，使用正确的 Integer 类型

标准库通常将 `std::size_t` 用于与大小有关的任何事情。 size_t 的大小由实现定义。

通常，使用 `auto` 将避免大多数（但不是全部）问题。

确保使用正确的整数类型，并与 C++ 标准库保持一致。 它可能不会在当前使用的平台上发出警告，但是当您更改平台时可能会发出警告。


*请注意，对无符号值执行某些操作时，可能导致整数下溢。 例如：*


```cpp
std::vector<int> v1{2,3,4,5,6,7,8,9};
std::vector<int> v2{9,8,7,6,5,4,3,2,1};
const auto s1 = v1.size();
const auto s2 = v2.size();
const auto diff = s1 - s2; // diff underflows to a very large number
```

## 使用 .hpp 和 .cpp 作为文件扩展名

基本上这是一个优选项，但是 .hpp 和 .cpp 被各种编辑器和工具广泛认可。 因此，这个选择是务实的。 具体来说，Visual Studio 仅会自动识别 C++文件的 .cpp 和 .cxx，而Vim不一定会将 .cc 识别为 C++文件。

一个特别大的项目（[OpenStudio]（https://github.com/NREL/OpenStudio））将 .hpp 和 .cpp 用于用户生成的文件，将 .hxx 和 .cxx 用于工具生成的文件。 两者都广为人知，这样的区别是很有帮助的。

## 不要混用 Tab 和 空格

默认情况下，某些编辑器喜欢将制表符和空格混合使用。 这使得不使用完全相同的制表符缩进设置的任何人都无法阅读该代码。 配置您的编辑器，以免发生这种情况。

## 不要在 `assert()` 的参数中放带有副作用的代码

```cpp
assert(registerSomeThing()); // make sure that registerSomeThing() returns true
```

上面的代码在进行调试版本时会成功，但是在进行发行版本时会被编译器删除，从而使您在调试和发行版本之间有不同的行为。
这是因为 `assert()` 是一个宏，在发布模式下不会扩展为任何宏。

## 不要畏惧模板

他们可以帮助您坚持[DRY原则](http://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。

它们应优先于宏，因为宏不支持名称空间等。

## 审慎地使用运算符重载

运算符重载的发明是为了启用表达语法。从两个大整数相加的意义上来说像 `a + b`，而不是 ` a.add（b）`。另一个常见的例子是 `std::string`，这是很常见的，用 `string1 + string2` 连接两个字符串。

但是，你可能使用过多或错误的运算符重载来轻易创建不可读的表达式。当重载运算符时，要遵循[关于stackoverflow]（http://stackoverflow.com/questions/4421706/operator-overloading/4421708#4421708）中所述的三个基本规则。

当要处理资源时，重载 `operator=()` 是必需的， 参见后面的[考虑零原则](03-Style.md#consider-the-rule-of-zero)


* 对于所有其他运算符，仅当它们在通常与这些运算符连接的上下文中使用时才重载它们。典型的场景是用 `+` 连接事物，否定可以视为 "true" 或 "false" 的表达式，等等

* 始终要注意[运算符优先级](http://en.cppreference.com/w/cpp/language/operator_precedence), 并尽量避免不直观难以理解的重载构造。


* 除非实现数字类型，或在特定领域中遵循公认的语法，否则不要重载 `~` 或 `%` 之类的不常用的运算符。


* 从不重载逗号运算符 `operator,()`，参见[何时重载逗号运算符]((http://stackoverflow.com/questions/5602112/when-to-overload-the-comma-operator?answertab=votes#tab-top))


* 对于输入输出流，使用非成员函数 `operator>>()` 和 `operator<<() 来做运算符重载，例如，你可以重载 `operator<<(std::ostream &, MyClass const &)` 来启用“输出”你的类对象到一个流中，例如`std::cout` 或 `std::fstream`，抑或 `std::stringstream`，后者常用于创建一个表示其值的字符串。

* 还有更多常见的运算符可以重载，参见[此处描述](http://stackoverflow.com/questions/4421706/operator-overloading?answertab=votes#tab-top).

更多有关自定义运算符的实现的详细信息的窍门，可以在[这里](http://courses.cms.caltech.edu/cs11/material/cpp/donnie/cpp-ops.html)找到。


## 避免隐式的转换

### 单参数构造函数


可以在编译时应用单参数构造函数以在类型之间自动转换。 这对于`std::string（const char *）` 之类的东西很方便，但是通常应避免使用，因为它们会增加意外的运行时开销。

应该将单参数构造函数标识为 `explicit`， 这样需要它们要被显式地调用。

### Conversion Operators

类似于单参数构造函数， 转换运算符也可被编译器调用，引入意外的行为. 它们也应该标识为 `explicit`



```cpp
//bad idea
struct S {
  operator int() {
    return 2;
  }
};
```

```cpp
//good idea
struct S {
  explicit operator int() {
    return 2;
  }
};
```

## 考虑零规则


零规则指出，除非正在构造的类执行某种不同寻常的所有权形式，否则不提供编译器可以提供的任何函数（复制构造函数、复制赋值运算符、移动构造函数、移动赋值运算符、析构函数）。

目标是让编译器提供最佳版本，当添加更多成员变量时自动维护这些版本。

[这篇文章](http://www.nirfriedman.com/2015/06/27/cpp-rule-of-zero/) 提供了背景知识，并说明了将近100％的时间实现技术。

