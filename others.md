# vscode的cpp插件设置MinGW

```json
{
    "name": "Win32",
    "intelliSenseMode": "clang-x64",
    "includePath": [
        "${workspaceRoot}/src",
        "${workspaceRoot}/thirdparty/boost/include",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include/c++",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include/c++/mingw32",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include/c++/backward",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include",
        "C:/MinGW/include",
        "C:/MinGW/lib/gcc/mingw32/6.3.0/include-fixed"
    ],
    "defines": [
        "_DEBUG",
        "UNICODE",
        "__GNUC__=6",
        "__cdecl=__attribute__((__cdecl__))"
    ],
    "browse": {
        "path": [
            "${workspaceRoot}/src",
            "${workspaceRoot}/thirdparty/boost/include",
            "C:/MinGW/lib/gcc/mingw32/6.3.0/include",
            "C:/MinGW/lib/gcc/mingw32/6.3.0/include-fixed",
            "C:/MinGW/include/*"
        ],
        "limitSymbolsToIncludedHeaders": true,
        "databaseFilename": ""
    }
}
```

# 机器学习

## 准确率、召回率、F1-score
对于数据测试结果有下面4种情况：

table   | 预测为正 | 预测为负
--------|----------|--------
实则为正 |   TP     |   FN
实则为负 |   FP     |   TN

* 准确率：P = TP/(TP+FP)，就是预测为正的有多少(百分比)是正，分母是上表第一列求和
* 召回率：R = TP/(TP+FN)，就是真实是正的预测出多少(百分比)，分母是上表第一行求和
* F1-score：F = 2PR/(P+R)

### 对于分词

分词问题转化为位置标注问题(通常是B、M、E、S标注)，所以评价就是通过标注的准确性来评价，每种标注都可以单独算出准确率和召回率，单独考虑每种标注，就可以套上面的公式了。下面是一个简化的测试文本(没有M标注)。第二列是正确答案，第三列是预测答案。看下面计算过程的注释部分。

```txt
这  S  S
是  S  S
一  B  B
个  E  E
测  B  S
试  E  S
文  B  B
本  E  E
```

```python
import pandas
line=[]
file=open('./result.txt','r',encoding='utf-8')
for i in file.readlines():
    i=i[0:-1]
    if len(i)!=0 and len(i)!=1:
        line.append(i.split())
df=pandas.DataFrame(line,columns=['character','train','test'])

print(df)

correct=df[df.train==df.test]

print(correct)

for i in ('B','E','S'):
    # 分别对B、E、S计算，下面以B为例注释
    # 分子：你预测到的而且是正确的B。分母：你预测到的全部B，就是上表的第一列求和。
    P=sum(correct.test==i)/sum(df.test==i)
    # 分子：一样。分母：正确答案里所有的B，就是上表的第一行求和。
    R=sum(correct.test==i)/sum(df.train==i)
    F=R*P*2/(R+P)
    print(i,':\n','P=',P,' R=',R,' F=',F)
```

计算结果：

table |  P  |  R  |  F
------|-----|-----|-----
B     | 1.0 |0.667| 0.8
E     | 1.0 |0.667| 0.8
S     | 0.5 | 1.0 | 0.667

## 维特比算法

参考吴军的《数学之美》

S -> x1 -> x2 -> xn

x1、x2、xn各有k1、k2、kn个节点。节点命名为xij，i=1~n，j=1~ki。

1. 计算S到x1的k1个节点**各自**的最短距离，就是要得出k1个距离(对于x1，直接就是最短距离，没有选择)
2. 计算S到x2的k2个节点**各自**的最短距离，不是比较x2的k2个节点之间的最短，而是以x2的k2个节点为终点，计算到它们的最短距离，也就是会计算出k2个距离。计算方法参考书上P231。
3. 递推，就得到从S到每一步xi的每一个节点xij的最短距离。这个已经是我们想要的结果。最短距离就是最后一步xn里面，距离最短的那个节点所表示的距离，路径只要每一步都记下来就行。

找到S到xi的各个节点的最短距离的意义在于，当我们从xi走到xi+1时，如果最短距离经过xi的某个节点xij，那么S到xij的距离肯定是S到xij的最短距离。这里并不是说一定要经过xij，而是如果要经过，那么它必须在所有S到xij的可能路径中最短。

把过程倒过来说，如果已经要从n-1步到n步(最后一步)，要求出最短距离，因为S到n-1步每个节点的最短距离已经得出，所以只要用这些节点往第n步的所有节点都走一次，做一个加法，再比较，就可以找出到第n步的最短距离。