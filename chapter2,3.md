
# fluent Python
# 容器
Container, Iterable, Sized是各种容器继承的三个基类;
Python序列类型可分为可变和不可变序列; 也可以分为扁平和容器序列.
不可变序列的基类是Sequence
可变序列的基类是MutableSequence, 提供了例如__setitem__的内置函数 
## list [ ], tuple ( )
1. 不要把可变对象放在元组里,  因为tuple不支持对它的元素赋值, 会抛出typeError异常
2. 不要对不可变序列重复拼接操作
3.  增量的赋值不是一个原子操作
4. `list.sort`就地排列不返回;
`sorted`方法返回一个列表
5. `bisect(stack, needle)` 和
`insort(stack, item)
6. 当列表不是首选时:
存放大量float: array
频繁对序列先进先出:  deque
7. array创建时需要一个类型码, 比如array(‘b’)只能存放一个字节的有符号整数, 可以节约很多空间; array(‘d’)浮点型;
8. array排序 array.array(a.typecode, sorted(a))
9. 具名元组可以构建带字段名的元组:
```py
city = namedtuple('City', 'name country population')
m_city = City('Tokyo', 'JP', 2412)
```

## 字典和集合
### dict { k:v }
1. 字典创建:列表推导
```py
D_CODE = [(1,'China'), (2,'usa')]
country_code = {country: code for code, country in D_CODE}
```

2. `dict.setdefault`设定空值:
```py
#第一种写法
if key not in my_dict:
	my_dict[key] = []
my_dict[key].append(new_value)
#更好的写法, 使用setdefault函数不需要第二次查找就能更新列表
my_dict.setdefault(key,[]).append(new_value)
```
3. 处理找不到键的一个选择:
	* `__missing__`方法是在对象找不到某个键的时候, 可以自定义如何处理
defaultdict(object) 对于不存在某个key的键值对, 会自动创建一个以object为值的key:object
```py
my_dict = collections.defaultdict(list)
```
4. 字典的变种
collection.OrderedDict  保持添加键的顺序
collection.ChainMap  
collection.Counter 会给键准备一个整数计数器
collection.UserDict
通过继承UserDict,可以创建很多自定义的字典类型:
比如创建一个str为key的dict:
```py
class StrKeyDict(collection.UserDict):
	def __setitem__(self, key, item): #核心重写, 这个是创建键值对时调用的方法
		self.data[str(key)] = item
	def __missing__(self, key):
		if isinstance(key, str):
			raise KeyError(key)
		return self[str(key)] #这里重写了UserDict父类的方法
	def __contains__(self, key):
		return str(key) in self.data
```
不可变映射类型: MappingProxyType
```py
from type import MappingProxyType
my_proxy = MappingProxyType({1:’A’})
```
### set { }
集合可以去重,  集合中的元素必须是可散列的(hashable);
frozenset是一种本身可以散列的set类型
集合实现了很多中缀运算符, 比如a % b取交集, 可以提高效率
```py
found = len(needle & haystack)
```
集合也可以做推导{ xxx if xxx }

### dict的hash过程
hash表是一个稀疏数组, 每个单元叫做bucket, 每个bucket有2个部分:对键的引用和对值的引用, 每个bucket大小一致, 通过偏移量来读取某个表元, hash表在快达到存储空间阈值的时候会复制到一个更大的空间里面;
计算原色的hash值使用hash()方法,  str, bytes, datetime类型在计算时会加盐;
`CPython的hash table是由数组组成的, 数组中获取一个元素的复杂度是O(1)，因为数组的起始地址p + 下标k * 每个元素的长度L，就可以获得下标k这个元素的起始地址了。`
> 查找操作的hash表算法
> 1. 计算search_key的hash值
> 2. 取其中几位去bucket里找键值对
> 3. if 没找到, 抛出KeyError
> 4. 找到了, 比较found_key == search_key ?
> 5. if 相等, 返回key
> 6. if 不相等, hash冲突了, 再多取hash的几位再重复查找

# 文本和字节序列
## 字符串
Python3中, 字符串 str对象中获取的元素是Unicode字符, 对str进行encode, 得到bytes:
```py
s = ‘cafe’
b = s.encode(‘utf8’)
>>> b
b’caf\xc3\xa9’
>>> b.decode(‘utf8’)
‘cafe’
```

Python2中, 字符串是ASCII编码

# 函数
1. map 和 filter可以用列表推导替代:
```py
# map和列表推导替代
a = list(map(fab, range(6)))
a = [fab(n) for n in range(6)]

# filter和列表推导替代
a = list(map(fab, filter(lambda n : n%2, range(6))))
a = [fab(n) for n in range(6) if n%2]
```

2. 内置函数:
求和`sum(range(100))`
整体都符合判断`all(iterable)`
任意符合判断`any(iterable)`
3. 匿名函数lambda
lambda表达式中不能赋值, while, try 等语句;
lambda是语法糖, 会创建函数对象
4. 调用类时会运行__new__方法创建1个实例, 然后运行__init__方法初始化实例.如果定义了__call__, 类的实例可以做函数调用:
```py
class Bingo:
	def __init__(self, items):
		self._items = list(items)
	def pick(self):
		return self._items.pop()
	def __call__(self):
		return self.pick()

# demo
b = Bingo(range(4))
b()
>>> 0
```
5. dir(f) 可以探知f具有的属性, 如:
```py
def big(a):
	return a

dir(big)
>>>
['__annotations__', '__call__', '__class__', '__closure__', '__code__', '__defaults__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__get__', '__getattribute__', '__globals__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__kwdefaults__', '__le__', '__lt__','__module__', '__name__', '__ne__', '__new__', '__qualname__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__']

```
6. global() 返回一个字典, 表示当前的全局符号表, 始终针对当前模块
```py
# 内省模块的全局命名空间
promos = [global()[name] for name in globals() if name.endswith('_promo') and name != 'best_promo']

def best_promo(order):
	return max(promo(order) for promo in promos)
```
```py
# 内省单独的promotions模块
promo = [func for name, func in inspect.getmembers(promotions, inspect.isfuntion()]

def best_promo(order):
	return max(promo(order) for promo in promos)
```

# 装饰器和闭包
1.作用域
```py
b = 6
def f1(a):
	print(a)
	print(b)
```
这里b就是全局变量, 在调用前就赋值了
```py
b = 6
def f1(a):
	global b
	print(a)
	print(b)
	b = 9
```
python函数内不需要声明变量, 但是前提是函数内的变量是局部变量, 如果函数需要获取*全局变量*,   使用`global`. 上例中, b进行了赋值 = 9, 所以被默认为局部变量, `如果b说是数字或其他不可变变量时,在赋值后都会定位为局部变量`,但是加上了global就表示是全局变量.

## 闭包
闭包指延伸了作用域的函数, 其中包含函数定义体的引用, 但是不在定义体重定义的非全局变量, 他能访问定义体之外定义的非全局变量.
```py
def make_average():
    series = []
    def average(new_value):
		  #series是自由变量, 指未在本地作用域中绑定的变量
        series.append(new_value)
        total = sum(series)
        return total/len(series)
    return average

ave = make_average()
print(ave(10)) 
#因为make_average返回的是average, 所以这里的10就是new_value的赋值
```
* 使用nonlocal字段将变量更新为自由变量
```py
def make_average():
	count = 0
	def average(new_value):
		nonlocal count
		count += 1
		return new_value
	return average
```

* 输出函数运行时间的装饰器
```py
import time
import functools

def clock(func):
	#这个装饰器把相关的属性从func复制到clocked
	@functools.wraps(func)
	def clocked(*args, **kwargs):
		#计算环节
		t0 = time.perf_counter()
		result = func(*arg)
		elapsed = time.perf_counter() - t0
		
		#打印环节
		name = func.__name__
		arg_lst = []
		if args:
			arg_lst.append(','.join(repr(arg) for arg in args))
		if kwargs:
			pairs = ['%s=%r' % (k,w) for k,w in sorted(kwargs.items())]
			arg_lst.append(','.join(pairs))
		arg_str = ','.join(arg_lst)
		print('[%0.8fs] %s(%s) -> %r' % (elapsed, name, arg_str, result))
		return result
	return clocked
```

### least recently used(lru)
functools.lru_cache 是一项优化技术, 把耗时的函数结果保存起来, 避免传入重复的计算. 
比如斐波那契数列的计算可以用这个方法优化:
```py
import functools
# 这个是自己写的计时函数
import clock

@functools.lru_cache()
@clock
def fibonacci(n):
	if n < 2:
		return n
	return fibonacci(n-2) + fibonacci(n-1)

if __name__ = '__main__':
	print(fibonacci(6))
```

# chapter 8 对象
1. `==`是值比较, 调用dict类中的__eq__, 但是多数内置类型使用更有意义的方法覆盖了__eq__导致会考虑对象的值;  `is`是标识比较, CPython比较的是对象的内存地址. 
2. `x is None`
3. tuple的相对不可变性, tuple的元素不可变实际指的是元素的标识不可变, 比如tuple中有list元素, 这个list实际是可以增加减少元素的.
