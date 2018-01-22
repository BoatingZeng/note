# python基础

dict的get和iteritems：
```py
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
d['Thomas'] # 报错，这个跟js不同
d.get('Thomas', -1) # 返回-1，第二个参数是key不存在时的返回值
d.items() # 返回[(key, value)]
d.iteritems() # 跟上面差不多，但它返回迭代器
```

sorted函数和dict：
```py
import operator
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
sorted(d.iteritems(), key=operator.itemgetter(1))
# 返回一个list，按照d的value排序，默认是从小到大排
# [('Bob', 75), ('Tracy', 85), ('Michael', 95)]
```

*args：
表示不定参数，以zip()函数为例，zip()接受不定参数：
```py
xyz = [(1, 4, 7), (2, 5, 8), (3, 6, 9)]
zip(*xyz) == zip((1, 4, 7), (2, 5, 8), (3, 6, 9)) # *号把xyz这个数组拆开成三个参数了
```

在函数调用中使用*list/tuple的方式表示将list/tuple分开，作为位置参数传递给对应函数（前提是对应函数支持不定个数的位置参数）

# pip源
-i https://mirrors.aliyun.com/pypi/simple/