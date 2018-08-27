# pyspark的使用技巧

## 如何改变dataframe某个column的数据类型

我们先看看我们手上数据的结构

```python
print(flightPerf.describe())

'''
Out: DataFrame[summary: string, date: string, delay: string, distance: string, origin: string, destination: string]
'''
```

现在题目的要求是，按来源城市(origin)的维度去统计飞机延误的总时间数。从数据的类型上看，delay是string类型，无法进行加减法的运算。因此我们需要实现一个类型的转化，具体实现如下：

```python
from pyspark.sql.types import *

flightPerf = flightPerf.withColumn("int_delay", flightPerf.delay.cast(IntegerType()))
flightPerf.head(5)

''' 
Out: 
[Row(date=u'01011245', delay=u'6', distance=u'602', origin=u'ABE', destination=u'ATL', int_delay=6),
 Row(date=u'01020600', delay=u'-8', distance=u'369', origin=u'ABE', destination=u'DTW', int_delay=-8),
 Row(date=u'01021245', delay=u'-2', distance=u'602', origin=u'ABE', destination=u'ATL', int_delay=-2),
 Row(date=u'01020605', delay=u'-4', distance=u'602', origin=u'ABE', destination=u'ATL', int_delay=-4),
 Row(date=u'01031245', delay=u'-4', distance=u'602', origin=u'ABE', destination=u'ATL', int_delay=-4)]
 '''
```

其中IntegerType是spark的整型格式，cast是SQL中用作格式转化的语句，withColumn则是用于增加不同名的列或替换同名的列

##### Reference: https://stackoverflow.com/questions/29383107/how-to-change-column-types-in-spark-sqls-dataframe

## sample函数的withReplacement的意思

sample函数用于对数据的抽样，下面为sample函数的定义

```python
sample(withReplacement, fraction, seed=None)
Returns a sampled subset of this DataFrame.
```

这里的withRepalcement的作用在于控制是否有放回的抽样，当withRepalcement设置为True时，那么抽样就是有放回的，否则就是无放回的。

举个例子：

不同重量的马铃薯：12, 13, 14, 15, 16, 17, 18

对这些马铃薯进行抽样两次，

- withReplacement = True

  产生的情况会有: (12,12), (12,13), (12, 14), (12,15), (12,16), (12,17), (12,18), (13,12), (13,13), (13,14), ...

- withReplacement = False

  产生的情况会有: (12,13), (12,14), (12,15), (12,16), (12,17), (12,18), (13,12), (13,14), (13,15), ...

##### Reference: https://web.ma.utexas.edu/users/parker/sampling/repl.htm

## split 多种情况

在数据处理的时候有时候会遇到下面的情况，需要对多种情况的字符串进行split操作

```python
text=["a b c", "d e", "f#g#h", "m n q", "r,q,w", "j%r%q"]
```

遇到这种情况我们可以使用Python的re.split方法来解决这个问题

```python
import re

re.split('[_#|]','this_is#a|test')

# Out
['this', 'is', 'a', 'test']

import re
text=["a b c", "d e", "f#g#h", "m n q", "r,q,w", "j%r%q"]
textRDD = sc.parallelize(text)
(
    textRDD
        .flatMap(lambda x: re.split('[\s|#|%|,]', x))
        .collect()
)

# Out
['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'm', 'n', 'q', 'r', 'q', 'w', 'j', 'r', 'q']
```

