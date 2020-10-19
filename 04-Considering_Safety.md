# 考虑安全性

## 尽可能的多用 Const

const 告诉编译器一个变量或方法是不可变的。这可以帮助编译器优化代码和帮助开发者了解一个函数是否有副作用。而且使用 `const &` 可以阻止编译器做不必要的拷贝。[John Carmack 对 `const` 的解读](http://kotaku.com/454293019) 是一个很不错的参考阅读。


```cpp
// Bad Idea
class MyClass
{
public:
  void do_something(int i);
  void do_something(std::string str);
};


// Good Idea，const 修饰的参数表示方法不会修改它
class MyClass
{
public:
  void do_something(const int i);
  void do_something(const std::string &str);
};

```

### 仔细考虑你的返回类型

 * Getters
   * 当一般使用返回值进行观察时，返回 `&` 或 `const &` 能有显著的性能提高
   * 按值返回更有利于线程安全，并且如果正常使用返回的值(原始类型)来进行复制，则不会损失性能
   * 如果您的API使用协变返回类型，则必须通过`＆`或`*`返回
 *  临时值和本地值
   * 始终按值返回。

注: 方法的协变返回类型是一种在子类中重写该方法时可以用“更窄”类型替换的方法。 在 C++ 中，这是一种相当常见的用法。

### 不要用 const 引用来传递和返回简单类型

```cpp
// Very Bad Idea
class MyClass
{
public:
  explicit MyClass(const int& t_int_value)
    : m_int_value(t_int_value)
  {
  }
  
  const int& get_int_value() const
  {
    return m_int_value;
  }

private:
  int m_int_value;
}
```

而是按值传递和返回简单类型。如果您不打算更改传递的值，则将它们声明为 const，而不声明为const refs:

```cpp
// Good Idea
class MyClass
{
public:
  explicit MyClass(const int t_int_value)
    : m_int_value(t_int_value)
  {
  }
  
  int get_int_value() const
  {
    return m_int_value;
  }

private:
  int m_int_value;
}
```

为什么呢？ 因为按引用传递和返回导致指针操作，而不是通过处理器寄存器中更快得多的传递值。

## 避免原始的内存存取

原始的内存访问，分配和重新分配很难在 C++ 中正确无误地使用，而又不会[引起内存错误和泄漏](http://blog2.emptycrate.com/content/nobody-understands-c-part-6-are-you-still-using-pointers)。 C++ 11 提供了避免这些问题的工具。

```cpp
// Bad Idea
MyClass *myobj = new MyClass;

// ...
delete myobj;


// Good Idea
auto myobj = std::make_unique<MyClass>(constructor_param1, constructor_param2); // C++14
auto myobj = std::unique_ptr<MyClass>(new MyClass(constructor_param1, constructor_param2)); // C++11
auto mybuffer = std::make_unique<char[]>(length); // C++14
auto mybuffer = std::unique_ptr<char[]>(new char[length]); // C++11

// or for reference counted objects
auto myobj = std::make_shared<MyClass>(); 

// ...
// myobj is automatically freed for you whenever it is no longer used.
```

## 使用 `std::array` 或 `std::vector` 来替代 C 风格的数组

Both of these guarantee contiguous memory layout of objects and can (and should) completely replace your usage of C-style arrays for many of the reasons listed for not using bare pointers.

这些都保证了对象的连续内存布局，并且由于已经列出的许多不使用裸指针的原因，可以（并且应该）完全替换你对 C 风格数组的使用。

并且, 要[避免](http://stackoverflow.com/questions/3266443/can-you-use-a-shared-ptr-for-raii-of-c-style-arrays) 使用 `std::shared_ptr` 来持有一个数组. 

## 使用异常

异常不能忽略。 返回值，例如使用 `boost::optional`，可以忽略，但如果不检查异常，可能导致崩溃或内存错误。 另一方面，异常应该进行捕获并处理。 通过写日志并自动重新启动应用程序，可将其一直提升到应用程序处理的最高级别。

C++ 的初始设计者 Stroustrup [阐明了这一点]（http://www.stroustrup.com/bs_faq2.html#exceptions-why）比我以前讲要好得多。

## 使用 C++ 风格的类型转换以替代 C 风格的类型转换


使用 C++ 风格的类型转换（`static_cast<>，dynamic_cast<>...`），而不是 C 风格的类型转换。 C++ 风格的类型转换允许进行更多的编译器检查，并且更加安全。

```cpp
// Bad Idea
double x = getX();
int i = (int) x;

// Not a Bad Idea
int i = static_cast<int>(x);
```
Additionally the C++ cast style is more visible and has the possibility to search for.

But consider refactoring of program logic (for example, additional checking on overflow and underflow) if you need to cast `double` to `int`. Measure three times and cut 0.9999999999981 times.


此外，C++ 风格的强制转换更明显，并且可以搜索。

但是，如果需要将 double 转换为 int，请考虑重构程序逻辑（例如，对溢出 overflow 和下溢 underflow 进行额外检查）。 多测试几次。

## 不定义可变函数

可变参数函数可以接受可变数量的参数。 可能最著名的示例是 `printf()`。 你可以自己定义此类功能，但这可能会带来安全风险。 可变参数函数的使用类型不安全，错误的输入参数可能会导致程序终止，并且行为不确定。 这种未定义的行为可以被利用从而导致安全问题。

如果可以使用支持 `C++ 11` 的编译器，则可以改用可变参数模板。


参见 [使用某些编译器制作类型安全的 C 风格可变参数在技术上是可能的](https://github.com/lefticus/cppbestpractices/issues/53)

## 其他资源

David Wheeler 写的[ 如何防止下一次的 Heartbleed 问题](http://www.dwheeler.com/essays/heartbleed.html) 对当前代码安全性以及如何确保安全代码进行了很好的分析。
