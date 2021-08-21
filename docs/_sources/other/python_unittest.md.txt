# python unittest
包括：

* python内置的unittest和doctest  
* nose
* pytest

先处理unittest和doctest。nose和pytest以后有时间在比照研究。

### unittest
一些重要的概念：

* __test fixture__ 一个test fixture相当于一个或多个测试开始前需要做的工作，例如创建临时目录、开启服务进程之类的工作
* __test case__  一个test cast是一个独立单元测试，对特定的输入检查输出是否正常，unittest提供基类——TestCase，可以用来创建新的测试示例
* __test suite__ 一个test suite是一个test case、test suite或both的组合，将多个test聚合在一起一起执行
* __test runner__ 一个test runner是一个组件——用来执行测试，将输出提供给客户，the runner可能使用一个图形化接口、一个文本接口等。

#### Basic Sample
代码示例：   

	import unittest  
	class TestStringMethods(unittest.TestCase):   
	def test_upper(self):   
	    self.assertEqual('foo'.upper(), 'FOO')  
	def test_isupper(self):
	    self.assertTrue('FOO'.isupper())
	    self.assertFalse('Foo'.isupper())
	def test_split(self):
	    s = 'hello world'
	    self.assertEqual(s.split(), ['hello', 'world'])
	    # check that s.split fails when the separator is not a string
	    with self.assertRaises(TypeError):
	        s.split(2)
	if __name__ == '__main__':
		unittest.main()

`setUp()`和`tearDown()`方法允许你定义一个需要在方法执行前或后执行的操作
`python test.py -v`可以查看详细的一些信息
#### Re-using old test code
使用`unittest.FunctionTestCase`类，例如：

	testcase = unittest.FunctionTestCase(testSomething,
	                                 setUp=makeSomethingDB,
	                                 tearDown=deleteSomethingDB)

#### Skip test
支持跳过单独的某个测试方法，也支持标记一个测试为“expected failure”——这个测试将失败，但是不会被记录到TestResult中。

	class MyTestCase(unittest.TestCase):
	
	@unittest.skip("demonstrating skipping")
	def test_nothing(self):
	    self.fail("shouldn't happen")
	
	@unittest.skipIf(mylib.__version__ < (1, 3),
	                 "not supported in this library version")
	def test_format(self):
	    # Tests that work for only a certain version of the library.
	    pass
	
	@unittest.skipUnless(sys.platform.startswith("win"), "requires Windows")
	def test_windows_support(self):
	    # windows specific testing code
	    pass

`@unittest.expectedFailure`标识expectFailure。
几种skip类型：

* @unittest.skip(reason)
* @unittest.skipIf(condition, reason)
* @unittest.skipUnless(condition, reason)
* @unittest.expectedFailure



### doctest
通常的几种使用doctest方法：

* 用来检查模块的docstring是否更新（执行以下doc中描述的测试的例子）
* 用来回归测试程序是否达到预期
* 写大量输入输出示例来作为辅导文档

#### Usage
一般使用时，每个module后都加上：   
`
if __name__ == "__main__":    
    import doctest    
    doctest.testmod()     
`
    
用来检验这个module的docstring是否可行。

可以将docstring写入一个文本文件，用下面语句调用：   
`doctest.testfile("example.txt")`

> """>>> func(arg)   
> result"""   
> 一个docstring就是这么定义的,在注释中，而且要注意`>>>`和`func`中间一定要有个空格，Exception的细节可以用`...`来代替，而只需要写上Exception的前后两句即可

#### How It Works
##### Which Docstrings Are Examined?
当前模块的docstring和下面的所有函数、类、方法docstring都被执行，import的模块不包含在内。
##### How are Docstring Examples Recognized?
一般情况下docstring就相当于将交互式的python console的输出复制过去，但是稍稍有些不同。所有的输出都必须紧跟'>>> '   

* 如果输出全空行，则需要使用'<BLANKLINE>'来替换每一行。
* 所有tab字符串都会被替换成空格。输出的空格不被替换，（什么是hard tab？）

doctest是查找docstring来测试，由于对测试模块的global采用了浅拷贝，所以不会实际的修改global值，意味着docstring中可以自由的使用变量名，而且每一个docstring的名字定义都不能互相看到。

## nose
nose在过去一些年一直处于维护模式下，如果没有新人或新组来维护就有可能被停止。如果是新的项目，则应该考虑使用Nose2，py.test或unittest/unittest2.

## pytest
pytest框架让写小的测试程序变得更简单、方便，还不支持复杂的功能性测试。

	def func(x):
		return x + 1
	
	def test_answer():
		assert func(3) == 5

直接执行`pytest`命令

### install
`pip install -U pytest` 更新到最新（-U是更新到最新版本）

