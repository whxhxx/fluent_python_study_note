
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
