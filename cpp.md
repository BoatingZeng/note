# 指针数组和数组指针

## 指针数组
```cpp
int *q[3]; //q是数组，数组元素是指针，3个int指针
```

## 数组指针
```cpp
int m[3][4]={0,1,2,3,4,5,6,7,8,9,10,11};
int (*p)[4]; //p是指针，指向一维数组,每个一维数组有4个int元素
int p[][4]; //这个和上面一样意思
```

example：
```cpp
#include <iostream>

using namespace std;

int main()
{
    int m[3][4]={0,1,2,3,4,5,6,7,8,9,10,11};
    int (*p)[4];
    p = &m[0];
    cout << (*p)[3]; //3
    return 0;
}
```

# const

## 指针使用const
1. 指针本身是常量不可变(const修饰指针本身)：`char * const pContent;`
2. 指针所指向的内容是常量不可变(const修饰所指变量)：`const char * pContent;`
3. 两者都不可变：`const char * const pContent;`

# 模版
编译器帮你做了替换的工作，编译器在编译时，把模版中的类型替换成和调用相应的类型，生成相应函数。是编译时的多态。在多文件编译链接时，声明和定义分开时，需要注意，看下面例子。

例子：
```cpp
//Example.h  
template<class T>  
class Test {  
    public:  
        Test(T a);  
};  
  
//Example.cpp  
#include "Example.h"  
  
template<class T>  
Test<T>::Test(T a) {  
    //do nothing...  
}  
  
template class Test<int>; //显式实例化

//main.cpp
//省略，使用了Test<int>
```

Test类的声明和定义不在同一个文件，如果没有Example.cpp最后一句的显式实例化，会出错。在编译Example.cpp时，编译器根本不知道在main.cpp中使用了`Test<int>`类（因为编译是各个文件独立进行的），也就没有实例化它。