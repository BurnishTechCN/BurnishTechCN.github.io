title: 使用 python mock 模拟单元测试
date: 2016-08-18 17:29:27
permalink: use-python-mock-to-run-unittest
category:
tags:
---

mock 是 python 中一个用于支持单元测试的库，它的主要功能是使用 mock 对象替代掉指定的 python 对象，以达到模拟对象的行为。

<!--more-->

## mock 的使用场景

* 假设你写的一个函数 `hello` 在处理数据的时候需要调用第三方的 API，而这个过程可能很长，这极大影响了单元测试的效率
* 假设你写的另一个函数 `hi` 需要发短信，调用打印机等等，跑单元测试还要连接打印机就非常没有必要了，所以这时候就需要 mock 掉调用打印机那一部分

## mock 的安装和导入

在 python 3.3 以前的版本需要另外安装 mock 模块

```bash
pip install mock
```

然后 import 就可以了

```python
import mock
```

从 python 3.3 开始，mock 模块已经被合并到标准库中，被命名为 unittest.mock，可以直接 import 进来使用：

```python
from unittest import mock
```

## Mock 对象
Mock 对象是 mock 模块中最重要的概念。Mock 对象就是 mock 模块中的一个类的实例，这个类的实例可以用来替换其他的 python 对象，来达到模拟的效果。

Mock对象的一般用法是这样的：

* 找到你要替换的对象，这个对象可以是一个类，或者是一个函数，或者是一个类实例。
* 然后实例化 Mock 类得到一个 mock 对象，并且设置这个 mock 对象的行为，比如被调用的时候返回什么值，被访问成员的时候返回什么值等等。
* 使用这个 mock 对象替换掉我们想替换的对象，也就是步骤 1 中确定的对象。
* 之后就可以开始写测试代码，这个时候我们可以保证我们替换掉的对象在测试用例执行的过程中行为和我们预设的一样。

下面是一个简单的示例，我们的一个函数需要发短信，而我们把发短信那个方法 mock 掉

下面是我们发短信的函数

```python
#!/usr/bin/env python
# coding=utf-8


def send_sms():
    pass


def func():
    result = send_sms()
    if result:
        return 'success'
    else:
        return 'fail'
```

下面是我们的单元测试代码，我们使用了一个我们自己构造的 mock 对象来替换 `sms` 里面的 `send_sms` 方法，使它的返回结果为 `True`

```python
#!/usr/bin/env python
# coding=utf-8

import unittest

import mock

import sms


class SMSTest(unittest.TestCase):
    def test_sms(self):
        sms.send_sms = mock.Mock(return_value=True)
        result = sms.func()
        self.assertEqual(result, 'success')


if __name__ == '__main__':
    unittest.main()
```

运行结果：

```bash
.
--------------------------------------------
Ran 1 test in 0.000s

OK
```

### patch 和 patch.object

在了解了 mock 对象之后，我们来看两个方便测试的函数：patch 和 patch.object。这两个函数都会返回一个 mock 内部的类实例，这个类是 class _patch。返回的这个类实例既可以作为函数的装饰器，也可以作为类的装饰器，也可以作为上下文管理器。使用 patch 或者 patch.object 的目的是为了控制 mock 的范围，意思就是在一个函数范围内，或者一个类的范围内，或者 with 语句的范围内 mock 掉一个对象。

下面我们使用 patch 装饰器来重写上面的示例：

```python
class SMSTest(unittest.TestCase):
    @mock.patch('sms.send_sms')
    def test_sms(self, mock_sms):
        mock_sms.return_value = True
        result = sms.func()
        self.assertEqual(result, 'success')
```

mock 掉 `sms.send_sms`，我们传入的参数 `mock_sms` 就是那个 mock 对象

使用 patch.object 的效果是一样的，不过有一点区别：

```python
class SMSTest(unittest.TestCase):
    @mock.patch.object(sms, 'send_sms')
    def test_sms(self, mock_sms):
        mock_sms.return_value = True
        result = sms.func()
        self.assertEqual(result, 'success')
```

就是替换掉一个对象的指定名称的属性，用法和 setattr 类似。

*注意：如果使用了多个 `mock.patch` 装饰器，注意装饰器和传入参数的对应：最里面的装饰器对应第一个参数，下面有一个示例：*

```python
@mock.patch('a.sys')
@mock.patch('a.os')
@mock.patch('a.os.path')
def test_something(self, mock_os_path, mock_os, mock_sys):
    pass
```

*然后实际情况是这样调用的*

*`patch_sys(patch_os(patch_os_path(test_something)))`*

*也就是说是相反的*

*由于这个关于 sys 的在最外层，因此会在最后被执行，使得它成为实际测试方法的最后一个参数。请特别注意这一点，以保证在测试的时候保证正确的参数按照正确的顺序被注入。*

patch 的关键在于 mock 掉正确的对象，所以 patch 里面的那个对象地址很重要。

假设我们的工程有下面的结构：

```bash
a.py
    -> 定义了一个类 SomeClass

b.py
    -> from a import SomeClass
```

如果我们需要把 `SomeClass` mock 掉，这时候我们在单元测试里 `import b`，这时候我们应该使用 `@patch('b.SomeClass')`

如果 `b.py` 里的定义是 `import a`，那这时候就应该是 `@patch('a.SomeClass')`


## 参考链接
* python mock 官方文档：[https://docs.python.org/3/library/unittest.mock.html](https://docs.python.org/3/library/unittest.mock.html)
* [https://docs.python.org/3.4/library/unittest.mock-examples.html](https://docs.python.org/3.4/library/unittest.mock-examples.html)
