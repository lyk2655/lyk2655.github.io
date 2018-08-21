[简书](https://www.jianshu.com/p/c95e73200887)
[github](https://github.com/lyk2655/lyk2655.github.io/blob/master/notes/Python_string&datetime.md)

使用Python库中的`datetime`实现字符串string和datetime之间的格式转化

 ### 将时间转化为特定格式地字符串：
```
str = dateNow.strftime("%Y%m%d %H:%M:%S")
```
### 将特定格式的字符串转化为时间
```
date2 = datetime.datetime.strptime(str, "%Y%m%d %H:%M:%S")
```

### 完整代码
```
#!/usr/bin/env python
# -*- coding:utf-8 -*-

# @Author   : linyk3
# @E-Mail   : linyk3@126.com.com
# @GitHub   : https://github.com/lyk2655
# @Blog     : https://www.jianshu.com/u/4242c25feb84

import datetime

# 获取当前时间
dateNow = datetime.datetime.now()
print("当前时间:", dateNow)

# 将datetime转化为特定格式的字符串
str = dateNow.strftime("%Y%m%d %H:%M:%S")
print("将datetime转化为特定格式的字符串[%Y%m%d %H:%M:%S]:", str)

# 将特定格式的字符串转化为datetime
date2 = datetime.datetime.strptime(str, "%Y%m%d %H:%M:%S")
print("将特定格式的字符串转化为datetime:", date2)
```
![执行结果](https://upload-images.jianshu.io/upload_images/2833964-c0a4a79a3229a9a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

