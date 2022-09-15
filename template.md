# 模板

## 函数模板
```cpp
template <typename T>
int compare(const T &v1, const T &v2)
{
    if(v1 < v2) return -1;
    if(v2 < v1) return 1;
    return 0;
}
```

### 非模板类型参数

```cpp
// 非类型参数：
template<int a, int b>
void test()
{
	cout << a + b << endl;
}

template<unsigned M, unsigned N>
int compare(const char (&p1)[M], const char (&p2)[N])
{
	return strcmp(p1, p2);
}

test<3,4>();
//实例化如下版本：int compare(const char (&p1)[3], const char (&p2)[4])
compare("hi", "mom"); 
```
### 重载

```cpp
void fun(int a, float b)
{
	cout << "void fun(int a, float b)" << endl;
}

template<typename T>
void fun(T a)
{
	cout << "void fun(T a)" << endl;
}

template<typename T1, typename T2>
void fun(T1 a, T2 b)
{
	cout << "void fun(T1 a, T2 b)" << endl;
}

template<typename T1, typename T2, typename T3>
void fun(T1 a, T2 b, T3 c)
{
	cout << "void fun(T1 a, T2 b, T3 c)" << endl;
}

// 类似地可以将泛型T看作变量, 推导后的类型看作赋给变量的值
int main(int argc, char* argv[])
{
	int a = 0;
	float b = 0;
	fun(a); fun(b); // fun(T a)这里T分别被赋值为int和float
	fun(a,b);       // 优先匹配普通函数void fun(int a, float b)
	fun<>(a,b);     // 使用限定符<>时，编译器就会去匹配函数模板。
	int c = 0;
	fun(a, c);      // void fun(T1 a, T2 b) 这里T1、T2分别被赋值为int和int
	fun(a, b, c);   // void fun(T1 a, T2 b, T3 c)  这里T1、T2、T3分别被赋值为int、float和int
	return 0;
}

```

## 类模板

### 参数

* 类型参数：使用`typename`或者`class`
* 模板参数：使用`template<...> class XXX`的形式指示
* 非类型参数：整型常量（包括enum），或者指向外部链接的指针（包括函数指针、类成员函数指针，以及具有外部链接的字符串常量指针）
  
**类型参数**

```cpp
//类型参数: T,Container
// std::vector<T>是默认参数
template<typename T, typename Container = std::vector<T>>
struct Stack
{
	void push(const T& elem)
	{
		elems.push_back(elem);
	}

	T pop()
	{
		if (empty()) throw std::out_of_range("Stack<>::pop: empty!");

		auto elem = elems.back();
		elems.pop_back();
		return elem;
	}

	bool empty() const
	{
		return elems.empty();
	}

private:
	Container elems;
};
// 使用示例:
Stack<int, vector<int>> intStack;

```

**模板参数**
```cpp
// 类型参数T; 模板参数Container
/*
按照标准这里声明Container前的关键字只能是class，不能是typename。
*/
template<typename T, 
	     template<typename Elem, typename Allocator = std::allocator<Elem>> class Container = std::vector>
class Stack1
{
	void push(const T& elem)
	{
		elems.push_back(elem);
	}

	T pop()
	{
		if (empty()) throw std::out_of_range("Stack<>::pop: empty!");

		auto elem = elems.back();
		elems.pop_back();
		return elem;
	}

	bool empty() const
	{
		return elems.empty();
	}

private:
	Container<T> elems;
};

// 使用示例:
Stack1<int, vector> intStack;
```
其中n是带转换的数，base是使用的进制，缺省为十进制，也可以指定为十六进制和二进制。下面是读取二进制字符串，然后转换为十进制和十六进制显示的按钮和槽函数代码。

void Widget::on_btnBin_clicked()
{
    QString str = ui->editBin->text();
    bool ok;
    int val = str.toInt(&ok,2);

}

例如我们可以编写一个compare版本处理字符串字面常量。这种字面量是const char的数组，由于不能，拷贝一个数组，所以我们将自己的参数定义为数组的引用。由于我们希望能比较不同长度字符串字面常量，因此为模板定义了两个非类型的参数。第一个模板参数表示第一个数组的长度，第二个参数表示第二个参数的长度。
```cpp

templete<unsigned N, unsigned M>
int compare (contst char (&p1)[N], const char (&p2)[M]){
	return strcmp(p1,p2);
}

```
当我们调用这个版本的compare是：
	compare("hi","mom");
编译器会使用字面量的大小来替换N和M，从而实例化模板

身后我总听得，时光之神驾着有翼的马车飞驰而来，而在我面前遥遥横亘的，却是永恒无边的荒漠
——题记
时间是在青春轨道上行进的列车，在你还来不及回首的时候载满着喜怒悲欢，然后呼啸而过。
这样的季节，如有若无的伤感蛰伏在空气的每一个角落，只等着某个不经意的时刻钻进你的心里，留下无法言语的感觉。
驻足学校，我在无数消逝了的足迹中看到那个走过夏日的湖边，踩过秋天的满地黄叶，感受过冬日风雷的自我，当然还有那个埋首于书堆中，惘然于知之无涯的自我。
我感到自我的青春好像抓不住的流质，正从我的指缝间悄悄溜走。
泪在瞬间箭一般穿洞于我的身体。以往刚处于花季的年龄，却懵懵懂懂地干了许多傻事。
那时的我天真地以为，欢乐才是青春的本质。
然而何为快乐那时地定义仅有一个字：玩。
是的，和朋友在一起的时光固然很快乐，可与此同时，失去的也很多。当时光老人从我身边经过的时候，他必然会皱起眉头，而他得到的只是我们的不屑。直到此刻我才明白那是的肤浅，生命中止不住的空空空空空虚空虚空空空空空空空空空空空空空空空空空空空空间空间空间空间空间空间百度网盘百度网盘百度网盘当我们调用这个版本的compare时，编译器会使用字面常量的大小来替换和，从而实例化模板身后驻足学校字面值和朋友在一起的快乐时光