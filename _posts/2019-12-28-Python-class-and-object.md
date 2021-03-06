---
layout: post
title: Python类和对象
date: 2019-12-28 14:00:00 +0900
categories: [能工巧匠集, Python]
tags: [class, python, function]
---

[<<8.错误和异常](../Errors_And_exceptions)---[10.标准库简介>>](../Python-Brief-Tour-of-the-Standard-Library-1)

## 9.类和对象

类提供一种将数据和功能绑定的方式，创建一个新类就可以创建一种新的对象类型，从而允许创建这种类型的新实例。每个类实例都可以附加属性来维护其状态。类实例还可以拥有用于修改其状态的方法 (由它的类定义)。

与其他编程语言相比，Python类的机制增加了最少新语法和语义的类，它是C++和Modula-3类机制的混合。Python类提供面向对象编程的所有标准特征：类的继承机制允许有多个基类、派生类可以重载基类的任何方法、而类的方法可以调用具有相同名字的基类的方法。对象可以包含任意数量和类型的数据。与模块一样，类也具有Python的动态特性：它们在运行时创建，创建后可进一步被修改。

在C++术语中，类成员 (包括数据成员) 通常是公有的 (除了下面的[私有变量 (Private Variables)](https://docs.python.org/3.8/tutorial/classes.html#tut-private))，而且所有成员函数为虚函数。与在Modula-3中一样，没有从对象的方法中引用对象成员的简短方法：方法函数使用一个显式的第一个参数代表对象本身的参数来定义，这个参数由调用隐式提供。与Smalltalk一样，类本身就是对象，这提供导入和重命名的术语。不像C++和Modula-3，Python内建类型可供用户作为基类进行拓展。同样，跟C++一样，大多数具有特殊语法的内置操作符 (算术操作符、下标等) 可被类实例重定义。

（由于缺乏普遍接受的术语来讨论类，我会偶尔采用Smalltalk和C++方面的术语。我将使用Modula-3方面的术语，因为它的面向对象的语法比C++的更接近Python的语法，但我希望很少数读者已经听说过它。)

## 9.1. 有关名称和对象的一个单词 (A Word About Names and Objects)

对象具有个体性，并且可以对相同的对象绑定多个名称 (在多个范围)。这在其他语言中这称为别名。在第一次接触Python时，这一点通常不会得到重视，并且在处理不可变的基本类型 (数组 （**numbers???**）、字符串、元组) 时，可以安全地忽略这一点。然而，在涉及可变对象 (比如列表、字典和其他大多数类型) 时，别名对Python代码的语义有惊人的效果。这通常用于方便编程，因为别名在某些方面表现得跟指针一样。比如，传递一个对象是简单的，因为只有一个指针通过实现传递。如果一个函数要改变作为参数传递过来的对象，这样调用者可以看到所发生的改变 — 这可以消除像在Pascal的传递机制中传递两个不同参数的需要。

## 9.2. Python作用域和名称空间 (Python Scopes and Namespaces)

在介绍类之前，我首先要告诉你一些关于Python的作用域规则。类定义对名称空间进行了一些巧妙的处理，并且你需要了解作用域和名称空间是怎么起作用，以便充分地理解所发生的事。顺便提及，关于这个主题的知识对任意一名高级Python程序员都非常有用。

让我们从一些定义开始。

名称空间是名称到对象的映射。目前，大多数的名称空间实现为Python字典，但是通常用任何的方式都不会引起注意 (除了性能之外)，而且在将来可能会发生变化。名称空间的例子有：一组内置名称 (包含 [`abs()`](https://docs.python.org/3.8/library/functions.html#abs) 等函数和内置异常名)、一个模块中的全局名称以及函数调用的本地变量名。在某种意义上，对象的属性的集合同样会形成名称空间。关于名称空间，需要了解的重要一点是，不同名称空间中的名称之间绝对没有关系。比如，两个不同的模块中都定义一个`maximize`函数而不会混淆 — 模块使用者必须用模块名给它添加前缀。

顺便说一下，我对句点后面紧跟的任何名称都使用了属性这个词 — 例如，在表达式`z.real`中，`real`是对象`z`的一个属性。严格来讲，模块中名称的引用就是属性引用：在表达式`modname.funcname`中，`modname`是一个模块对象，而`funcname`是它的一个属性。在这种情况下，模块中的属性刚好直接映射到模块中定义的全局名称 (In this case there happens to be a straightforward mapping between the module's attributes and the global names defined in the module)：它们共享相同的名称空间。

属性可以是只读或可写的。在后一种情况下，对属性赋值是可以的。模块的属性是可写的：你可以这样写`modname.the_answer = 42`。可写的属性也可以使用 [`del`](https://docs.python.org/3.8/reference/simple_stmts.html#del) 语句来删除，比如，`del modname.the_answer`会从名称为`modname`的对象中移除属性`the_answer`。	

名称空间在不同的时刻被创建，并且有不同的生命周期。在Python解释器启动时，包含内置名称的名称空间就被创建，而且永远不会被删除。当模块的定义被读入的时候，模块的全局名称空间就会被创建，通常，模块的名称空间持续到解释器退出。由解释器顶层调用执行的语句 (无论是从脚本文件读取的还是交互执行的) 被当成称为 [`__main__`](https://docs.python.org/3.8/library/__main__.html#module-__main__) 模块的一部分，因此它们有自己的名称空间。 (内置的名字实际上也是生存与模块之中，这就是所谓的[内置 (`builtins`)](https://docs.python.org/3.8/library/builtins.html#module-builtins)。)

函数的局部名称空间在函数调用时被调用，并且在函数返回或抛出在函数内未处理的异常时删除。 (实际上，用遗忘来描述实际发生的事情会更好。) 当然，递归调用都有自己的局部名称空间。

作用域是Python程序的文本区域，在这里可以直接访问名称空间。**“直接访问”**在这里意味着对名称的为限定引用尝试在名称空间中查找名称。

尽管作用域是静态确定的，但它们是动态使用的。在执行过程的任何时刻，至少有3处嵌套的作用域，它们的名称空间可以直接访问：

- 最里面的作用域，包含局部变量，首先被搜索。

- 任意封装函数的作用域，包含既不是局部也不是全局的名称，在最近的封装范围开始搜索。
- 倒数第二个作用域包含当前模块的全局名称。
- 最外层的作用域是包含内置名称的名称空间，它最后被搜索。

如果名称声明为全局的，那么所有的引用和赋值都直接指向包含模块全局名称的中间作用域。要重新绑定在最内层作用域外面的变量，可以使用 [`nonlocal`](https://docs.python.org/3.8/reference/simple_stmts.html#nonlocal)语句，如果没有声明为非局部变量，那些变量是只读的 (对这样一个变量进行写操作，只会简单地在最内部作用域中创建简单地生成一个新的局部变量，使得外部的同名变量并不会改变)。

通常，局部作用域引用当前函数的局部名称。在函数外面，局部作用域引用与全局作用域相同的名称空间：模块的名称空间。类定义为局部作用域提供另一个名称空间。

重要的是要认识到作用域是文本确定的: 在模块中定义的函数的作用域是模块的名称空间，无论这个函数是在哪、以什么别名调用。另一方面，实际的名称搜索是动态完成的，在运行时—然而，语言的定义向静态命名方案演化，在编译时，因此，不要依赖于动态命名方案！(On the other hand, the  actual search for names is done dynamically, at run time — however, the language  definition is evolving towards static name resolution, at “compile” time, so  don’t rely on dynamic name resolution! ) (实际上，局部变量已经是静态确定的。)

Python的特殊之处是：如果没有 [全局 (`global`)](https://docs.python.org/3.8/reference/simple_stmts.html#global) 或 [非局部 (`nonlocal`)](https://docs.python.org/3.8/reference/simple_stmts.html#nonlocal)  语句影响，对名称赋值经常进入最内层作用域 (if no global or nonlocal statement is in effect - assignments to names always go into the innermost scope)。赋值不会复制数据，它们仅仅是将名称绑定到对象。删除也是如此：`del x` 语句从局部作用域的名称空间移除对`x`的绑定。实际上，所有引入新名称的操作都使用局部作用域 (In fact, all operations that introduce new names use the local scope)：特别是，[`import`](https://docs.python.org/3.8/reference/simple_stmts.html#import) 语句和函数定义将模块或函数名绑定到局部作用域中。

[全局 (`global`)](https://docs.python.org/3.8/reference/simple_stmts.html#global) 语句可以被用来指明特定的变量存在于全局作用域，并且应该在全局作用域内重新绑定；[非局部 (`nonlocal`)](https://docs.python.org/3.8/reference/simple_stmts.html#nonlocal) 语句指明特定的变量存在于一个封装的作用域，并且在封装的作用域内重新绑定。

### 9.2.1.作用域和名称空间举例 (Scopes and Namespaces Example)

这个例子阐述如何引用不同的作用域和名称空间，以及 [全局 (`global`)](https://docs.python.org/3.8/reference/simple_stmts.html#global) 和 [非局部 (`nonlocal`)](https://docs.python.org/3.8/reference/simple_stmts.html#nonlocal) 如何影响变量的绑定：

```python
def scope_test():
	def do_local():
		spam = "local spam"
		
	def do_nonlocal():
		nonlocal spam
		spam = "nonlocal spam"
	
	def do_global():
		global spam
		spam = "global spam"
        
    spam = "test spam"
    do_local()
    print("After local assignment:", spam)
    do_nonlocal()
    print("After nonlocal assignment:", spam)
    do_global()
    print("After global assignment:", spam)

scope_test()
print("In global scope:", spam)
```

例子中代码的输出是：

```python
After local assignment: test spam
After nonlocal assignment: nonlocal spam
After global assignment: nonlocal spam
In global scope: global spam
```

注意*局部 (local)* 赋值 (这个是默认的) 并不会改变 *scope_test* 中`spam`的绑定。[非局部 (`nonlocal`)](https://docs.python.org/3.8/reference/simple_stmts.html#nonlocal)  赋值改变 *scope_test* 中`spam`的绑定，以及 [全局 (`global`)](https://docs.python.org/3.8/reference/simple_stmts.html#global) 赋值改变模块级别的绑定。

你也可以看到在 [全局 (`global`)](https://docs.python.org/3.8/reference/simple_stmts.html#global) 赋值之前没有对`spam` 提前绑定。

## 9.3. 初识类 (A First Look at Classes)

类引入了一些新语法、三种新对象类型和一些新语义。

### 9.3.1. 类定义的语法 (Class Definition Syntax)

最简单的类定义形式长这样：

```python
class ClassName:
	<语句 1>
	.
	.
	.
	<语句 N>
```

与函数定义一样，类的定义必须在它们产生影响之前执行 ([`def`](https://docs.python.org/3.8/reference/compound_stmts.html#def) 语句)。（可以想象，你可以将类定义放在一个 [`if`](https://docs.python.org/3.8/reference/compound_stmts.html#if) 语句分支或者一个函数中。）

在实践中，类定义中的语句通常为函数定义，但是其他语句也是允许的，并且有时是有用的，我们稍后对此进行讨论。类中函数的定义通常有特殊形式的参数列表，由方法的调用约定决定，同样，稍后将对此进行解释。

当输入类定义时，一个新的名称空间就创建了，这个名称空间被用作局部作用域。因此，对局部变量的所有赋值都将进入这个新的名称空间。特别地，函数定义在这里绑定新函数的名称。

当类的定义被正常地留下来 (通过末尾)，一个类对象就被创建了。这基本是对类定义创建的名称空间内容的封装；在下一节，我们将会学习更多关于类对象的内容。原始的局部作用域 (在输入类定义之前生效的作用域) 被恢复，并且，类对象在这里被绑定到在类定义头中给定的类名 (在例子中是`ClassName`)。

### 9.3.2. 类对象 (Class Objects)

类对象支持两种操作：属性引用和实例化。

属性引用使用Python中所有属性引用的标准语法：`obj.name`。当类对象创建时，有效的属性名是类名称空间的所有名称。因此，如果类的定义如下：

```python
class MyClass:
	"""一个简单的类示例"""
	i = 12345
	
	def f(self):
		return 'hello world'
```

那么`MyClass.i`和`MyClass.f`是有效的属性引用，分别返回一个整数和函数对象。类的属性可以被赋值，因此你可以通过赋值来改变`MyClass.i`的值。`__doc__`也是一个有效属性，返回类的文档字符串：`"一个简单的类示例".`

类实例化使用函数调用，类对象可以看成是一个可以返回类新实例的无参数函数，比如 (假设有上面定义的类)：

```python
x = MyClass()
```

创建一个类新实例并将这个对象赋值给局部变量`x`。

类实例化操作 (调用一个类对象) 创建一个空对象。很多类喜欢通过自定义特地的初始状态来创建对象。因此，一个类可以定义一个特殊的方法，命名为 [`__init__()`](https://docs.python.org/3.8/reference/datamodel.html#object.__init__)，像这样：

```python
def __init__(self):
	self.data = []
```

当类定义了 [`__init__()`](https://docs.python.org/3.8/reference/datamodel.html#object.__init__) 方法时，类实例化会自动为新创建的类实例调用 [`__init__()`](https://docs.python.org/3.8/reference/datamodel.html#object.__init__)  。因此，在这个例子中，一个初始化的类新实例通个以下代码获得：

```python
x = MyClass()
```

当然，为了有更好的灵活性，[`__init__()`](https://docs.python.org/3.8/reference/datamodel.html#object.__init__) 方法可以包含参数。在那样的情况，给类实例化操作的参数被传递给[`__init__()`](https://docs.python.org/3.8/reference/datamodel.html#object.__init__)，比如：

```python
>>> class Complex:
...		def __init__(self, realpart, imagpart):
...			self.r = realpart
...			self.i = imagpart
...
>>> x = Complex(3.0, -4.5)
>>> x.r, x.i
(3.0, -4.5)
```

### 9.3.3.实例对象 (Instance Objects)

那么我们现在可以使用实例对象做些什么呢？可被实例对象理解的唯一操作是属性引用，有两类有效的属性名：数据属性和方法。

*数据属性*对应于Smalltalk的“实例变量”和C++中的“数据成员”。与局部变量一样，数据属性无需声明，当它们第一次被赋值时自动出现。比如，如果`x`是上面定义的`MyClass`类的实例，下面的代码片段将会打印值`16`而不会留下任何痕迹：

```python
x.counter = 1
while x.counter < 10:
	x.counter = x.counter * 2
print(x.counter)
del x.counter
```

另一种实例属性引用是 *方法*。方法是属于对象的函数。(在Python中，方法这个术语并不是类实例所独有的，其他类型的对象也有方法。比如，列表对象有`append`、`insert`、`remove`和`sort`等方法。然而，在下面的讨论中，我们将使用方法这个术语来表示类实例对象的方法，除非另有明确说明。)

实例对象的有效方法名称取决于它的类。根据定义，类所有的函数对象属性定义了其实例的相应的方法。因此，在我们的例子中，`x.f`是有效的方法引用，因为`MyClass.f`是函数。但是`x.i`不是有效的方法引用，因为`MyClass.i`不是函数。然而，`x.f` 不等于`MyClass.f，它 (指`x.f`) 是方法对象而不是函数对象。

### 9.3.4.方法对象 (Method Objects)

通常，方法被绑定后立即调用：

```python
x.f()
```

在`MyClass`这个例子中，这将返回字符串`'hello world'`。然而，没有必要马上调用方法： `x.f`是方法对象，可以将其存储并在以后调用。比如：

```python
xf = x.f
while True:
	print(xf())
```

会持续打印`hello world`直到时间结束。

当方法被调用时，实际上会发生什么？你也可能已经注意到，虽然`f()`函数定义指定了一个参数，但是`x.f()`的调用没有任何参数。这个参数发生了什么？毫无疑问，当果需要参数的函数被调用而没有提供参数时，则Python会抛出异常 — 即使该参数实际上不会用到。

实际上，你可能已经猜到了答案：方法特殊之处在于实例对象作为函数的第一个参数传递。在我们的例子中，调用`x.f()`等价于`MyClass.f(x)`。通常，用n个参数的列表调用方法等价于用一个参数列表调用对应的函数，这个参数列表是通过在传递过来的参数列表第一个参数前插入方法的实例对象来创建的。

如果你依然不明白方法是如何工作的，看一眼实现可能清楚怎么回事。当引用实例的非数据属性时，将搜索实例的类。如果这个名称表示一个有效的类属性，它是一个函数对象，那么方法对象是通过将实例对象和函数对象打包 (指针指向) 在一个抽象对象中创建的：这就是方法对象 (a method object is created by packing (pointers to) the instance object and the function object just found together in an abstract object: this is the method object)。当使用参数列表调用方法对象时，将从实例对象和传递过来的参数列表创建一个新的参数列表，并且使用这个新的参数列表调用函数对象。

### 9.3.5.类和实例变量 (Class and Instance Variables)

一般来说，实例变量是用于独立于每个实例的数据，而类变量是用于类所有实例共享的属性和方法。

```python
class Dog:

	kind = 'canine'			# 所有实例共享的类变量
	
	def __init__(self, name):
		self.name = name	# 独立于每个实例的实例变量
		
>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.kind					# 由所有的狗共享
'canine'
>>> d.name					# d 独有
'Fido'
>>> e.name					# e独有
'Buddy'
```

正如在[有关名称和对象的一个单词 (A Word About Names and Objects)](https://docs.python.org/3.8/tutorial/classes.html#tut-object) 中所讨论的那样，共享数据在涉及列表和字典等 [可变 (mutable)](https://docs.python.org/3.8/glossary.html#term-mutable) 对象时可能会产生惊人的效果。例如，下面代码中的 `tricks` 列表不应该用作类变量因为所有的**Dog**实例只共享一个列表:

```python
class Dog:
	
	tricks = []			# 类变量的错误使用
	
	def __init__(self, name):
		self.name = name
	
	def add_trick(self, trick):
		self.tricks.append(trick)
        
>>> d = Dog('Fido')        
>>> e = Dog('Buddy')
>>> d.add_trick('roll over')
>>> e.add_trick('play dead')
>>> d.tricks		# 意外被所有的狗共享
```

正确的类设计是应该使用实例变量：

```python
class Dog:
	
	def __init__(self, name):
		self.name = name
		self.tricks = []		# 为每条狗创建一个新的空列表
		
	def add_trick(self, trick):
    	self.tricks.append(trick)
    	
>>> d = Dog('Fido')
>>> e = Dog('Buddy')
>>> d.add_trick('roll over')
>>> e.add_trick('play dead')
>>> d.tricks
['roll over']
>>> e.tricks
['play dead']
```

## 9.4.随机标记 (Random Remarks)

如果相同的属性名同时出现在实例和类中，那么属性优先在实例中查找：

```python
>>> class Warehouse:
...		purpose = 'storage'
...		region = 'west'
...
>>> w1 = Warehouse()
>>> print(w1.purpose, w1.region)
storage west
>>> w2 = Warehouse()
>>> w2.region = 'east'
>>> print(w1.purpose, w1.region)
storage east
```

数据属性可以由方法引用，也可以由对象的普通用户 ("客户端") 引用。换句话说，类不能用于纯的抽象数据类型。实际上，在Python中没有任何东西可以强制数据隐藏，它是所有约定的基础 (In fact, nothing in Python makes it possible to enforce data hiding — it is all based upon convention)。 (另一方面，用C语言写的Python实现完全可以隐藏实现细节，并在必要时控制对对象的访问，这可以用于用C编写的Python拓展。)

客户端应该谨慎使用数据属性，因为客户端可能会通过破坏方法的数据属性从而破坏方法所维护的常量。请注意，只要避免名称冲突，客户端可以在不影响方法的有效性的情况下将自己的数据属性添加到实例对象中。命名约定在此可以避免很多麻烦。

从方法中引用数据属性 (或其他方法) 没有简写。我发现，这实际上增加了方法的可读性：当浏览方法时不会混淆局部变量和实例变量。

通常，方法的第一个参数称为`self`。这只不过是一个约定：名称`self`对Python来说绝对没有特殊的含义。但是，请注意，如果不遵循这样的约定，你的代码对其他Python程序员的可读性可能会降低，而且可以想象，可能会编写一个依赖于这种约定的类浏览器程序。

类属性的任意函数对象都是为类的实例定义一种方法。函数定义不需要非得以文本形式封装在类定义中：也可以将函数对象赋值给类中的局部变量。例如：

```python
# 在类的外面定义函数
def f1(self, x, y):
	return min(x, x + y)
	
class C:
	f = f1
	
	def g(self):
		return 'hello world'
		
	h = g
```

现在，`f`、`g`和`h`都是`C`类的属性，它们引用函数对象，因此它们都是`C`实例对象的方法，`h`实际上等价`g`，请注意的是，这种做法通常只会使程序的读者感到迷惑。

方法可以使用 `self` 参数的方法属性来调用其他方法:

```python
class Bag:
	def __init__(self):
		self.data = []
	
	def add(self, x):
		self.data.append(x)
		
	def addtwice(self, x):
		self.add(x)
		self.add(x)
```

方法可以像普通函数一样引用全局名称。与方法关联的全局作用域是包含其定义的模块。(类永远不会用作全局作用域。) 虽然很少有很好的理由在方法中使用全局数据，但是全局作用域有许多合法的用法：首先，方法可以使用导入到全局作用域的函数和模块以及在方法中定义的函数和类。通常，包含方法的类本身是在这个全局作用域定义的。在下一节，我们将找到一些好的理由说明为什么一个方法想要引用自身的类。

每个值都是一个对象，因此都有一个类 (也可称为它的**类型**)，它被存储为 `object.__class__`。

## 9.5.继承 (Inheritance)

当然，如果类不支持继承，那么这种语言的特点就没有什么价值了。派生类定义的语法像这样：

````python
class DerivedClassName(BaseClassName):
	<语句 1>
	.
	.
	.
	<语句 N>
````

名称`BaseClassName`必须定义在包含派生类定义的作用域中。除了基类名之外，还允许使用其他任意表达式 (in place of a base class name, other arbitrary expressions are also allowed)，这可能很有用，列如，当基类在另一个模块定义时：

```python
class DerivedClassName(modname.BaseClassName):
```

派生类定义的执行过程跟基类的一样。当类对象被构建时，基类就被记住了，这被用于解析属性引用：如果所请求的属性在类中没找到，那么就从基类中找。如果基类本身是从其他类派生得到，这个过程递归进行。

派生类实例化没有特殊之处：`DerivedClassName()`创建一个类的新实例。方法引用的解析如下：搜索相应的类属性，必要时沿基类链搜索下去，如果这个过程产生函数对象则方法引用是有效的。

派生类可以重写其基类的方法。由于方法在调用相同对象的其他方法时没有特权，所以基类的方法如果调用同一基类中定义的另一个方法，则可能调用覆盖该类的派生类的方法 (Because methods  have no special privileges when calling other methods of the same object, a  method of a base class that calls another method defined in the same base class  may end up calling a method of a derived class that overrides it.)。(对C++程序员来说：在Python中的所有方法实际上都是`虚的`。)

派生类重载的方法实际上可能希望进一步拓展而不是简单地替代基类中的同名方法。这里有一种直接调用基类方法的简单方法：只调用`BassClassName.methodname(self, arguments)`。这对客户端来说有时也很有用。 (注意，只有基类在全局作用域中可以通过`BassClassName`来访问时这才有效。)

Python有两个与继承有关的内置函数：

- 使用 [`isinstance()`](https://docs.python.org/3.8/library/functions.html#isinstance) 来检查实例的类型： `isinstance(obj, int)` 会是真 (`True`) 如果 `obj.__class__`是 [`int`](https://docs.python.org/3.8/library/functions.html#int) 或是派生自 [`int`](https://docs.python.org/3.8/library/functions.html#int) 的类。
- 使用 `issubclass()` 来检查类的继承： `issubclass(bool, int)` 是真的，因为 [`bool`](https://docs.python.org/3.8/library/functions.html#bool) 是 [`int`](https://docs.python.org/3.8/library/functions.html#int) 的子类；然而， `issubclass(float, int)` 是假的，因为 [`float`](https://docs.python.org/3.8/library/functions.html#float) 不是 [`int`](https://docs.python.org/3.8/library/functions.html#int) 的子类。

### 9.5.1.多重继承 (Multiple Inheritance)

Python 同样支持多重继承的形式。具有多个基类的类定义的形式如下所示：

```python
class DerivedClassName(Base1, Base2, Base3):
	<语句1 >
	.
	.
	.
	<语句 N>
```

对于多数目的，在最简单的情况下，你可以将从继承的父类的属性搜索视为从左往右的深度优先搜索，而不是在层次结构中有重叠的同一个类中搜索两次。因此，如果属性在`DervivedClassName` 中没找到，就去`Base1`中查找，然后 (递归地) 在 `Base1` 的基类中查找，如果还没找到则进入`Base2`查找，以此类推。

实际上比这要稍微复杂一些；方法的解析顺序会动态地变化以支持对 [`super()`](https://docs.python.org/3.8/library/functions.html#super) 的协作调用。这种方法在其他一些多重继承语言中被称为**调用下一个方法 (call-next-method)**，这比单继承语言的超级调用更强大。

动态排序是必要的，因为所有情况的多重继承都存在一种或多种多角关系 (其中至少有一个父类由最底层父类经过多条路径而被访问)。例如，所有的类继承自  [`object`](https://docs.python.org/3.8/library/functions.html#object)，因此任何情况下的多重继承提供了超过一种路径去访问  [`object`](https://docs.python.org/3.8/library/functions.html#object)。为了避免基类被访问超过1次，动态算法将搜索顺序线性化，通过保护每个类以从左往右的特定搜索顺序的方式，这只调用父类一次并且是单调的 (意味着一个类可以是子类而不会影响其父类前面的顺序)。综合在一起，这些属性使得通过多重继承设计可靠且可拓展的类成为可能。更详细的信息参见 https://www.python.org/download/releases/2.3/mro/。

## 9.6.私有变量 (Private Variables)

除非从对象内部去访问，否则私有实例变量在Python中不存在 ("Private" instance variables that cannot be accessed except from inside an object don't exist in Python)。但是，有一个约定是大多数Python代码都遵循的：以下划线为前缀的名称 (比如：`_spam`) 应该被视为API (无论是函数、方法或数据成员) 的非公有部分 。它应该被视为实现细节，可以在不通知的情况下进行更改 (It should be considered an implementation detail and subject to change without notice)。

由于存在一个类私有成员的有效用例 (即避免名称与子类定义的名称之间的名称冲突)，所以对这种称为**名称篡改 (name mangling)** 的机制的支持是有限的。任何`__spam`形式 (至少有两个前导下划线，最多有一个尾部下划线) 的识别器都在文本上替换为`_classname__spam`，其中`classname` 是去除前导下换线的当前类名。只要出现在类的定义中，这种重整就不考虑识别器的语法位置。

名称重整有助于让子类重写方法而不破坏内部类方法调用，例如：

```python
class Mapping:
	def __init__(self, iterable):
		self.items_list = []
		self.__update(iterable)
		
	def update(self, iterable):
		for item in iterable:
			self.items_list.append(item)
		
	__update = update		# 原始 update() 方法的私有复制

class MappingSubclass(Mapping):
	
	def update(self, keys, values):
		# 给update()提供新签名
		# 但是不破坏 __init__()
		for item in zip(keys, values):
			self.item_list.append(item)
```

即使 `MappingSubclass` 引入了一个 `__update` 识别器，上面的例子仍然可以工作，因为识别在 `Mapping` 和 `MappingSubclass` 类中分别被 `_Mapping__update` 和 `_MappingSubclass__update` 替代。

注意，重整规则的设计主要是为了避免意外，但是仍然可以访问或修改被认为是私有的变量。在特定情况下，比如调试器中，这甚至是特别有用。

请注意，传递给 `exec()` 或 `eval()` 的代码并不会认为调用类的类名是当前类，这跟 `全局 (global)` 语句的效果类似，其效果同样局限于按字节编译的代码。相同的限制也适用于 `getattr()`、`setattr()`和`delattr()`以及直接引用 `__dict__` 时。

## 9.7.零碎的知识 (Odds and Ends)

有时，拥有类似于Pascal语言的 "记录 (record)" 和C语言的 "结构 (struct)" 的数据类型是很有用的，可以将少数的命名数据项绑定在一起，一个空的类定义可以很好地实现：

```python
class Employee:
	pass

john = Employee()			# 创建一个空的雇员记录

# 填充记录的域
john.name = 'John Doe'
john.dept = 'computer lab'
john.salary = 1000
```

一段Python代码期望一种特殊的抽象数据类型可以通过传递一个枚举处理数据方法的类来实现而不是传递数据类型。例如，如果你有一个函数从一个文件对象中格式化读取一些数据，那么你可以定义一个包含从字符串缓存中获取数据的 `read()` 和 `readline()` 方法的类，并将这个类当参数传递。

实例方法对象也有属性： `m.__self__` 是包含方法 `m()` 的实例对象，而 `m__func__` 是对应于该方法的函数对象。

## 9.8.迭代器 (Iterators)

迄今为止，你可能注意到大多数的容器对象都可以用[`for`](https://docs.python.org/3.8/reference/compound_stmts.html#for) 语句进行循环：

```python
for element in [1, 2, 3]:
	print(element)
for element in (1, 2, 3):
	print(element)
for key in {'one':1, 'two':2}:
	print(key)
for char in "123"
	print(char)
for line in open("myfile.txt")
	print(line, end='')
```

这种访问风格清晰、简洁和方便。迭代器的使用在Python中普遍而一致 (The use of iterator pervades and unifies Python)。在后台，[`for`](https://docs.python.org/3.8/reference/compound_stmts.html#for) 语句调用容器对象的 [`iter()`](https://docs.python.org/3.8/library/functions.html#iter)，这个函数返回一个定义了 [`__next__()`](https://docs.python.org/3.8/library/stdtypes.html#iterator.__next__) 方法的迭代器对象，该方法每次可以访问容器中的一个元素，当没有更多可访问的元素时，[`__next__()`](https://docs.python.org/3.8/library/stdtypes.html#iterator.__next__) 方法就抛出一个 [`StopIteration`](https://docs.python.org/3.8/library/exceptions.html#StopIteration) 异常告诉 `for` 循环停止。你可以通过 [`next()`](https://docs.python.org/3.8/library/functions.html#next) 内置函数调用 [`__next__()`](https://docs.python.org/3.8/library/stdtypes.html#iterator.__next__) 方法。这个例子显示它是如何工作的：

```python
>>> s = 'abc'
>>> it = iter(s)
>>> it
<str_iterator object at 0x000001F06AE57820>
>>> next(it)
'a'
>>> next(it)
'b'
>>> next(it)
'c'
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

明白了迭代器协议背后的机制，就很容易给自己的类添加迭代器行为。定义一个 [`__iter__()`](https://docs.python.org/3.8/reference/datamodel.html#object.__iter__) 方法返回包含 [`__next__()`](https://docs.python.org/3.8/library/stdtypes.html#iterator.__next__)  方法的对象。如果类定义了 [`__next__()`](https://docs.python.org/3.8/library/stdtypes.html#iterator.__next__) ，那么 [`__iter__()`](https://docs.python.org/3.8/reference/datamodel.html#object.__iter__) 只需返回 `self`：

```python
class Reverse:
	"""反向循环序列的迭代器"""
    def __init__(self, data):
        self.data = data
        self.index = len(data)
        
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.index == 0:
            raise StopIteration
        self.index = self.index - 1
        return self.data[self.index]
    
>>> rev = Reverse('spam')    
>>> iter(rev)
<__main__.Reverse object at 0x000001F06AE57580>
>>> for char in rev:
...     print(char)
...
m
a
p
s
```

## 9.9.生成器 (Generators)

[生长器 (Generator)](https://docs.python.org/3.8/glossary.html#term-generator) 是创建迭代器简单而强大的工具。它们像常规函数一样编写，但是可以在任何需要返回语句时使用 [`yield`](https://docs.python.org/3.8/reference/simple_stmts.html#yield) 语句。每次在其上调用 [`next()`](https://docs.python.org/3.8/library/functions.html#next) 时，生成器会返回它离开的地方 (它将记住所有的数据以及最后执行的语句)。下面的例子显示了很容易创建生成器：

```python
def reverse(data):
	for index in range(lenn(data) - , -1 -1):
		yield data[index]
```

```python
>>> for char in reverse('golf'):
...     print(char)
...
f
l
o
g
```

可以用生成器完成的任何工作也可以使用前一节所述的基于类的迭代器实现。生成器如此紧凑的原因是自动创建了 [`__iter__()`](https://docs.python.org/3.8/reference/datamodel.html#object.__iter__) 和 [`__next__()`](https://docs.python.org/3.8/reference/expressions.html#generator.__next__)  方法。

另一个关键特征是调用之间的局部变量和执行状态可以自动保存。这使得函数比使用如`self.index`和`self.data`实例变量的方法更容易编写也更清晰。

除了自动的方法创建以及自动保存程序状态外，当生成器结束时，它们还自动抛出 [`StopIteration`](https://docs.python.org/3.8/library/exceptions.html#StopIteration)。总之，这些特性使得创建迭代器很容易，只需编写一个常规函数即可。

## 9.10.生成器表达式 (Generator Expressions)

一些简单的生成器可以使用类似列表解析的语法简单地写成表达式，只需使用圆括号替换方括号。这些表达式设计用于一个封装的函数想要立即使用生成器的情况。与完整的生成器定义相比，生成器表达式更紧凑，但是通用性更差，而且与等价的列表解析相比，生成器表达式更易于存储。

例如：

```python
>>> sum(i*i for i in range(10))		# 平方数求和
285

>>> xvec = [10, 20, 30]
>>> yvec = [7, 5, 3]
>>> sum(x*y for x, y in zip(xvec, yvec))	# 点乘
260

>>> unique_words = set(word for line in page  for word in line.split())

>>> valedictorian = max((student.gpa, student.name) for student in graduates)

>>> data = 'golf'
>>> list(data[i] for i in range(len(data)-1, -1, -1))
['f', 'l', 'o', 'g']
```

