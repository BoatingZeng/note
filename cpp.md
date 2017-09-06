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
