# Pyhon的函数参数类型

Python中函数（function）或方法（method）的参数类型有那些，每种参数类型要怎么传参才能调用，默认参数要怎么设置才算合理。在Python有一个标准模块`inspect`，主要提供了四种用处：
- 对是否是模块，框架，函数等进行类型检查
- 获取源码
- 获取类或函数的参数信息
- 解析堆栈

### 有那几种参数类型？
- POSITIONAL_OR_KEYWORD 位置或比选参数
- VAR_POSITIONAL 命名关键次参数
- KEYWORD_ONLY 命名关键词参数
- VAR_KEYWORD 关键词参数
- POSITONAL_ONLY 位置参数（高版本已停用）

### POSITIONAL_OR_KEYWORD
如果没有任何`*`的声明，那么就是`POSITIONAL_OR_KEYWORD`类型的，如同语义一样，`POSITIONAL_OR_KEYWORD`类型的参数可以通过位置`POSITIONAL`传参调用，也可以通过`KEYWORD`传参。以下是一个最简单的例子：
```
def foo(a):
    pass
# 位置传参调用
foo(1)
# 关键字传参调用
foo(a=1)
```
### VAR_POSITIONAL
第二种是可变的位置参数，通过一个`*`前缀来声明，如果你看到一个`*XXX`的函数参数声明，那一定是属于`VAR_POSITIONAL`类型的，如同语义，这种类型的参数只能通过位置`POSITIONAL`传参调用，不支持关键字`KEYWORD`传参，在函数内部，`VAR_POSITIONAL`类型的参数以一个元组(`tuple`)显示，有一点需要注意的，`VAR_POSITIONAL`类型可以不传任何参数调用也不会报错，而且只允许存在一个。以下是一个简单的例子：
```
def foo(*b):
    print(b)
# 不传参数不会报错，参数值是一个空元组
foo() # 结果是()
foo(1, 2.0, '3', True) #结果是(1, 2.0, '3', True)
```

### KEYWORD_ONLY
第三种是关键字参数，这种参数只会在`VAR_POSITIONAL`类型参数的后面而且不带`**`前缀。如同语义，这类只能用关键子`KEYWORD`来传参，不可以用位置传参，因为位置传的参全让前面的`VAR_POSITIONAL`类型参数接收完了，所以`KEYWORD_ONLY`只能通过关键字才能接收到参数值。以下是一个简单的例子：
```
# VAR_POSITIONAL不需要使用时，可以匿名化
def foo(*, c):
   pass
# 只能关键字传参调用
foo(c=1)
```

### VAR_KEYWORD
第四种是可变的关键字参数，`VAR_KEYWORD`类型的参数通过`**`前缀来声明。如同语义，这种类型的参数只能通过关键词调用，但可以接收任意个关键字参数，甚至是0个参数，在函数内部以一个字典(`dict`)显示。`VAR_KEYWORD`类型的参数只允许有一个，只允许在函数的最后声明。以下是简单的例子：
```
def foo(**d):
    print(d)
# 不传参数不会报错，参数值是一个空字典
foo() #结果是{}
foo(a=1, b=2.0, c='3', d=True) # 结果是{'d':True, 'c':'3', 'b':2.0, 'a':1}
```

### POSITONAL_ONLY
第五种是位置参数，选择最后说这个，是因为它一点也不重要，属于python的历史产物，你无法在高版本的python中创建一个POSITIONAL_ONLY类型的参数，在某种底层的内置函数也许会使用这类型的参数，但我试用inspect模块也没法正确识别它的命名，但在Ipython的??帮助下，还是能看到Init signature: dict(self, /, *args, **kwargs)这里的self就是位置参数POSITIONAL_ONLY了。相信我，你不会需要用到它的，现在python推荐用VAR_POSITIONAL来代替它。第五种是位置参数，选择最后说这个，是因为它一点也不重要，属于_python_的历史产物，你无法在高版本的_python_中创建一个`POSITIONAL_ONLY`类型的参数，在某种底层的内置函数也许会使用这类型的参数，但我试用`inspect`模块也没法正确识别它的命名，但在Ipython的`??`帮助下，还是能看到`Init signature: dict(self, /, *args, **kwargs)`这里的`self`就是位置参数`POSITIONAL_ONLY`了。相信我，你不会需要用到它的，现在_python_推荐用`VAR_POSITIONAL`来代替它。

### 综合示例
```
import inspect
def foo(a, *b, c, **d):
   pass
for name, parame in inspect.signature(foo).parameters.items():
   print(name, ': ', parame.kind)
```

```
a : POSITIONAL_OR_KEYWORD
b : VAR_POSITIONAL
c : KEYWORD_ONLY
d : VAR_KEYWORD
```

### 默认参数

- VAR类型不允许设置默认参数
`POSITIONAL_OR_KEYWORD`和`KEYWORD_ONLY`可以自定义默认参数，而`VAR_POSITIONAL`和`VAR_KEYWORD`不允许自定义默认参数的，因为`VAR_POSITIONAL`的默认参数是`tuple()`空元祖，而`VAR_KEYWORD`的默认参数是`dict()`空字典。如果自定义了默认的参数的话，调用函数的时候可以不必传参，如果默认值是空的话，那就必须传参数才能调用。
- 默认参数的位置
`POSITIONAL_OR_KEYWORD`类型的默认参数一定要放在后面，否则会报错，`KEYWORD_ONLY`虽然没有强制要求，因为都是用关键字传参，谁先谁后无所谓，但最好还是尽可能的放在后面。
- 默认参数不默认？
默认参数绝对不能设置为可变类型（比如list、dict、set），如果你在函数内改变了默认参数，下次再调用时它就不再是默认值了。

正确示例：
```
def foo(p1, p2=2.0, *, k1, k2=None):
    a_list = k2 or list()
	pass
foo(1, k1='3')
```

### 接收参数的优先级
- 先接收`POSITIONAL_OR_KEYWORD`
- 再接收`KEYWORD_ONLY`
- 再接收`VAR_POSITIONAL`和`VAR_KEYWORD`,这两者没有交集

```
def foo(a, *b, c, **d):
    print(a, b, c, d, sep='\n')
	
# a: 1
# b: (2, '3')
# c: 3
# d: {'x':1, 'y':2}
```
