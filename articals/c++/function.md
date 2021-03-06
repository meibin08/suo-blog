# 函数
### 函数基础：兼容C
- 典型的函数：返回类型，函数名字，0个或多个形参组成的列表，函数体
- 函数调用：实参初始化形参，控制权转移给被调函数
- 实参与形参：实参是形参的初始值，实参类型必须与形参类型匹配，至少可以隐式的转换成形参
- 形参列表：可以为空，但不能省略，为了与c兼容加上void，任何形参都不能同名，而且外层作用域的局部变量不能与形参同名
- 返回类型：大多数类型都能作为函数返回类型，void是特殊的返回类型，表示不返回任何值，不能是数组或者函数类型，但可以是它们的指针
- 局部对象：名字有作用域，对象有生命周期，形参和函数体内部都变量都是局部变量，仅在函数作用域可见
- 自动对象：只存在于块执行期间的对象称自动对象
- 局部静态对象：将局部变量定义成static类型，在执行路径第一次经过对象定义语句初始化，程序结束后被销毁
- 函数声明：函数只能定义一次，但可以声明多次，用分号代替函数体，也称函数原型，记得在头文件中声明
- 分离式编译：编译和链接多个文件

### 参数传递：传值和引用
- 传值参数：初始值拷贝给对象，对变量改变不会影响初始值，指针形参可以间接访问他所指向的对象，可以修改对象的值
- 传引用参数：通过引用形参，允许函数改变一个或者多个实参的值

```
#include <iostream>
void swap(int &a, int &b){
	int tem = a;
	a = b;
	b = tem;
}   //传引用
int main(void){
	int c = 1, d = 2;
	swap(c, d);
	std:: cout << c << d << std:: endl;
}    // 结果：21
```

- - 使用引用可以避免拷贝，拷贝大的类型或者容器对象比较低效
- - 使用引用参数返回额外的信息，一个函数只能返回一个值，引用给我提供了解决方法

```
//返回指定字符串中指定字符的位置和出现的次数
string::size_type find_char(const string &s, char c, string::size_type &occurs){
    auto ret = s.size();
    occurs = 0;
    for(decltype(re))
}
```
- - const形参和实参：同样的规则应用到形参上

```
int b;
void reset(int &a);   reset(b);//参数为引用,只能用int对象，不能用字面量，需要转换的对象，const int
void reset(int *a);   reset(&b);//参数为指针，只能用int *对象
//允许字面量初始常量引用

```
- - 尽量用常量引用：把函数不会改变的形参定义称引用是一种比较常见的错误，给调用者误导这是可以修改的，同时限制了实参的类型
- - 数组形参：不允许拷贝数组，只能通过指针形式来传值

```
//比c语言多一样
 void print(const int *);  
 void print(const int []);
 void print(const int [10]); //期望的长度，不一定
 //通过显示传递参数来表示数组的大小
 void print(const int [], size_t );
 //数组引用形参
 void print(int (&arr)[10]);
 //多维数组
 void print(int (*mat)[10], int row);
 void print(int mat[][10], int row);
```
### 返回类型

### 函数重载

### 特殊用途
- 默认实参：有一种形参很多次调用都赋予相同的值，这样的值称为默认实参

```
//默认实参的形式，直接在函数中给形参赋值,一旦某一形参被赋予了值，后面所有形参都必须赋默认值
string screen(sz ht = 24, sz wid = 80, char background = '');
//使用默认实参，默认实参按位置解析，负责填写缺少的尾部实参，只能是尾部的
//调用必须赋值赋满所有的形参
window = screen();   ==> 24, 80, ''
window = screen(66); ==> 66, 80, ''
window = screen(66, 256); ==> 66, 256, ''
window = screen( , ,'a'); ==> 错误，只能省略尾部的，从第一位开始填写的
//通常声明放头文件中，函数只声明一次，可以声明多次但形参不能重复赋值
//默认实参是可以初始化的，必须在函数之外进行
sz sw = 80;
string screen(sz ht = 24, sz wid = sw, char background = '');
```

- 内联函数：避免调用函数开销，在每个调用点内联的展开，优化规模较小，流程直接，频繁调用的函数

```
inline const string & shorterString(const string &s1, const string &s2){
    return s1.size() <= s2.size() ? s1 : s2;
}
cout << shortrtString(a,b) << endl;
==> cout << s1.size() <= s2.size() ? s1 : s2 << endl; //内联的展开 
```
- - 用函数的内联取代宏原因
```
1）宏代码最大的缺点容易出错  
2）宏无法进行调试  
3）宏无法操作类的私有成员  
```
- - 函数内联的工作原理：将内联函数的返回值，形参，函数放入符号表，没有错误则将代码放入符号表，发现正确调用后直接替换调用语句  
- - 优点：==具备宏的效率，增加了安全型，又可以自由的操作类的成员==，编译器可以根据上下文对代码进行深优化  
- - 注意：  
```
1）inline必须与函数定义放一起才算真正的内联（声明时没多大必要加inline）  
2）定义在类内部的函数会自动变成内联函数
3）定义有必要放头文件，这样每个文件都可以访问到，否则每个源文件都有从新定义它 
4）慎用内联，函数只有10行甚至更小时才用内联，内联会增加代码的长度，复杂的代码增加更多的执行开销和内存消耗  
5）内联的声明只是请求，是否使用取决于编译器，太长的函数编译器可能拒绝使用内联  
```
- constexpr:能用于常量表达式的函数，函数返回类型和形参必须都是字面量类型，函数有且只有一个return语句

```
constexpr int new_st(){ return 42; } //常量函数
constexpr int foo = new_st(); //常量表达式
//被隐式的指定为内联函数
//允许函数返回值并非常量
```

### 函数匹配

### 函数指针