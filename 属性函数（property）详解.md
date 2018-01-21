也有翻译为属性的，不过我认为翻译为属性函数更合理。它本质上类函数的一种装饰器。它可以实现两种主要功能：
- 将类方法转换为只读属性
- 重新实现一个属性的setter和getter方法
- 删除一个属性

功能实现上比较的简单，直接看代码。

### 将类方法转换为只读属性
```python
class C:
    def __init__(self):
        self._x = 'hello'

    @property
    def x(self):
        """I'm the 'x' property."""
        return self._x
c = C()
print(c.x)
# c.x = 'hello world!'
# AttributeError: can't set attribute

# 执行结果
# hello
```
从上面的示例可知，如果想通过一个类方法转换为只读属性只需要在对应的函数上添加`@property`装饰器即可。属性名为函数名。这里会有一个很有意思的问题，如果函数名和原有的属性同怎么办。我们来测试一下。

```python
class C:
    def __init__(self):
        self.x = 'hello'

    @property
    def x(self):
        """I'm the 'x' property."""
        return self.x
c = C()
print(c.x)

# Traceback (most recent call last):
#   File "xxx.py", line 12, in <module>
#     c = C()
#   File "xxx.py", line 6, in __init__
#     self.x = 'hello'
# AttributeError: can't set attribute
```

额~原来这样会覆盖原来的类中所定义的属性。所以这种情况应在程序中应该尽可能避免。

### 重新实现一个属性的setter和getter方法

#### getter

另外一个问题如果想在类函数中修改一下返回信息该怎么做呢？

```python

class C:
    def __init__(self):
        self._x = 'hello'

    @property
    def x(self):
        """I'm the 'x' property."""
        return self._x

    @x.getter
    def x(self):
        return self._x * 2
c = C()
print(c.x)

# 执行结果
# hellohello
```
我们可以看到`@x.getter`会覆盖原`x`属性的值获取方法。所以如果需要该原属性的获取方法可以通过`@属性名.getter`装饰器来装饰类函数来覆盖原获取属性值的方式。


#### setter

现在读数据没有问题了，还剩下一个写的问题。它的实现方法类似获取。`@属性名.setter`装饰器解决这个问题。

```python
class C:
    def __init__(self):
        self._x = 'hello'

    @property
    def x(self):
        """I'm the 'x' property."""
        return self._x

    @x.setter
    def x(self,value):
        self._x = value
c = C()
print('old value is', c.x)
c.x = 'hello world!'
print('new value is', c.x)

# 输出结果
# old value is hello
# new value is hello world!
```

### 删除一个属性

该操作比较少出现。装饰器定义和前面两个示例类似。`@属性名.deleter`,还是直接看代码比直观。
```python
class C:
    def __init__(self):
        self._x = None

    @property
    def x(self):
        """I'm the 'x' property."""
        return self._x

    @x.setter
    def x(self, value):
        self._x = value

    @x.deleter
    def x(self):
        del self._x

c = C()
print(dir(c))
del c.x
print(dir(c))

执行结果
# ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', '_x', 'x']
# ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'x']
```

可很明显的从执行结果发现。原属性`_x`已被删除。
>这里我们再开一个脑洞，如果删除的是`x`而不是`_x`会是什么样的结果呢？
从上面的程序来看，`del c.x`执行是`del self._x`.
那么修改为`del self.x`,本质是`del self.x` 和 `del c.x`是完全等价的，相当于自己对自己进行调用。而且没有退出条件。根据测试结果和猜想一样进入了无限循环。。卒
```python
# 执行结果
# del self.x
# RecursionError: maximum recursion depth exceeded
```
