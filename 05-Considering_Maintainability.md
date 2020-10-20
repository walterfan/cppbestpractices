# 考虑可维护性

## 避免编译器宏

在运行编译器之前，编译器定义和宏将被预处理器替换。 这会使调试非常困难，因为调试器不知道源代码来自何处。


```cpp
// Bad Idea
#define PI 3.14159;

// Good Idea
namespace my_project {
  class Constants {
  public:
    // if the above macro would be expanded, then the following line would be:
    //   static const double 3.14159 = 3.14159;
    // which leads to a compile-time error. Sometimes such errors are hard to understand.
    static constexpr double PI = 3.14159;
  };
}
```

## 考虑避免使用 Boolean 参数

这些布尔参数在阅读代码时不提供其他含义，你可以创建一个名字更有意义的单独的函数，也可以传递一个含义更清楚的枚举值。

更多信息参见 http://mortoray.com/2015/06/15/get-rid-of-those-boolean-function-parameters/。

## Avoid Raw Loops 避免裸循环

知晓并理解已有的 C++ 标准算法并使用它们

 * 参见 [cppreference](https://en.cppreference.com/w/cpp/algorithm)
 * 察看 [C++ Seasoning](https://www.youtube.com/watch?v=qH6sSOr-yk8)
 
考虑对 `[]` 的调用为一个潜在的代码臭味，它表示可能没有使用过算法

## 不要使用带副作用的 `assert` 

```cpp
// Bad Idea
assert(set_value(something));

// Better Idea
[[maybe_unused]] const auto success = set_value(something);
assert(success);
```

`assert()` 将在发行版本中删除，这会防止发生 `set_value` 的调用。

因此，尽管第二版比较丑陋，但第一版根本不正确。

## 正确的使用 'override' 和 'final'

这些关键字使其他开发人员清楚了解虚拟函数的使用方式，如果虚拟函数的签名发生更改，则可以捕获潜在的错误，并且可能[提示给编译器](http://stackoverflow.com/questions/7538820/how-does-the-compiler-benefit-from-cs-new-final-keyword) 如何进行优化。