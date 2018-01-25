
### 包
- 目录包含含`__init__.py`文件，系统才会将其识别为包
- 默认包中模块是不可见的
- 在不修改`__init__.py`的情况下如果要使用模块，可以通过`from pkg import mod`的方式
- 在`__init__.py`中添加`from pkg import mod`可让外部访问包中所导入的模块
- `__call__`属性影响`from pkg import *`。它是一个队列，`*`对应这个队列中所包含的模块
- 在`__init__.py`中`.`和`..`分别标识当前和父级包。可通过`from . import mod`或`from .. import mod`方式分别导入当包或父级包中的模块

参考资料[https://docs.python.org/3/tutorial/modules.html#packages]

