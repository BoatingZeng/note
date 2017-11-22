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
