### classmethod
classmethod类函中所定义的变量进行操作。它提供的是类中运行而不在实例中运行的方法。这里需要注意类和实例之间的关系。这里我做了一个示例，做类的实例计数，看完示例就会明白类只有一个。实例有千千万。
```python
class CClassmethod(object):
    ins_count = 0
    def __init__(self):
        CClassmethod.ins_count = CClassmethod.ins_count + 1

    @classmethod
    def insCount(cls):
        print(cls.ins_count)

    def __del__(self):
        CClassmethod.ins_count = CClassmethod.ins_count - 1

c1 = CClassmethod()
c1.insCount()
c2 = CClassmethod()
c2.insCount()

# 执行结果
# 1
# 2
```
在示例中我们可以看到`ins_count`变量不会因为实例的创建而被重置。它的数据结构是存储在类定义中的，而非实例中。其实被`@classmethod`所装饰的函数也不是跟着实例跑的。它是跟随你所定义类走的。它所传入的参数是一个类对象而非实例。


### staticmethod
staticmethod本质上和classmethod真的非常类似。它提供了和类功能相关但在运行时又不需要实例和类参与的方法（和classmethod的区别在于classmethod还要用到类里面的东西，而staticmethod不需要）。同样的是它的数据结构也是跟踪类走的。我们来看一个简单的示例：
```python
class CStatic(object):

    def __init__(self):
        pass

    @staticmethod
    def hello():
        print('Hello World!')

    def user_static(self):
        self.hello()

c = CStatic()

print('instance use:')
c.hello()
print('method use in instance:')
c.user_static()
print('static use:')
CStatic.hello()

# 执行结果
# instance use:
# Hello World!
# method use in instance:
# Hello World!
# static use:
# Hello World!
```