# 泛型

## lambda

### lambda 表达式的概念和基本用法

lambda表达式定义了一个匿名函数，并且可以捕获一定范围内的变量。lambda表达式的语法可以表示如下

> [ capture ] ( params ) opt -> ret { body; };

* capture 捕获列表
* params 参数表
* opt 函数可选项
* ret 返回类型
* body 函数体

```cpp
auto f = [](int a) -> int { return a + 1; };
std::cout << f(1) << std::endl;
```
很多时候可以省略返回类型
```cpp
auto f = [](int a) { return a + 1; };
std::cout << f(1) << std::endl;
```

初始化列表不能用于返回类型的自动推导

```cpp
auto x1 = []() { return {1,2,3,4}; };//错误
```
这时需要显式给出返回值类型

lambda在没有参数列表时可以省略参数列表
```cpp
auto x1 = [] { return 1; };//错误
```
### 使用lambda表达式捕获列表

* [] 不捕获任何变量
* [&] 捕获外部作用域的所有变量，并作为引用在函数体中使用（按引用捕获）
* [=] 捕获外部作用域的所有变量，并作为副本在函数体中使用（按值捕获）
* [=, &foo] 按值捕获外部作用域的所有变量，并按引用捕获foo变量
* [bar] 按值捕获bar变量
* [this] 捕获当前的this指针，让lambda表达式拥有和当前类成员函数同样的访问权限。如果使用了&或者=着默认此选项。捕获this可以使用当前类的成员函数和成员变量
```cpp
class A
{
    public:
    int i_ = 0;
    void func(int x, int y)
    {
        auto x1 = []{ return i_; };                    // error，没有捕获外部变量
        auto x2 = [=]{ return i_ + x + y; };           // OK，捕获所有外部变量
        auto x3 = [&]{ return i_ + x + y; };           // OK，捕获所有外部变量
        auto x4 = [this]{ return i_; };                // OK，捕获this指针
        auto x5 = [this]{ return i_ + x + y; };        // error，没有捕获x、y
        auto x6 = [this, x, y]{ return i_ + x + y; };  // OK，捕获this指针、x、y
        auto x7 = [this]{ return i_++; };              // OK，捕获this指针，并修改成员的值
    }
};
int a = 0, b = 1;
auto f1 = []{ return a; };               // error，没有捕获外部变量
auto f2 = [&]{ return a++; };            // OK，捕获所有外部变量，并对a执行自加运算
auto f3 = [=]{ return a; };              // OK，捕获所有外部变量，并返回a
auto f4 = [=]{ return a++; };            // error，a是以复制方式捕获的，无法修改
auto f5 = [a]{ return a + b; };          // error，没有捕获变量b
auto f6 = [a, &b]{ return a + (b++); };  // OK，捕获a和b的引用，并对b做自加运算
auto f7 = [=, &b]{ return a + (b++); };  // OK，捕获所有外部变量和b的引用，并对b做自加运算
```

lambda表达式按值捕获变量，在捕获的一瞬间，a的值就已经被复制了
```cpp
int a = 0;
auto f = [=]{ return a; };

a++;
cout << f() << endl;//输出 0
```
> 如果需要修改捕获的变量就需要使用引用方式捕获

如果希望取修改按值捕获的外部变量
```cpp
int a = 0;
auto f = [=]() mutable { return a++; };
//注意，加上mutable之后参数列表不能省略
cout << f() << endl;//输出 1
```

## lambda表达式的类型

lambda表达式在c++11中被称为“闭包类型（Closure Type）”，它时一种特殊的，匿名的非nunion的类类型

因此可以认为他是一个带有operator()的类（仿函数）。因此可以用std::function和std::bind来存储和操作lambda表达式
```cpp
std::function<int(int)> f1 = [](int a){ return a; };
std::function<int(int)> f2 = std::bind([](int a){ return a; },123);

```

对于没有捕获任何变量的lambda表达式可以转换为一个普通的函数指针

```cpp
using func_t = int(*)(int);
func_t f = [](int a){ return a; };
f(123);
```
