https://docs.python.org/3/library/unittest.html

```python
import unittest

class TestStringMethods(unittest.TestCase):

    def test_upper(self):   # 测试用例
        self.assertEqual('foo'.upper(), 'FOO')

    def test_isupper(self):  # 测试用例
        self.assertTrue('FOO'.isupper())
        self.assertFalse('Foo'.isupper())

    def test_split(self):  # 测试用例
        s = 'hello world'
        self.assertEqual(s.split(), ['hello', 'world'])
        # check that s.split fails when the separator is not a string
        with self.assertRaises(TypeError):
            s.split(2)

if __name__ == '__main__':
    unittest.main()  #运行所有的测试用例
```

unittest supports some important concepts in an object-oriented way:

**test fixture**  测试脚手，测试代码的运行环境，指测试前后要做的工作，包括setUp和tearDown

   A test fixture represents the preparation needed to perform one or  more tests, and any associate cleanup actions. This may involve, for  example, creating temporary or proxy databases, directories, or starting a server process.

**test case** 测试用例

  A test case is the individual unit of testing. It checks for a specific response to a  particular set of inputs. unittest provides a base class, TestCase,  which may be used to create new test cases.

**test suite** 测试套件 多个测试用例test case集合就是TestSuite，TestSuite可以嵌套TestSuite

   A test suite is a collection of test cases, test suites, or both. It  is used to aggregate tests that should be executed together.

**test runner**  是来执行测试用例的，其中的run（test）会执行TestSuite/TestCase中的run(result)方法。

   A test runner is a component which orchestrates the execution of  tests and provides the outcome to the user. The runner may use a  graphical interface, a textual interface, or return a special value to  indicate the results of executing the tests. 

```python
import unittest
# setUp(), tearDown(), and __init__() will be called once per test.
class WidgetTestCase(unittest.TestCase):
    def setUp(self):  #在每个测试用例执行前执行
        self.widget = Widget('The widget')

    def tearDown(self):  #在每个测试用例执行完后执行
        self.widget.dispose()

    @classmethod
    def setUpClass(self):
    # 必须使用@classmethod 装饰器,所有test运行前运行一次
        print('setUpClass')

    def test_default_widget_size(self):   # 测试用例
        self.assertEqual(self.widget.size(), (50,50),
                         'incorrect default size')

    def test_widget_resize(self):   # 测试用例
        self.widget.resize(100,150)
        self.assertEqual(self.widget.size(), (100,150),
                         'wrong size after resize')
```



测试流程：

1. 写好TestCase：一个class继承unittest.TestCase，就是一个测试测试用例，其中有多个以test开头的方法，那么 每一个这样的，在load的时候会生成一个TestCase实例。如果一个class中有四个test开头的方法，最后load到suite中时则有四个测试用例

2. 由TestLoder加载TestCase到TestSuite

3. 然后由TextTestRunner来运行TestSuite，运行的结果保存在TextTestResult中。

说明：
1. 通过命令行或者unittest.main()执行时，main会调用TextTestRunner中的run来执行，或者可以直接通过TextTestRunner来执行用例

2. Runner执行时，默认将结果输出到控制台，我们可以设置其输出到文件，在文件中查看 结果，也可以通过HTMLTestRunner将结果输出到HTML)