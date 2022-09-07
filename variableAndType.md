# 变量和类型

## bool类型
```cpp
bool b = 42; // the value of b is true(1)

int i = b; // the value of i is 1

```
## 无符号数和有符号数
```cpp
unsigned u = 10;
int i1 = -42;
std::cout << u + i1
        << std::endl; //有符号数和无符号数相加转为无符号数为4294967264

unsigned u1 = 10;
unsigned u2 = 42;
std::cout << u1 - u2 << std::endl; //结果为无符号数为4294967264
std::cout << u2 - u1 << std::endl; //结果为无符号数为32

for (int i = 0; i >= 0; i--) { //正确，10次后结束
std::cout << i << std::endl;
}

for (unsigned i = 0; i >= 0; i--) { //错误，死循环，i永远不可能为负数
std::cout << i << std::endl;
}
```
## 列表初始化

```cpp
//下面都可以将变量i初始化为0
int i = 0;
int i = {0};
int i {0};
int i (0);

long double ld = 3.1415926;
int a{ld},b = {ld}//错误，转换不能执行
int a(ld),b = ld;//正确，能转换，但丢失小数部分
```
## 声明和定义

```cpp

extern int i;//声明i没有定义i
int j;//声明j并定义j
extern int k = 1;//定义
```

## 引用

>**引用并非对象**，只是把一个已经存在的对象起了一个新名字。  
定义引用时，程序将引用与初始值绑定在一起，无法绑定到另一个对象
``` cpp
int i = 1024;
int &i1 = i;//i1指向i（其实就是i的另外一个名字）
int &i2;//错误，引用必须被初始化

int &a  = 10;//初始值必须是一个对象
int &b = a;//错误
```

## 空指针

``` cpp
//下面三个都是空指针
int *p1 = nullptr;
int *p2 = 0;
int *p3 = NULL;//需要include cstdlib
```

## void* 指针

> void* 指针可以存放任意对象的地址

## 指向指针的引用

> 引用不是对象，但指针是对象。所有可以对指针引用

``` cpp
int i = 42;
int *p;
int *&r = p;//r是一个对指针的引用

r = &i;//指向i;

*r = 0;//i的值为0
```

## const 限定符

``` cpp
const int buff_size = 512;
buff_size = 0;//错误,buff_size为一个常量，不可修改

const int i;//错误，const修饰的量必须初始化
```

> 默认情况下，const对象只在本文件内有效。在不同文件内定义的同名const对象，其实相当于在不同文件内分别定义了独立的变量。如果想要在不同文件共享一个const对象，对于const对象不管是定义还是声明都加上`extern`（在源文件和头文件的const对象都加`extern`）

### const 的引用

``` cpp
const int ci = 1024;
const int &r1 = ci;
r1 = 42;//错误，ri是对常量的引用
int &r2 = ci; //错误，非常量引用无法指向一个常量对象

int i = 2;
const int &r2 = 42;//正确，r2是一个常量引用
const int &r3 = i * 2;//正确，r3是一个常量引用
int &r4 = i * 2//错误，r4不是一个常量引用
```
> 常量引用被绑到另外一个类型上实际是绑定了一个**临时量**对象。

``` cpp
double dval = 3.14;
const int &ri = dval;

//编译器实际上发生的转换
double dval = 3.14;
const int temp = dval;
const int &ri = temp;
```

## 指针和const
``` cpp
const double dval = 3.14;
double *ptr = &dval;//错误，ptr是一个普通指针
const double *ptr1 = &dval;//正确
*ptr1 = 45;//错误，不能给ptr赋值

const double const *ptr2 = &dval;//ptr2是一个指向常量对象的常量指针
*ptr2 = 45；//错误，ptr2指向的对象的值无法更改
double i = 1;
ptr2 = i;//错误，ptr2指向的对象无法更改
```
## constexpr和常量表达式

> 一个对象是不是常量表达式由数据类型和初始值决定

``` cpp
const int max = 20;//是常量表达式
const int limit = max + 1;//是常量表达式

int size = 21;//不是常量表达式

const int sz = get_size();//不是常量表达式
```

### constexpr变量
> c++11新标准规定，允许将变量声明为`constexpr`类型以便由编译器来验证变量的值是否为一个常量表达式。
``` cpp
constexpr int mf = 20;//常量表达式
constexpr int limit = mf + 1;//常量表达式
constexpr int sz = size();//只有当size()为一个constexpr函数时才是一条正确的语句
```

## 字面值常量
> ***算数类型***、***引用*** 和 ***指针***都属于字面值类型。自定义类，IO库，string等不属于字面值类型，***不能定义为constexpr***

>一个constexpr指针的初始值必须为nullptr或者0或者储存于某个固定地址的对象。函数体内变量存放在非固定地址，constexpr指针不能指向这样的变量，函数外的变量存放在固定地址，可以用。函数内有效范围超过函数的变量由固定地址（比如malloc new出来的变量对象）可以用constexpr

## 指针和constexpr
> constexpr声明中定义的指针限定符constexpr仅对指针有效，与指针指向的对象无关
``` cpp
const int *p = nullptr;//指向整型常量的指针
constexpr int *q = nullptr;//指向整数的常量指针
```

## 类型别名

* 传统方法：使用关键字typedef
    ``` cpp
   typedef double wages;
    ```
* 新标准：使用using
    ``` cpp
   using SI = Sales_item//Sales_item是一个自定义类型
    ```

## 指针、常量和类型别名
``` cpp
typedef char *pstring;
char a[4]{1, 2};
const pstring cstr = a; //cstr使指向char的常量指针
const pstring *ps; //ps是一个指针，它指的对象是指向char的常量指针

*cstr = 1;//正确
cstr = nullptr;//错误
```

## auto
> 让编译器自动去推断表达式所属的类型

> 可以使用auto在一条语句中声明多个变量，但变量类型要相同，所有这条语句的所有初始值的基本数据类型必须全部一样。
``` cpp
auto i = 0,*p = &i;//正确，i为整数，p为整型指针
auto sz = 0 ,pi = 3.14;//错误，sz和pi类型不一样
```

## 复合类型、常量和auto

``` cpp
auto i = 0,&r = i;
auto a = r;//a为整数
```
> auto一般会忽略顶层const,同时底层const会保留下来

``` cpp
auto i = 0;
const int ci = i,&cr = ci;
auto b = ci;//b是一个整数（顶层const被忽略了）
auto c = cr;//c是一个整数（cr本身是ci的别名，ci本身是一个顶层const）
auto d = &i;//d是一个整型指针
auto e = &ci;//e是一个指向整数常量的指针（对常量对象取地址是一种底层const）
```
> 如果希望推断出来的auto类型是一个顶层const，需要明确指出

``` cpp
const auto f = ci;
```
> 引用类型也可以为auto
``` cpp
auto &g = ci;//正确
auto &h = 42;//错误
const auto &j = 42;//正确
```

## decltype 类型指示符
``` cpp
decltype(f()) sum = x;//sum的类型就是函数f的返回类型（实际上并没有调用f，而是调用发生时f的返回类型作为sum的类型）
```

``` cpp
const int ci = 0 , &cj = ci;
decltype(ci) x = 0;//x的类型为const int
decltype(cj) y = 0;//y的类型为const int&
decltype(cj) z;//z为const int&，必须初始化
```
> 发生解引用操作时decltype的类型为一个引用，如果是`decltype((variable))`（双层括号）时，类型永远为引用，而`decltype(variable)`,只有当variable为一个引用时，类型才为引用
``` cpp
int i = 42,*p = &i,&r = i;
decltype(r + 0) b;//加法结果为int
decltype(*p) c;//错误，因为c是int&，必须初始化
decltype((i)) d;//错误，类型为引用,必须初始化
```
