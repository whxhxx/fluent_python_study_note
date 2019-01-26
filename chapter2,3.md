
# 容器
Python序列类型可分为可变和不可变序列; 也可以分为扁平和容器序列.
## list, tuple
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

## 字典和集合
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
