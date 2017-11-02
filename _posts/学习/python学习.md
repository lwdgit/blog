1. 单双引号等价
2. 三引号用来输入多行文字
3. 数组可以直接相加
4. Set比js强大，支持计算交集，并集
```

向集合中添加元素：
In [22]:
s.add(1)
s
Out[22]:
{1, 2, 3, 4}
集合的交：
In [23]:
a = {1, 2, 3, 4}
b = {2, 3, 4, 5}
a & b
Out[23]:
{2, 3, 4}
并：
In [24]:
a | b
Out[24]:
{1, 2, 3, 4, 5}
差：
In [25]:
a - b
Out[25]:
{1}
对称差：
In [26]:
a ^ b
Out[26]:
{1, 5}
```



from numpy import array




Issues: https://stackoverflow.com/questions/15763497/importerror-cannot-import-name-array-when-importing-urllib2

5. 元组是不可变的
 为什么需要元组？
 旧式字符串格式化中参数需要用元组 s = "some numbers:" x = 1.34 y = 2 t = "%s %f %d " % (s, x, y)
 字典中当作键值
 数据库的返回值
 速度比列表快一个数量级


6. 导入方法

from numpy import *
import numpy
import numpy as np
from numpy import array, sin