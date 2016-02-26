print("你好")  
这个编译运行会出现
File "D:\Python\test5.py", line 2
SyntaxError: Non-ASCII character '\xe4' in file D:\Python\test5.py on line 2, but no encoding declared; see http://www.python.org/peps/pep-0263.html for details
[Finished in 0.1s with exit code 1]

这种错误
这是由中文字符串引起的，加上  # -*- coding: utf-8 -*-  这一句就没问题了，如下：
```
# -*- coding: utf-8 -*-  
print("你好")  
```
比较奇怪的是直接在python的IDLE编辑器上，不加那一句注释，也能正确运行。
