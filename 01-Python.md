# Python




## 实用数据结构和算法

### 双端队列—deque

deque是一种序列容器，同样支持list的一些操作。

```python
from collections import deque
```

* 填充

  * **extend()：** 从序列右边填充一个序列；
  * **extendleft()：** 从序列左边填充一个序列；
  * **append()：** 从序列右边填充一个元素；
  * **appendleft()：** 从右边填充一个元素；

  **<font color=red >注意：</font>** 队列指定了最大长度之后，当队列元素达到限制时，会将添加位置相反的最后或最前一个元素挤掉。

```python
dq = deque(maxlen=5)
dq.extend('1234')
print(dq)
dq.extendleft('987')
print(dq)
dq.append(3)
print(dq)
dq.appendleft('hi')
print(dq)
'''
deque(['1', '2', '3', '4'], maxlen=5)
deque(['7', '8', '9', '1', '2'], maxlen=5)
deque(['8', '9', '1', '2', 3], maxlen=5)
deque(['hi', '8', '9', '1', '2'], maxlen=5)
'''
```



* 消费

  * **leftpop()：** 从左边取出一个元素；
  * **pop()：** 从右边取出一个元素；

  

* 旋转

  * **rotate(2)：** 从右端取出相应个数元素，移到左端；
  * **rotate(-2)：** 加个“-”，从左端移到右端；



#### 两端消费

双端队列是线程安全的，可以在不同的线程中同时从两端消费队列

```python
import threading
import time
from collections import deque

dq = deque(range(5))
print(dq)


def burn(direction, next_source):
    while True:
        try:
            source = next_source()
            time.sleep(1)
        except IndexError:
            break
        else:
            print('{:8}：{}'.format(direction, source))
    print('{:8}：done'.format(direction))


left = threading.Thread(target=burn, args=('Left', dq.popleft))
right = threading.Thread(target=burn, args=('Right', dq.pop))

left.start()
right.start()

left.join()
right.join()
```

### 堆排序算法—heapq

堆(heap)：树形数据结构，其中子节点和父节点有一种有序关系。

```python
import heapq
```

* 创建堆
  * heappush(heap, itme)：增加新元素；
  * heapify(x)：列表就地转换为堆
* 替换
  * heapreplace(heap, item)：删除一个最小元素，再加入新元素；
  * heapushpop(heap, item)：加入一个新元素，再删除列表中的最小元素；
* 消费
  * heappop(heap)：拿出最小的元素；
* 合并
  * merge(*iterables, key=None, reverse=False)：使用一个堆一次一个元素地生成一个新的有序序列，返回一个生成器对象。

```python
print(list(heapq.merge([1, 3, 5, 7], [0, 2, 4, 8], [5, 10, 15, 20], [], [25])))
print(list(heapq.merge(['dog', 'horse'], ['cat', 'fish', 'kangaroo'], key=len)))
h1 = [3, 5, 4]
h2 = [8, 1, 19]
mr = heapq.merge(h1, h2)
print(h1)
print(h2)
print(mr)
print(next(iter(mr)))
print(next(iter(mr)))
'''
[0, 1, 2, 3, 4, 5, 5, 7, 8, 10, 15, 20, 25]
['dog', 'cat', 'fish', 'horse', 'kangaroo']
[3, 5, 4]
[8, 1, 19]
<generator object merge at 0x000001A65B083510>
3
5
'''
```



#### 优先级队列

```python
# 优先级队列

class PriorityQueue:
    def __init__(self):
        self._queue = []
        self._index = 0

    def push(self, item, priority):
        # heappush(使用的列表（列表）, (优先级, 插入顺序, 插入元素))，优先级从低到高，加”-“保证从高到底
        heapq.heappush(self._queue, (-priority, self._index, item))
        self._index += 1

    def pop(self):
        # heappop(队列)[-1]
        return heapq.heappop(self._queue)[-1]


class Item:
    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return 'Item({!r})'.format(self.name)


q = PriorityQueue()
q.push(Item('foo'), 1)
q.push(Item('xoo'), 2)
q.push(Item('goo'), 5)
q.push(Item('eoo'), 6)
q.push(Item('poo'), 1)

print(q.pop())
print(q.pop())
print(q.pop())
print(q.pop())
print(q.pop())
```



### 字典

#### 字典中映射多个值-defaultdict

* defaultdict(list)：值是一个列表，有序的。
* defaultdict(set)：值是一个集合，可去重的。

```python
from collections import defaultdict

# 键映射多个值——有序（list)
d = defaultdict(list)
d['a'].append(1)
d['a'].append(2)
d['a'].append(2)
d['b'].append(2)
print(d)  # defaultdict(<class 'list'>, {'a': [1, 2, 2], 'b': [2]})
# 键映射多值——去重（set)
d = defaultdict(set)
d['a'].add(1)
d['a'].add(2)
d['a'].add(2)
d['b'].add(2)
print(d)  # defaultdict(<class 'set'>, {'a': {1, 2}, 'b': {2}})
print(d['a'])  # {1, 2}

# defaultdict会自动为将要访问的键创建映射实体（存在修改，存在key添加key）。
# 使用dict中的setdefault代替
d = {}
d.setdefault('a', []).append(1)
d.setdefault('a', []).append(1)
d.setdefault('b', []).append(1)
d.setdefault('b', []).append(1)
print(d)  # {'a': [1, 1], 'b': [1, 1]}
```



#### 字典排序-OrderedDict()

* OrderedDict 内部维护着一个根据键插入顺序排序的双向链表。每次当一个新的元 素插入进来的时候，它会被放到链表的尾部。对于一个已经存在的键的重复赋值不会 改变键的顺序。
* 需要注意的是，一个 OrderedDict 的大小是一个普通字典的两倍，因为它内部维 护着另外一个链表。所以如果你要构建一个需要大量 OrderedDict 实例的数据结构的 时候 (比如读取 100,000 行 CSV 数据到一个 OrderedDict 列表中去)，那么你就得仔细 权衡一下是否使用 OrderedDict 带来的好处要大过额外内存消耗的影响。

```python
from collections import OrderedDict, defaultdict

d = OrderedDict()
d['foo'] = 1
d['boo'] = 2
d['xoo'] = 3
d['coo'] = 4
print(d)
'''
OrderedDict([('foo', 1), ('boo', 2), ('xoo', 3), ('coo', 4)])
'''
```







### zip()

**zip()** 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后创建的是一个只能访问一次的迭代器，这样做的好处是节约了不少的内存。

python3 zip()是一个可迭代对象

python2 zip()是一个元组

```python
print(zip([1, 2, 3], ['a', 'b', 'c'])) # <zip object at 0x0000028905DF9900>
zp = sorted(zip([1, 2, 3], ['a', 'b', 'c']))
print(zp)  # [(1, 'a'), (2, 'b'), (3, 'c')]
```





### 删除序列中相同的元素保持顺序

```python
# 要保持顺序最好由生成器def keep_order(items):    st = set()    for item in items:        if item not in st:            yield item            st.add(item)hi = [1, 3, 4, 67, 4, 5, 8, 1]print(list(keep_order(hi)))  # [1, 3, 4, 67, 5, 8]# 不要求顺序直接使用set()print(set(hi))  # {1, 67, 3, 4, 5, 8}
```





### 命名切片

明明切片解决下标硬编码，造成代码的可读性可可维护大大降低。

slice()，内置函数创建一个切片对象。

```python
items = '---melon----22------xichong'
name = slice(3, 8)
age = slice(12, 14)
length = len(items)
address = slice(length - 7, length)
print(f'我的名字叫{items[name]}，今年{items[age]},我住在{items[address]}')
# 我的名字叫melon，今年22,我住在xichong
```



### 统计序列中出现次数

Counter对象在几乎所有需要制表或者计数的场合非常有用。

```python
from collections import Counter
```

```python
from collections import Counter

words = [
    'look', 'into', 'my', 'eyes', 'look', 'into', 'my', 'eyes',
    'the', 'eyes', 'the', 'eyes', 'the', 'eyes', 'not', 'around', 'the', 'eyes', "don't", 'look', 'around', 'the',
    'eyes', 'look', 'into', 'my', 'eyes', "you're", 'under'
]

word_counts = Counter(words)  # 出现频率最高的    3 个单词
top_three = word_counts.most_common(3)
print(top_three)
# [('eyes', 8), ('the', 5), ('look', 4)]

# 统计单词出现的次数
print(word_counts['look'])  # 4
print(word_counts['xxxx'])  # 0

morewords = ['why', 'are', 'you', 'not', 'looking', 'in', 'my', 'eyes']

# 手动计数
for word in morewords:
    word_counts[word] += 1

print(word_counts['eyes']) # 9

# update
print(word_counts) 
# Counter({'eyes': 9, 'the': 5, 'look': 4, 'my': 4, 'into': 3, 'not': 2, 'around': 2, "don't": 1, "you're": 1, 'under': 1, 'why': 1, 'are': 1, 'you': 1, 'looking': 1, 'in': 1})
word_counts.update(morewords)
print(word_counts)
# Counter({'eyes': 10, 'my': 5, 'the': 5, 'look': 4, 'into': 3, 'not': 3, 'around': 2, 'why': 2, 'are': 2, 'you': 2, 'looking': 2, 'in': 2, "don't": 1, "you're": 1, 'under': 1})

# 数学运算
a = Counter(words)
b = Counter(morewords)
c = a + b
print(a)
print(b)
print(c)
'''
Counter({'eyes': 8, 'the': 5, 'look': 4, 'into': 3, 'my': 3, 'around': 2, 'not': 1, "don't": 1, "you're": 1, 'under': 1})
Counter({'why': 1, 'are': 1, 'you': 1, 'not': 1, 'looking': 1, 'in': 1, 'my': 1, 'eyes': 1})
Counter({'eyes': 9, 'the': 5, 'look': 4, 'my': 4, 'into': 3, 'not': 2, 'around': 2, "don't": 1, "you're": 1, 'under': 1, 'why': 1, 'are': 1, 'you': 1, 'looking': 1, 'in': 1})

'''
d = a - b
print(d)
'''
Counter({'eyes': 7, 'the': 5, 'look': 4, 'into': 3, 'my': 2, 'around': 2, "don't": 1, "you're": 1, 'under': 1})
'''
```



###  通过key为字典列表排序

根据某个或某几个字典字段为列表排序

```python
from operator import itemgetter
```



```python
from operator import itemgetter

rows = [
    {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003}, {'fname': 'David', 'lname': 'Beazley', 'uid': 1002},
    {'fname': 'John', 'lname': 'Cleese', 'uid': 1001}, {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
]

rows_by_ifname = sorted(rows, key=itemgetter('lname', 'uid'))
# print(rows_by_ifname)
for row in rows_by_ifname:
    print(row)

'''
{'fname': 'David', 'lname': 'Beazley', 'uid': 1002}
{'fname': 'John', 'lname': 'Cleese', 'uid': 1001}
{'fname': 'Brian', 'lname': 'Jones', 'uid': 1003}
{'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
'''
```

itemgetter() 有时候可以用lambad表达式替代

~~~python
rows_by_lfname = sorted(rows, key=lambda r: (r['lname'],r['fname']))
~~~

同样适用于max()和min()方法中

```python
print(min(rows, key=itemgetter('uid')))
print(max(rows, key=itemgetter('uid')))
'''
{'fname': 'John', 'lname': 'Cleese', 'uid': 1001}
{'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
'''
```

亦可以用于heapq求最大的几个字典或最小的几个字典

```python
small_3 = heapq.nsmallest(2, rows, key=itemgetter('uid'))
print(small_3)
big_3 = heapq.nlargest(2, rows, key=itemgetter('uid'))
print(big_3)
'''
[{'fname': 'John', 'lname': 'Cleese', 'uid': 1001}, {'fname': 'David', 'lname': 'Beazley', 'uid': 1002}]
[{'fname': 'Big', 'lname': 'Jones', 'uid': 1004}, {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003}]
'''
```



### 为不支持原生比比较的对象排序

~~~ python
from operator import attrgetter


class User:
    def __init__(self, user_id):
        self.user_id = user_id

    def __repr__(self):
        return 'User({})'.format(self.user_id)


users = [User(99), User(22), User(33)]
print(users)
print(sorted(users, key=attrgetter('user_id')))
~~~



### 为字段记录分组

groupby()函数，扫描整个序列并且查找连续相同值（或者根据指定key函数返回值相同）的元素序列。在每次迭代的时候，返回一个值和一个迭代器对象。

**<font color=red>注意</font>：** 在分组之前一定要根据指定字段排好序，groupby()仅检查连续的元素。

~~~python
from itertools import groupby
~~~



字典分组

```python
from itertools import groupby
from operator import itemgetter

rows = [
    {'address': '5412 N CLARK', 'date': '07/01/2012'}, {'address': '5148 N CLARK', 'date': '07/04/2012'},
    {'address': '5800 E 58TH', 'date': '07/02/2012'}, {'address': '2122 N CLARK', 'date': '07/03/2012'},
    {'address': '5645 N RAVENSWOOD', 'date': '07/02/2012'}, {'address': '1060 W ADDISON', 'date': '07/02/2012'},
    {'address': '4801 N BROADWAY', 'date': '07/01/2012'}, {'address': '1039 W GRANVILLE', 'date': '07/04/2012'},
]
# 首先要为字典排序
rows.sort(key=itemgetter('date'))
for date, items in groupby(rows, key=itemgetter('date')):
    print(date)
    for i in items:
        print('         ', i)
'''
07/01/2012
          {'address': '5412 N CLARK', 'date': '07/01/2012'}
          {'address': '4801 N BROADWAY', 'date': '07/01/2012'}
07/02/2012
          {'address': '5800 E 58TH', 'date': '07/02/2012'}
          {'address': '5645 N RAVENSWOOD', 'date': '07/02/2012'}
          {'address': '1060 W ADDISON', 'date': '07/02/2012'}
07/03/2012
          {'address': '2122 N CLARK', 'date': '07/03/2012'}
07/04/2012
          {'address': '5148 N CLARK', 'date': '07/04/2012'}
          {'address': '1039 W GRANVILLE', 'date': '07/04/2012'}
'''
```



对象分组

```python
from itertools import groupby
from operator import attrgetter


class User:
    def __init__(self, name, address, create_time):
        self.name = name
        self.address = address
        self.create_time = create_time

    def __repr__(self):
        return f'name：{self.name}，address：{self.address}，create_time：{self.create_time}。'


users = [
    User('melon', 'xichong', '1999-10-05'),
    User('yiyan', 'nanchong', '1997-10-05'),
    User('chengxi', 'xichong', '2000-10-05'),
    User('feiao', 'chengdu', '1999-10-05'),
    User('liaotao', 'shehong', '1999-9-05'),
    User('qiaolu', 'xichong', '1999-10-05'),
]
users.sort(key=attrgetter('address'))
for user in users:
    print(user)

for address, items in groupby(users, key=attrgetter('address')):
    print(address)
    for item in items:
        print(' ' * 10, item)
'''
name：feiao，address：chengdu，create_time：1999-10-05。
name：yiyan，address：nanchong，create_time：1997-10-05。
name：liaotao，address：shehong，create_time：1999-9-05。
name：melon，address：xichong，create_time：1999-10-05。
name：chengxi，address：xichong，create_time：2000-10-05。
name：qiaolu，address：xichong，create_time：1999-10-05。

chengdu
           name：feiao，address：chengdu，create_time：1999-10-05。
nanchong
           name：yiyan，address：nanchong，create_time：1997-10-05。
shehong
           name：liaotao，address：shehong，create_time：1999-9-05。
xichong
           name：melon，address：xichong，create_time：1999-10-05。
           name：chengxi，address：xichong，create_time：2000-10-05。
           name：qiaolu，address：xichong，create_time：1999-10-05。
'''
```



根据对象属性或者键存在一个数据结构中

```python
from collections import defaultdict
groupby_user_dict = defaultdict(set)
for user in users:
    groupby_user_dict[user.address].add(user)
print(groupby_user_dict)
for k, v in groupby_user_dict.items():
    print(f'{k}：{v}')
    
'''
defaultdict(<class 'set'>, {'chengdu': {name：feiao，address：chengdu，create_time：1999-10-05。}, 'nanchong': {name：yiyan，address：nanchong，create_time：1997-10-05。}, 'shehong': {name：liaotao，address：shehong，create_time：1999-9-05。}, 'xichong': {name：qiaolu，address：xichong，create_time：1999-10-05。, name：melon，address：xichong，create_time：1999-10-05。, name：chengxi，address：xichong，create_time：2000-10-05。}})
chengdu：{name：feiao，address：chengdu，create_time：1999-10-05。}
nanchong：{name：yiyan，address：nanchong，create_time：1997-10-05。}
shehong：{name：liaotao，address：shehong，create_time：1999-9-05。}
xichong：{name：qiaolu，address：xichong，create_time：1999-10-05。, name：melon，address：xichong，create_time：1999-10-05。, name：chengxi，address：xichong，create_time：2000-10-05。}
'''
```



### 过滤序列元素

1. 列表推导式：输入非常大时、会占用大量内存。
2. 生成器表达式：较为简单的过滤规则
3. filter(fun, sequence)函数：较为复杂的过滤规则

```python
# 列表推导式权限，输入非常大时，占用大量内存。对内存较为敏感，生成器时一个更好的选择。
lis = [1, 3, -11, 22, 0, 79, -13]
print([i for i in lis if i > 0])
print([i for i in lis if i < 0])
'''
[1, 3, 22, 79]
[-11, -13]
'''

# 生成器
pos = (i for i in lis if i < 0)
print(pos)  # <generator object <genexpr> at 0x000001975894EBA0>
for p in pos:
    print(p)
'''
-11
-13
'''

# 过滤条件较为复杂使用内建函数filter()，放回的是一个迭代器
vals = ['1', '2', '-3', 'N/A', '-', '11']


def is_int(val):
    try:
        x = int(val)
        return True
    except ValueError:
        return False


res = list(filter(is_int, vals))
print(res)  # ['1', '2', '-3', '11']
```



### 映射名称到序列元素-namedtuple

```python
from collections import namedtuple

# 解决列表或元组下标难以阅读
# 返回 Python 中标准元组类型子类的一个工厂方法。需要传递一个类型名和需要的字段，然后就会返回一个类，可以初始化这个类， 定义的字段传递值等
User = namedtuple('User', ['name', 'age', 'gender'])
user = User('melon', 22, '男')
print(user.name)
print(user.age)
print(user.gender)

print(*user)


# namedtuple可以当作一个类的实例，它也可以与元组交换，支持所有的元组操作。
# 主要用途将代码从下标中解脱出来
def cost(records):
    names = []
    for rec in records:
        data = User(*rec)
        names.append(data.name)

    return names


res = (('melon', 22, '男'), ('aixi', 22, '男'), ('wwu', 22, '男'))
print(cost(res))

# 另一个用途代替字典，字典需要更多的存储空间，构建很大包含字典的数据结构时，命名元组更高效，但是它不可更改。
# namedtuple提供了_replace()方法更改属性，但返回一个新的namedtuple，原元组没变
user = User('xxx', 0, '女')
u1 = user._replace(gender='男')
print(u1)
u2 = u1._replace(name='melon', age=22)
print(u2)
```



## 字符串和文本

  







## Python安装问题



### pip错误



#### No module named 'pip'

在命令行中输入以下命令，修复pip

~~~shell
python -m ensurepip
python -m pip install --upgrade pip
~~~













<font color="gray">注：本文纯用于个人学习笔记。</font>
