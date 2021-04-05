# 流畅的Python

## 2 序列构成的数组

​	Python中不管是哪种序列数据，字符串、列表、字节序列、数组、XML元素，抑或是数据库查询结果，它们都共用一套丰富操作：迭代、切片、排序，还有拼接。

### 2.1 内置序列类型概览

- 容器序列

  list、tuple和collections.deque这些序列能存放不同类型的数据。

- 扁平序列

  str、bytes、bytearray、memoryview和array.array，这类序列只能容纳一种类型。

容器序列存放的是它们所包含的任意类型的对象的引用，而扁平序列里存放的是值而不是引用。扁平序列其实是一段连续的内存空间，它里面只能存放诸如字符、字节和数值这种基础类型。

序列类型还能按照能否被修改来分类。

- 可变序列

  list、bytearray、array.array、collections.deque和memoryview。

- 不可变序列

  tuple、str和bytes。

### 2.2 列表推导和生成器表达式

列表推导是构建列表(list)的快捷方式，而生成器表达式则可以用来创建其他任何类型的序列。

#### 2.2.1 列表推导和可读性

```python
>>>symbols = 'abc'
>>>codes = []
>>>for symbol in symbols:
    codes.append(ord(symbol))
//列表推导的写法
>>>codes = [ord(symbol) for symbol in symbols]
```

- 使用原则：只用列表推导来创建新的列表，并且尽量保持简短，如果列表推导的代码超过了两行，应该考虑是不是用for循环重写了。
- 列表推导、生成器表达式，以及同它们很相似的集合(set)推导和字典(dict)推导，在Python3中都有了自己的局部作用域。
- Python会忽略代码里[ ]、{ }和( )中的换行。
- 列表推导可以帮助我们把一个序列或是其他可迭代类型中的元素过滤或是加工，然后再新键一个列表。(Python内置的filter和map函数组合起来也能达到这一效果，但可读性不高)。

#### 2.2.2 列表推导同filter和map的比较

```python
//列表推导
>>>symbols = '$#abs'
>>>beyond_ascii = [ord(s) for s in symbols if ord(s) > 127]

//filter/map
>>>beyond_ascii = list(filter(lambda c: c > 127,map(ord,symbols)))
```

#### 2.2.3 笛卡儿积

```python
>>>colors = ['black','white']
>>>sizes = ['S','M','L']
>>>tshirts = [(color,size) for color in colors for size in sizes]
[('black','S'),('black','M'),('black','L'),('white','S'),('white','M'),('white','L')]


```

#### 2.2.4 生成器表达式

虽然也可以用列表推导来初始化元组、数组或其他序列类型，但是生成器表达式是更好的选择。这是因为生成器表达式背后遵守了迭代器协议，可以逐个地产出元素，而不是先建立一个完整的列表，然后再把这个列表传递到某个构造函数里。前面那种方式显然能够节省内存。

```python
>>>colors = ['black','white']
>>>sizes = ['S','M','L']
>>>for tshirt in ('%s %s' %(c,s) for c in colors for s in sizes):
    print(tshirt)
```

生成器表达式逐个产出元素，从来不会一次性产出一个含有6个样式的列表。

### 2.3 元组不仅仅是不可表的列表

#### 2.3.1 元组和记录

如果只把元组理解为不可变的列表，那其他信息——它所包含的元素的总数和它们的位置——似乎变得可有可无。但是如果元组当作一些字段的集合，那么数量和位置信息就变得非常重要了。

如果在任何表达式里我们在元组内对元素排序，这些元素所携带的信息就会丢失，因为这些信息是跟它们的位置有关的。

```python
>>>lax_coordinates = (33.9425,-118.408056)
>>>city,year,pop,chg,area = ('Tokyo',2003,0.66,8014)
>>>traveler_ids = [('USA','331195855'),('BRA','CE342567'),('ESP','XDA205856')]

>>>for passport in sorted(traveler_ids):
    print('%s / %s' %passport)
...
BRA/ce342567
ESP/31195855
USA/31195855	

>>>for country,_ in traveler_ids:
    print(country)
USA
BRA
ESP
```

#### 2.3.2 元组拆包

元组拆包可以应用到任何可迭代对象上，唯一的硬性要求是，被可迭代对象中的元素数量必须要跟接受这些元素的元组的空档数一致。除非我们用*来表示忽略多余的元素。

平行赋值:

```python
>>>lax_coordinates = (33.9425,-118.408056)
>>>latitude,longitude = lax_coordinates #元组拆包
```

- 不使用中间变量交换两个变量的值:

  ```python
  b,a = a,b
  ```

- 使用*运算符把一个可迭代对象拆开作为函数的参数:

  ```python
  >>>divmod(20,8)
  (2,4)
  >>>t = (20,8)
  >>>divmod(*t)
  (2,4)
  ```

- 使用_占位符

  ```python
  import os
  >>>_,filename = os.path.split('/home/luciano/.ssh/idrsa.pub')
  >>>filename
  'idrsa.pub'
  ```

- 使用*来处理剩下的元素

  ```python
  >>>a,b,*rest = range(5)
  >>> a,b,rest
  (0,1,[2,3,4])
  ```

  在平行赋值中，*前缀只能用在一个变量名前面，但是这个变量可以出现在赋值表达式的任意位置:

  ```python
  >>>a,*body,c,d = range
  >>>a,body,c,d
  (0,[1,2],3,4)
  ```

#### 2.3.3 嵌套元组拆包

接受表达式的元组可以是嵌套式的，例如(a,b,(c,d))。只要这个接受元组的嵌套结构符合表达式本身的嵌套结构，Python就可以作出正确的对应。

```python
metro_areas = [
    ('Tokyo','JP',36.933,(35.689722,139.691667)),
    .......
]
for name,cc,pop,(latitude,longitude) in metro_areas:
    print(fmt.format(name,latitude,longitude))
```

#### 2.3.4 具名元组

。。。

#### 2.3.5 作为不可变列表的元组

除了跟增减元素相关的方法之外，元组支持列表的其他所有方法。例外，元组没有\__\_reversed\_\_方法，但是这个方法只是个优化而已，reversed(my_tuple)这个用法在没有\_\_reversed\_\_的情况下也是合法的。

。。。

### 2.4 切片

#### 2.4.1 为什么切片和区间会忽略最后一个元素

。。。

#### 2.4.2 对对象进行切片

。。。



#### 2.4.3 多维切片和省略

。。。

#### 2.4.4 给切片赋值

如果把切片放在赋值语句的左边，或把它作为del操作的对象，我们就可以对序列进行嫁接、切除或就地修改操作。

```python
>>> l =list(range(10))
>>> l
[0,1,2,3,4,5,6,7,8,9]
>>> l[2:5] = [20,30]
>>> l
[0,1,20,30,5,6,7,8,9]
```

如果赋值的对象是一个切片，那么赋值语句的右侧必须是一个可迭代对象。即便只有单独一个值，也要把它转换成可迭代的序列。

### 2.5 对序列使用+和*

+和*都遵循这个规律，不修改原有的操作对象，而是构建一个全新的序列。

- 建立由列表组成的列表

  有时我们需要初始化一个嵌套着几个列表的列表，想要达成这些目的，最好的选择是使用**列表推导**。

```python
>>> board = [['_'] * 3  for i in range(3)]
>>> board
[['_','_','_'],['_','_','_'],['_','_','_']]
>>> board[1][2] = 'X'
>>> board
[['_','_','_'],['_','_','X'],['_','_','_']]
```



```python
>>> weird_board = [['_']*3]*3
>>>	weird_board 
[['_','_','_'],['_','_','_'],['_','_','_']]
>>> weird_board[1][2] = 'o'
>>> weird_board
[['_','_','o'],['_','_','o'],['_','_','o']]
```

### 2.6 序列的增量赋值

增量赋值运算符+=和*=的表现取决于它们的第一个操作对象。

+=背后的特殊方法是\_\_iadd\_\_(用于就地加法)。但是如果一个类没有实现这个方法的话，Python会退一步调用\_\_add\_\_。

```python
>>> a+=b
```

如果a实现了\_\_iadd\_\_方法，就会调用这个方法。同时对可变序列来说，a会就地改动，就像调用了a..extend(b)一样。但是如果a没有实现\_\_iadd\_\_的话，a+=b这个表达式的效果就变得a=a+b一样了；首先计算a+b，得到一个新的对象，然后赋值给a。也就是说，在这个表达式中，变量名会不会被关联到新的对象，完全取决于这个类型有没有实现\_\_iadd\_\_这个方法。

#### 一个关于+=的谜题

```python
>>> t = (1,2,[30,40])
>>> t[2] += [50,60]
Traceback(most recent call last):
    File "<stdin>", line 1,in <module>
TypeError:'tuple' object does not support item assignment
>>> t
(1,2,[30,40,50,60])
```



- str是一个例外，因为对字符串做+=实在太普遍了，所以CPython对它做了优化，为str初始化内存的时候，程序会为它留出额外的可扩展空间，因此进行增量操作的时候，并不会涉及到复制原有字符串到新位置这类操作。

### 2.7 list.sort方法和内置函数sorted

- list.sort方法会就地排序列表，也就是说不会把原列表复制一份。这也是这个方法的返回值是None的原因，提醒你本方法不会新建一个列表。
- 内置函数sorted，它会新建一个列表作为返回值。这个方法可以接受任何形式的可迭代对象作为参数，甚至包括不可变序列或生成器。而不管sort接受的是怎样的参数，它最后都会返回一个列表。

不管是list.sort方法还是sorted函数，都有两个可选的关键字参数。

- reverse

  如果被设定为True，被排序的序列里的元素会以降序输出。默认为False。

- key

  一个只有一个参数的函数，这个函数会被用在序列里的每一个元素上，所产生的结果将是排序算法依赖的对比关键字。参数的默认值是恒等函数，也就是默认用元素自己的值来排序。

### 2.8 用bisect来管理已排序的序列

。。。

### 2.9 当列表不是首选时

#### 2.9.1 数组

如果我们需要一个只包含数字的列表，那么array.array比list更高效。数组支持所有跟可变序列有关的操作，包括.pop、.insert和.extend。另外，数组还提供从文件读取和存入文件的更快的方法，如.frombytes和.tofile。



#### 2.9.2 内存视图



#### 2.9.3 NumPy和SciPy



#### 2.9.4 双向队列和其他形式的队列



## 3 字典和集合



### 3.1 泛映射类型





### 3.2 字典推导

字典推导可以从任何以键值对作为元素的可迭代对象中构建出字典。

```python
>>> DIAL_CODES = [
...		(86,'China'),
    	(91,'India'),
    	...
]
>>> country_code = {country:code for code , country in DIAL_CODES}
{'China':86,'India':91,....}

>>> {code:country.upper() for country, code in country_code.items() if code < 66}
{1:'UNITED STATES', 55:'BRAZIL'}
```



### 3.3 常见的映射方法



#### 用setdefault处理找不到的键



## 4 文本和字节符

。。。



## 5 一等函数

在Python中，函数是一等对象。编程语言理论家把"一等对象"定义为满足下述条件的程序实体:

- 在运行时创建
- 能赋值给变量或数据结构中的元素
- 能作为参数传给函数
- 能作为函数的返回结果

在Python中，整数、字符串和字典都是一等对象——没什么特别的。

### 5.1 把函数视作对象

```python
>>> def factorial(n):
    '''returns n!'''
    return 1 if n <2 else n *factorial(n-1)
>>> factorial(42)
140.......
>>> factoria.__doc__
'''returns n!'''
>>> type(factoria)
<class 'function'>
```

\_\_doc\_\_属性是用于生成对象的帮助文本。

```python
>>> fact = factorial
>>> fact
<function factorial at 0x...>
>>> map(factorial,range(11))
<map object at 0x...>
```

### 5.2 高阶函数

接受函数为参数，或者把函数作为结果返回的函数就是高阶函数。如map函数、sorted内置函数。

#### map、filter和reduce的现代替代品

由于引入了列表推导和生成器表达式，即使Python3中，map和filter还是内置函数，它们也变得没那么重要了。

```python
>>> list(map(fact,range(6)))
[1,1,2,6,24,120]
>>> [fact(n) for n in range(6)]
[1,1,2,6,24,120]

>>> list(map(factorial,filter(lambda n : n%2,range(6))))
[1,6,120]
>>>[factorial(n) for n in range(6) if n % 2]
[1,6,120]
```

### 5.3 匿名函数

lambda关键字在Python表达式内创建匿名函数。

- lambda函数的定义体中不能赋值，也不能使用while和try等Python语句。
- 在参数列表中最适合使用匿名函数。
- lambda句法只是语法糖：与def语句一样，lambda表达式会创建函数对象。这是Python中几种可调用对象的一种。

```python
>>> fruits = ['strawberry','fig','apple','cherry','raspberry','banana']
>>> sorted(fruits,key=lambda word:word[::-1])
['banana','apple','fig','raspberry','cherry']
```

### 5.4 可调用对象

除了用户定义的函数，调用运算符(即(）)还可以应用到其他对象上。如果想判断对象能否调用，可以使用内置的callable(）函数。Python数据模型文档列出了7种可调用对象。

- 用户定义的函数

  使用def语句或lambda表达式创建

- 内置函数

  使用C语言CPython实现的函数，如len或time.strftime。

- 内置方法

  使用C语言实现的方法，如dict.get。

- 方法

  在类的定义体中定义的函数

- 类

  调用类时会运行类定\_\_new\_\_方法创建一个实例，然后运行\_\_init\_\_方法，初始化实例，最后把实例返回给调用方。因为Python没有new运算符，所以调用类相当于

### 5.5 用户可定义的可调用类型

任何Python对象都可以表现得像函数。为此，只需实现实例方法\_\_call\_\_。





### 5.6 函数内省

使用dir函数可以探知函数factorial具有下列属性:

```python
>>> dir(factorial)
['__annotations__','__call__','__call__','__closure__','__code__','__defaults__','__delattr__','__dict__','__doc__','__eq__',.....]
```





- 用户定义的函数的属性

| 名称                | 类型           | 说明                                 |
| ------------------- | -------------- | ------------------------------------ |
| \_\_annotations\_\_ | dict           | 参数和返回值的注解                   |
| \_\_call\_\_        | method-wrapper | 实现()运算符：即可调用对象协议       |
| \_\_closure\_\_     | tuple          | 函数闭包，即自由变量的绑定           |
| \_\_code\_\_        | code           | 编译成字节码的函数元数据和函数定义体 |
| \_\_defaults\_\_    | tuple          | 形式参数的默认值                     |
| \_\_get\_\_         | method-wrapper | 实现只读描述协议                     |
| \_\_globals\_\_     | dict           | 函数所在模块中的全局变量             |
| \_\_kwdefaults\_\_  | dict           | 仅限关键字形式参数的默认值           |
| \_\_name\_\_        | str            | 函数名称                             |
| \_\_qualname\_\_    | str            | 函数的限定名称                       |

### 5.7 从定位参数到仅限关键字参数

- 普通参数
- 可变参数
- 默认参数
- 关键字参数

参数的定义顺序有一定要求:普通参数、可变参数、默认参数、关键字参数。

```python
// 生成HTML标签
def tag(name,*content,cls=None,**attrs):
   if cls is not None:
    attrs['class']=cls
   if attrs:
        attr_str = ''.join( '%s="%s"' % (attr,value) 
                           for attr,value 
                           in sorted(attrs.items()))
   else:
    	attr_str = ''
  	if content:
        return '\n'.join('<%s%s>%s</%s>' %
                        (name,attr_str,c,name) for c in content)
   	else:
        return '%s%s />' % (name,attr_str)
```



```python
>>> tag('br')
'<br />'

>>> tag('p','hello')
'<p>hello</p>'

>>> print(tag('p','hello','world'))
<p>hello</p>
<p>world</p>

>>> tag('p','hello',id=33)
'<p id="33">hello</p>'

>>> print(tag('p','hello','world',cls='sidebar'))
<p class="sidebar">hello</p>
<p class="sidebar">world</p>

>>> tag(content="testing",name="img")
'<img content="testing"/>'

>>> my_tag = {'name:'img','title':'Sunset Boulevard',
             	'src':'sunset.jpg','cls':'framed'}
>>> tag(**my_tag)
'<img class="framed" src="sunset.jpg" title ="Sunset Boulevard"/>'
```

- 仅限关键字参数是Python3新增的特性。在示例中，cls参数只能通过关键字参数指定，它一定不会捕获未命名的定位参数。定义函数时若想指定仅限关键字参数，要把他们放到前面有\*的参数后面。如果不想支持数量不定的定位参数，但是想支持仅限关键字参数，在前面个中放一个\*，如下所示:

```python
>>> def f(a,*,b):
...    return a,b
...
>>> f(1,b=2)
(1,2)
```

- 注意，仅限关键字参数不一定要有默认值，可以像上例中b那样，强制必须传入实参。

### 5.8 获取关于参数的信息



### 5.9 函数注解



### 5.10 支持函数式编程的包

#### 5.10.1 operator模块



#### 5.10.2 使用functools.partial冻结参数





## 7 函数装饰器和闭包

函数装饰器用于在源码中，标记函数，以某种方式增强函数的行为。这是一项强大的功能，但是若想掌握，必须理解闭包。

除了在装饰器中有用处之外，闭包还是回调式异步编程和函数式编程风格的基础。

### 7.1 装饰器基础知识

装饰器是可调用的对象，其参数是另一个函数(被装饰的函数)。装饰器可能会处理被装饰的函数，然后把它返回，或者将其替换成另一个函数或可调用对象。

```python
@decorate
def target():
    print('running target()')
# 上述代码的效果与下述写法一样:
def target():
    print('running target()')

target = decorate(target)
```

```python
# 装饰器通常会把函数替换成另一个函数
>>> def deco(func):
    	print('running inner()')
   	return inner

>>> @deco
... def target():
...    	print('running target()')
...

>>> target()
running inner()
>>> target
<funtion deco.<locals>.inner at 0x10063b598> //target现在是inner的引用
```

- 装饰器只是语法糖
- 装饰器能把被装饰的函数替换成其他函数
- 装饰器在加载模块时立即执行

### 7.2 Python何时执行装饰器

装饰器的一个关键特性是，它们在被装饰的函数定义之后立即运行。这通常是在导入时(即Python加载模块时)，如

```python
regsitry = []

def register(func):
    print('running register(%s)' % func)
    registry.append(func)
    return func

@register
def f1():
    print('running f1()')
   
@register
def f2():
    print('running f2()')
 
@register
def f3():
    print('running f3()')
    
def main():
    print('running main()')
    print('registry ->',registry)
    f1()
    f2()
    f3()
if__name__='__main__':
    main()
```

运行结果：

```python
$ python3 registration.py
running register(<function f1 at 0x100631bf8>)
running register(<function f2 at 0x100631c80>)
running main()
registry -> [<function f1 at 0x100631bf8>,<function f2 at 0x100631c80>]
running f1()
running f2()
running f3()
```



如果导入registration.py模块(不作为脚本运行)，输出如下：

```python
>>> import registration
running register(<function f1 at 0x10063b1e0>)
running register(<function f2 at 0x10063b268>)
```

此时查看registry的值，得到的输出如下：

```python
>>> registration.registry
[<function f1 at 0x10063b1e0>,<function f2 at 0x10063b268>]
```

一般装饰器在真实代码中的常用模式

- 装饰器通常在一个模块中定义，然后应用到其他模块中的函数上
- 大多数装饰器会在内部定义一个函数，然后将其返回。

### 7.3 使用装饰器改进“策略”模式

使用注册装饰器可以改进6.1节中的电商促销折扣示例。

之前示例6-6的主要问题是，定义体中有函数的名称，但是best_promo用来判断哪个折扣幅度最大的promos列表中也有函数名称。这种重复是个问题，因为新增策略函数后可能会忘记把它添加到promos列表中，导致best_promo忽略新策略，而且不报错，为系统引入了不易察觉的缺陷。可以使用注册装饰器解决了这个问题。

```python
promos = []

def promotion(promo_func):
    promos.append(promo_func)
    return promo_func

@promotion
def fidelity(order):
    return order.total()*.05 if order.customer.fidelity >= 1000 else 0
....

def best_promo(order):
    return max(promo(order) for promo in promos)
```

### 7.4 变量作用域规则

```python
>>> b = 6
>>> def f2(a):
    	print(a)
        print(b)
        b=9
        
>>> f2(3)
3
Traceback (most recent call last):
    UnboundLocalError:local variable 'b' referenced before assignment
```

Python编译函数的定义体时，它判断b是局部变量，因为在函数中给它赋值了。生成的字节码证实了这种判断，Python会尝试从本地环境获取b。后面调用f2(3)时，f2的定义体会获取打印局部变量a的值，但是尝试获取局部变量b的值时，发现b没有绑定值。

这不是缺陷，而是设计选择：Python不要求声明变量，但是假定在函数定义体中赋值的变量是局部变量。这比JavaScript的行为好多了，JavaScript也不要求声明变量，但是如果忘记把变量声明为局部变量(使用var)，可能会在不知情的情况下获取全局变量。

```python
>>> b = 6
>>> def f3(a):
    	global b
        print(a)
        print(b)
        b = 9

>>> f3(3)
3
6
>>> b
9

>>> f3(3)
3
9
>>> b = 30
>>> b
30
>>>
```

### 7.5 闭包

闭包指**延伸了作用域的函数**，其中包含函数定义体中引用、但是不在定义体中定义的非全局变量。函数是不是匿名的没有关系，关键是它能访问定义体之外定义的非全局变量。

```python
def make_averager():
    series = []
    
    def averager(new_value):
        series.append(new_value)
        total = sum(series)
        return total / len(series)
    return averager
```

- 在averager函数中，series是自由变量。指未在本地作用域中绑定的变量。

- averager的闭包延伸到那个函数的作用域之外，包含自由变量series的绑定。

### 7.6 nonlocal声明

前面实现make_averager函数的方法效率不高。更好的实现方式是，只存储目前的总值和元素个数，然后使用这两个数计算均值。

```python
def make_averager():
    count = 0
    total = 0
    
    def averager(new_value):
        count+=1
        total+=new_value
        return total / count
```



```python
>>> avg = make_averager()
>>> avg(10)
Traceback(most recent call last):
    ...
UnboundLocalError:Local variable 'count' referenced before assignment
>>>
```

问题是，当count是数字或任何不可变类型时，count+=1语句其实与count=count+1一样。因此，我们在averager的定义体中为count赋值了，这会把count变成局部变量。total变量也受这个问题影响。

- 上例中我们给series赋值，只是调用series.append，并把它传给sum和len。也就是说，我们利用了列表是可变的对象这一事实。
- 但是对数字、字符串、元组等不可变类型来说，只能读取，不能更新。如果尝试重新绑定，例如count=count+1,其实会隐式创建局部变量count。这样，count就不是自由变量了，因此不会保存在闭包中。

为了解决这个问题，Python3引入了nonlocal声明，它的作用是把变量标记为自由变量，即使在函数中为变量赋予新值了，也会变成自由变量。如果为nonlocal声明的变量赋予新值，闭包中保存的绑定会更新。

```python
def make_averager():
    count = 0
    total = 0
    
    def averager(new_value):
        nonlocal count,total
        count+=1
        total+=new_value
        return total / count
```

