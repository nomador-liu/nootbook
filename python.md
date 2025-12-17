# 							python

# 一、Python的数值类型

## int整数

整数在Python里面没有大小限制，比如2**1024可以得到一个非常大的长的整数类型
二进制用0b或者0B开头，后面跟上0或者1
八进制用0o后者0O开头，后面跟上0～7
十六进制用0x或者0X开头，后面跟上0~9, A~F
hex(100)  将十进制100换算成十六进制
oct(100)  将十进制100换算成八进制
bin(100)  将十进制100换算成二进制
int(字符串, X进制)  将一个字符串看作是X进制，得到它的十进制结果
X进制：其中任意进制都是可以的，但是世界上没有0和1进制。
int() base must be >= 2 and <= 36, or 0

在int的数值类型中，可以使用下划线来对数字进行分割，主要是为了美观易读。

关于除法的说明
/  真正的除法，按照浮点数的运算规则进行除法运算
// 小数舍入的除法，这种除法如果两个数都是整数，结果依然是整数；如果有任何一个是浮点数，结果也是浮点数
比如10//3结果3.0
另外，//的舍入实际上是得到距离它结果最近的且比结果小的整数——向下取整
5//2, 5//-2    # 得到结果是(2, -3)

实际上在开发过程中，如果涉及到舍入，尽量考虑采用math包，而不去使用//

```python
import math
math.floor(2.5)     # floor就是向下取整，结果是2
math.floor(-2.5)    # 结果是-3
math.trunc(2.5)     # trunc就是截断，结果是2
math.trunc(-2.5)    # 结果是-2
```

整数的位运算：先将参加运算的数都换成补码，然后逐位进行运算
& 按位与运算，有0就是0，其余都是1
| 按位或运算，有1就是1，其余都是0
^ 按位异或运算，相同为0，不同为1
~ 按位求反，1变0，0变1
<<左移，将一个数低位补0，左移一位就相当于对一个数乘以2
01000 

\>>右移，将一个数低位抛弃，高位填写符号位，右移一位就相当于对一个数除以2

x=99 它的二进制是'0b1100011'，一共7位

```python
x.bit_length()   # 结果是7, 表示二进制的总长度
len(bin(99))-2   # 之所以减掉2，是因为要去掉二进制前面的0b
```



## float浮点数 float

Python的浮点数计算结果遵循IEEE754国际规范。
这种浮点数的运算是通过硬件来直接进行运算的，虽然精度有损失，但是速度很快。

科学记数法
常见的浮点数3.14， 3.14e-1（科学计数法，e或者E都是可以的）

## complex复数

Python的复数实际上是两个浮点数组成的，实部和虚部各用一个浮点数
虚部用j或者J都是可以的，比如
1j

```python
type(1j)   # 结果是<class 'complex'>
```



## 小数类型Decimal

此处的小数类型可以被认为是任意精度的浮点数。

```python
from decimal import Decimal         # 从decimal这个模块中导入Decimal这个类
Decimal('0.1')+Decimal('0.1')+Decimal('0.1')-Decimal('0.3')   # 结果是Decimal('0.0')

Decimal('10.0') / Decimal('3.0')    # 结果是Decimal('3.333333333333333333333333333')

import decimal                      # 用import导入整个的decimal模块
decimal.getcontext().prec=6         # 设置浮点运算的精度
Decimal('10.0') / Decimal('3.0')    # Decimal('3.33333')
Decimal('100.0') / Decimal('3.0')   # Decimal('33.3333')
Decimal('1.0') / Decimal('3.0')     # Decimal('0.333333')
```

假如我们想在某次运算中临时改变运算精度，而不希望整个的上下文都发生改变，可以使用with

```python
import decimal

print(decimal.Decimal('10.0') / decimal.Decimal('3.0'))           # 3.333333333333333333333333333

with decimal.localcontext() as ctx:
    ctx.prec = 2
    print(decimal.Decimal('10.0') / decimal.Decimal('3.0'))       # 此时结果是3.3

print(decimal.Decimal('10.0') / decimal.Decimal('3.0'))           # 3.333333333333333333333333333
```



## 分数Fraction

分数和Decimal其实是一样的，可以被认为是另一种不同的表示形式。

```python
from fractions import Fraction      # 注意模块名有s结尾，但是类名没有s结尾
x=Fraction(1, 3) 
y=Fraction(4, 6)
print(x)            # 结果是1/3，此处是str类型的字符串输出
x                   # 如果在终端中直接输入x，结果是Fraction(1, 3)，此处是repr类型的字符串输出
x + y               # 如果在终端中直接输入x，结果是Fraction(1, 1)，此处是repr类型的字符串输出
print(x+y)          # 结果是1/3，此处是str类型的字符串输出

# 可以通过浮点数来创建一个分数类型，比如
Fraction(0.25)结果就是Fraction(1, 4)
```





##  字符串

一、str类型的字符串 

二、bytes类型的字符串 

三、bytearray类型的字符串 

四、Python字符串的操作 

五、字符串的格式化  

一、str类型的字符串 

1. 普通字符串：用单引号或者双引号包含起来的内容 
2.  多行字符串：用三个单引号或者双引号包含起来的内容 
3.  原始字符串raw string：在字符串前面加上r，使得\保持原样 

```python
print(r"\n\n") # 结果是\n\n 转义字符：用\开头的特殊内容, \n \t \\ \' \" print('\\')  # 结果是\ 
print('\"This\'s\"')  # 结果是"This's" print('a\tb')  # 结果是a        b，\t表示8个空格。有的软件为了美观会显示为4个空格，但这在软件内通常都是可调整的。 print('C:\Users\tom\Desktop')是错误的，正确的写法是 
print('C:\\Users\\tom\\Desktop')  # 但是这种写法太繁琐，所以我们发明了r开头的原始字符串 
print(r'C:\Users\tom\Desktop')  
```



二、bytes字节字符串 在一个字符串的前面加上b，用来表示这是一个二进制的字符串。这个字符串必须是ASCII格式或者字节。 

```python
print(b'Hello, World') print(b'你好') # ❌错误的，因为'你好'不是ASCII字符 type(b'ss')  # 结果是  ‼️ 将bytes类型转换为str类型 
byte_str = b'Hello, World' 
print(str(byte_str))  # 结果是b'Hello, World' 
utf_str = byte_str.decode('utf-8')  # decode是一个函数，用于将bytes转化为特定格式的字符串，此处的utf-8表示Unicode 
print(utf_str)  # 结果是Hello, World  ‼️以上两种字符串都是不允许发生变化的（内容）  
```



三、bytearray是bytes的可修改版本，在本质上可以被理解为一个列表。 

```python
ba = bytearray(b'hello') 
ba = bytearray('hello', 'utf-8') 
ba = bytearray([104, 101, 108, 108, 111]) # hello 
ba = bytearray(5) # 这里的5表示我有5个ASCII字符的空间，每一个空间默认是0，即\x00 ，所以结果是b'\x00\x00\x00\x00\x00' ba = bytearray(5)类似于上面的ba = bytearray([0, 0, 0, 0, 0])   
ba = bytearray([104, 101, 108, 108, 111])   # 范围是0～255 
ba[0] = 72  # ⚠️说明了bytearray是可以修改的  
```

Python3中的str实际上是Unicode的“码点”的序列，而不是字节的序列。 之所以这么说，是因为一个码点可能是1个字节，也可能是多个字节。 比如“中”这个字实际上就是一个码点，但是它要用三个字节来存放。

```python
\xhh            # \x表示十六进制转义字符，后面必须跟2位16进制数 比如'\xAB'结果'«'， '\xABC'的结果是'«C'， '\xAZC'直接报错了 
\ohhh           # \o表示八进制的转义字符，后面必须跟3位8进制数 
\uhhhh          # \u后面表示必须是4位的16进制Unicode字符 
\Uhhhhhhhh      # \U后面表示必须是8位的16进制Unicode字符 如果一个\后面跟的内容不是一个正确的转义字符，Python也不会报错，而是直接当作普通字符串对待。  
```

四、Python字符串的操作 

字符串的基本操作 

⚠️我们发现str，list，tuple等都支持+和*运算 字符串的基本操作可以分成3大类：

运算(+, *), 分片([])，调用方法*

  'abc''def' 会合成一个新的'abcdef' 

```python
'abc' + 'def' 		# 会合成一个新的'abcdef'
'abc' + 123 		# 会报错（注意其它语言中基本是合法的）
'*' * 10  			# 会得到'**********' 
'h' in 'hello' 		# 会得到True 
'he' in 'hello' 	# 会得到True
'ho' in 'hello' 	# 会得到False 
'hello' in 'hello' 	# 会得到True
'hello ' in 'hello'	# 会得到False
name = 'tom' 
for c in name: 
    print(c, end=' ') 在终端中需要按2次回车表示这个句子结束。结果是t o m 'hello'[0]												# 产生一个新的字符串是'h'
'hello'[-2]        	# 从后面开始向前计数，最后一位是-1，产生一个新的字符串是'l' 
'hello'[1:3]      	# 产生一个新的字符串是'el' 
str(32)    			# 将参数换成str 
repr(32)   			# repr是机器内部存放的字符串的形式 
ord('s')   			# 求字符s对应的数值 
chr(64)    			# 求64对应的字符  
```

字符串的常见方法 

```python
'spammy'.replace('mm', 'uuu')    						# 结果是'spauuuy'，此处的结果是一个新的字符串，并非在原始内容上进行替换 
'xxxxYYYxxxxYYYxxxxYYY'.replace('xxxx', 'ZZZZ', 1)   	# 结果是'ZZZZYYYxxxxYYYxxxxYYY' 
'xxxxYYYxxxxYYYxxxxYYY'.replace('xxxx', 'ZZZZ', 2)   	# 结果是'ZZZZYYYZZZZYYYxxxxYYY' 
l = list('hello')     									# 结果是['h', 'e', 'l', 'l', 'o'] 
s = str(['h', 'e', 'l', 'l', 'o'])   					# 结果是"['h', 'e', 'l', 'l', 'o']"，而不是'hello' 
s = ''.join(l)     										# s的结果就是'hello' 
'SPAM'.join(['aaa', 'bbb', 'ccc'])   					# 结果是'aaaSPAMbbbSPAMccc' 
'aaa bbb ccc'.split(' ')           						# 结果是['aaa', 'bbb', 'ccc'] 
'aaa  bbb  ccc '.split(' ')        						# 结果是['aaa', '', 'bbb', '', 'ccc', ''] 
'aaa   bbb   ccc   '.split(' ')    						# 结果是['aaa', '', '', 'bbb', '', '', 'ccc', '', '', ''] 
'  h  e l l   o\n \n'.rstrip()    						# 结果是'  h  e l l   o' 
'Hello'.upper()  										# 结果是HELLO 
'Hello'.lower()  										# 结果是hello 
'hello}'.isalpha()    									# 判断字符串是否只包含26个英文字母 
'hello'.endswith('lo') 
'hello'.startswith('H')    								# 结果是False，注意endswith和startswith严格区分大小写 
```

五、字符串的格式化 

字符串格式化的3种方式：

1. f字符串(天然的，原生的)，调用format方法，用字符串做%运算 

在字符串前面加上f，用于对字符串进行格式化。 

Python 3.6版本之后添加的新写法，目前看来属于建议使用的方法 

```python
name = 'Tom' 
age = 18 
print(f'My name is {name} and I am {age} years old.') 
x = 3 
y = 5 
print(f'the sum of {x} and {y} is {x + y}.') 
pi = 3.141592654 
print(f'{pi:.2f}')  # 在变量的后面加上:后写上格式化内容即可。 
print(f'{pi:,}')  
num = 1000000000 
print(f'{num:,}')  # 结果是1,000,000,000 
num = 33 
print(f'|{num:5}|')     # |   33|默认是靠右对齐 
print(f'|{num:<5}|')    # <表示左对齐  
```

2. 字符串格式化方法，这种方法大概是Python 2.6才开始引入

```python
'{}, {} and {}'.format('spam', 'of', 'eggs') 
'{0}, {1} and {2}'.format('spam', 'of', 'eggs') 
'{motto}, {pork} and {food}'.format(motto='spam', pork='ham', food='eggs') # 结果是'spam, ham and eggs' 
'{motto}, {0} and {food}'.format('ham', motto='spam', food='eggs')  
'{motto}, {0} and {food}'.format(42, motto=3.14, food=[1,2])  # 结果是'3.14, 42 and [1, 2]' 


import sys 
'My {1[kind]} runs {0.platform}'.format(sys, {'kind':'laptop'})  # 结果是'My laptop runs darwin' 
'My {map[kind]} runs {sys.platform}'.format(sys=sys, map={'kind':'laptop'})   # 结果和上一个一致 
'{0:10} = {1:10}'.format('spam', 123.4567)      # 结果是'spam       =   123.4567' 
'{0:>10} = {1:<10}'.format('spam', 123.4567)    # 结果是'      spam = 123.4567  ' 
'{0.platform:>10} = {1[kind]:<10}'.format(sys, dict(kind='laptop')) # 结果是'    darwin = laptop    ' 
'{.platform:>10} = {[kind]:<10}'.format(sys, dict(kind='laptop'))   # 结果是'    darwin = laptop    ' 
'{0:e}, {1:.3e}, {2:f}'.format(3.14159, 3.14159, 3.14159) # 结果是'3.141590e+00, 3.142e+00, 3.141590' # e表示用科学计数法
'{0:,d}'.format(10000000) # 结果是'10,000,000' 
'{0:.{1}f}'.format(1/3.0, 4) # 结果是'0.3333'  
```

3. ⚠️不建议使用了⚠️从Python刚刚诞生就存在的字符串格式化表达式 '...%..%.' % (value, vaule) 一个字符串可以在其中嵌入%开头的内容，通常是%后面跟上一个字母，用来表示此处有一个占位 

```python
'%%%d' % (1)    # %d表示一个十进制整数的数值类型，%%表示一个%（转义） 
'%d%%' % (30)    # 30% 
'%-10s, %.4f' % ('hello', 3.1415926)  # -表示左对齐，10是总宽度，.4指小数点后4位，四舍五入 
'%s' % (r'c:\z')   # 结果'c:\\z' 
'%r' % (r'c:\z')   # %r表示以repr的方式显示，结果是"'c:\\\\z'" 
'a%cc' % ('b')   # %c表示只打印一个字符，不能多也不能少 %o, %x, %X分别表示以8进制和16进制进行显示 
'%.2E' % (314.13247) 
'%.2e' % (314.13247) 
x = 1.23456789 
'%6.2f | %05.2f | %+06.1f' % (x, x, -1.23) # 结果是'  1.23 | 01.23 | -001.2' 其中0表示在高位补0，+表示显示结果的符号位 
'%.*f' % (4, 3.14159) # 结果是3.1416，其中*也需要在后面给定 
'%(qty)d more %(food)s' % {'qty': 1, 'food': 'spam'} # 结果是'1 more spam' 
```

## 列表，元组，字典，集合

1. 掌握列表，元组，字典，集合四个复杂数据类型的基本使用

2. 掌握简单的文件操作

3. 掌握简单的if，match，for，while语句

4. 掌握扩展序列解包

5. 布尔，短路和三元表达式

6. 推导式

### 集合

在 Python 里，集合（Set）是一种无序且元素唯一的数据结构，它的主要用途包括成员检测、消除重复元素，还能进行交集、并集、差集等数学集合运算。

一、集合的创建
创建集合有两种方式，一种是使用花括号 {}，另一种是使用 set() 函数。不过要注意，创建空集合只能用 set()，因为 {} 创建的是字典。

```python
# 创建包含元素的集合
fruits = {"apple", "banana", "cherry"}  # 使用花括号
empty_set = set()  # 创建空集合

# 从列表创建集合（自动去重）
numbers = set([1, 2, 2, 3, 3, 3])  # 结果为 {1, 2, 3}
```

二、集合的基本操作

集合支持添加、删除元素等操作。

```python
# 添加元素
fruits.add("orange")  # 添加单个元素
fruits.update({"grape", "mango"})  # 添加多个元素

# 删除元素
fruits.remove("apple")  # 元素不存在会报错
fruits.discard("apple")  # 元素不存在不会报错
popped_fruit = fruits.pop()  # 随机移除一个元素（因为集合无序）
```

三、集合的常用方法

集合有很多实用的方法，用于集合间的比较和运算。

```python
# 判断元素是否存在
if "banana" in fruits:
    print("Yes")

# 集合长度
print(len(fruits))  # 输出集合中元素的数量
```

四、集合运算

集合可以进行多种数学运算。

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

# 交集：同时存在于两个集合中的元素
print(a & b)  # 输出 {3, 4}，也可以用 a.intersection(b)

# 并集：两个集合中的所有唯一元素
print(a | b)  # 输出 {1, 2, 3, 4, 5, 6}，也可以用 a.union(b)

# 差集：在a中但不在b中的元素
print(a - b)  # 输出 {1, 2}，也可以用 a.difference(b)

# 对称差集：只在一个集合中出现的元素
print(a ^ b)  # 输出 {1, 2, 5, 6}，也可以用 a.symmetric_difference(b)
```

五、集合的不可变版本：frozenset

`frozenset` 是不可变的集合，创建后不能修改，因此它可以作为字典的键或其他集合的元素。

```python
fs = frozenset([1, 2, 3])
# fs.add(4)  # 会报错，因为frozenset不可变
```

六、集合的应用场景

集合在很多场景下都很有用。

```python
# 去重
lst = [1, 2, 2, 3, 3, 3]
unique_lst = list(set(lst))  # 结果：[1, 2, 3]（顺序可能变化）

# 判断两个列表是否有重复元素
list1 = [1, 2, 3]
list2 = [3, 4, 5]
if set(list1) & set(list2):
    print("有重复元素")
```

### 元组







## 扩展序列解包

下面是详细的文档材料，没有的部分可以借助下AI学习。


我们可以在变量名的前面添加一个星号进行通用的匹配。

```python
a, *b = 'SPAM'          # a是S，b是['P', 'A', 'M']
*a, b = 'SPAM'          # a是['S', 'P', 'A']， b = 'M'
a, *b, c = 'SPAM'		# a是's' , b = ['P' , 'A'] , c = 'M'
```

扩展序列解包总是会返回一个列表，

```python
a, *b, c = 'SPM'        # b是['P']
a, *b, c = 'SP'         # b是[]
```

扩展序列解包中是不允许出现多个*的，会直接产生错误

```python
a, *b, c, *d = 'ABCD'   # 直接报错
seq = [1, 2, 3, 4]
*a = seq   				# 单独的一个*没有放在元组中会产生错误
*a, = seq  				# 但是a在一个元组中，结果就是正确的，a就是[1, 2, 3, 4]
```

## 文件

Python中的文件是通过顶层函数导入的功能，也可以说是默认就被导入的功能。
很多地方都将文件当作是继列表，元组，字典，集合等之后的一种新的内置类型。

我们在这里讨论的文件都是文本文件。所谓文本文件就是通过记事本打开后人类依然可读的文件。

文件的操作
变量 = open('文件名', '模式')
模式用于控制我们能对这个文件做什么
w：只能对文件写（不能读），如果文件不存在会帮我们建立一个新文件并写入，如果文件存在则覆盖其中的内容重新写
open('file.txt', 'w')
open表示打开一个文件准备进行操作
'file.txt'表示要操作的这个文件的名字，这个名字是可以随便起的
'w'表示我们要对这个文件进行修改，w是write写这个单词的首字母

通常一个文件被打开之后，要被存入一个变量中

```python
f1 = open('file.txt', 'w')
f1.write('hello')  # 此处表示这个hello要被写入文件
f1.write('hello')
f1.write('hello')
f1.close()  # 关闭f1代表的这个文件，如果不关，内容实际上并没有真正写入。
```

⚠️当模式处只有w的时候，表示只能对这个文件进行写操作，而不能进行读

w：只能对文件写，如果文件不存在，则建立新文件；如果文件存在‼️则内容全部覆盖
r：只能对文件读, 如果文件不存在，那么在open的时候就会直接报错
a：a是append表示追加（不能读），如果不存在则建立新文件；如果文件存在，它就在文件结尾进行追加
⚠️w，r和a还可以被写成wt，rt和at，他们的含义完全相同

r+：读写，即能读也可以写，但是要求文件必须存在否则报错，r+实际上还拥有追加的特点。例子参考下面r+的例子

```python
f = open('hello.txt', 'r+')

# 扩展名只是为了通知操作系统用哪个软件去打开这个文件

f.write('hello1\n')
f.write('hello2\n')
f.flush() # 将缓存中的内容立刻写出到硬盘

# 此时的写入实际上是写在了缓存里(内存)，并没有真正写入硬盘

f.seek(2) # 定位到这个文件的第几个字节处
print('第1行:', f.readline(), end='')
print('第2行:', f.readline(), end='')
f.seek(2)
f.write('1234567890\n')

# 程序一旦结束就相当于执行了一个f.close(),一旦文件被关闭，缓存的内容就会被立刻写出

f = open('hello.txt', 'r+')
for l in f:
    print(l, end='')
f.close()

with open('hello.txt') as f:
    for l in f:
        print(l, end='')
```

with叫做上下文管理器，with可以为我们提供自动关闭文件的特性。
open的时候默认就是r操作



## python中的真与假以及三元表达式


Python的真和假是相当奇怪的，有点类似于C中的0和非0
1. 数字0，空对象(空列表，元组，字典)，None都是假
2. 除了假全是真，所有的非0数字和非空对象都是真
3. 常见的布尔运算有not and 和 or

Python中的短路

```python
2 or 3, 3 or 2      # 结果是(2, 3)，or运算如果前面为true后面不再计算，同时结果就是前面的内容
[] or 3, [] or []   # 结果是(3, [])，or运算符前面为false，取后面的内容
False or []         # 结果是[]

2 and 3, 3 and 2    # 结果是(3, 2)，and运算如果前面为true，我们就直接取后面的结果了
[] and [], 3 and [], True and []   # 结果是([], [], [])
True and [1]        # 结果是[1] 
```

Python的三元表达式（类似于其它语言的?:运算符，语法： 条件?结果1:结果2）
语法： 结果1 if 条件 else 结果2
执行： 条件为Ture，就取结果1，否则取结果2

```python
't' if 'spam' else 'f'  # 结果是't'
't' if '' else 'f'      # 结果是'f'
't' if [] else 'f'      # 结果是'f'
A = 't' if [] else 'f'  # 结果不会显示，结果是'f'，结果被存入了变量A
```


上面的式子涉及到运算符的优先级，实际上它等价于A = ('t' if [] else 'f')



## 推导式

推导式出现在[]中，如果出现在()中会得到可迭代的生成器表达式
语法：
[操作 for 变量 in 可迭代对象]

```python
L = [1, 2, 3, 4, 5]
L = [x + 1 for x in L]
```

L的结果是[2, 3, 4, 5, 6]

语法：
[操作 for 变量 in 可迭代对象 if 条件]

```python
L = [1, 2, 3, 4, 5]
L = [x + 1 for x in L if x % 2 == 0]
```

这个推导式的顺序是先对L的每一个元素对2求余，如果结果是0，则对这个元素进行加一
L的结果是[3, 5]，这个结果是基于L=[1, 2, 3, 4, 5]的基础上得到的

```python
line = [line + '@' for line in open('mytext.txt') if line[0] == 'H']
```

这个推导式的含义是对H开头的文件中的行进行拼接@

推导式也是可以嵌套的，但是通常都难以理解，应该尽量避免使用

```python
[x + y for x in 'abc' for y in 'def']
```

结果是['ad', 'ae', 'af', 'bd', 'be', 'bf', 'cd', 'ce', 'cf']
就类似于其它语言中的多重循环

```python
res = []
for x in 'abc':
    for y in 'def':
        res.append(x + y)

print(res)
```

⚠️推导式除了简单之外，它还是一种更基本的语法结构。
追求这种简单语法结构的好处就是它可以在更多的场景下使用。



## NoneType

NoneType：它的表现是None

​	NoneType是一个独立类型，它唯一的值就是None
​	比如参数的默认值，函数的返回值，类型的声明
​	def f(a=None) -> None:
​	    print(a)
​	    return None  # 可以省略

```python
print(f())
```



## 生成器和推导式	

​	

```python
[x+1 for x in range(5)]		# 推导式产生列表[1, 2, 3, 4, 5]
{x+1 for x in range(5)}		# 推导式产生集合{1, 2, 3, 4, 5}
{x:x+1 for x in range(5)}	# 推导式产生字典{0: 1, 1: 2, 2: 3, 3: 4, 4: 5}
```

```python
(x+1 for x in range(5))	    

# ‼️不是推导式产生元组，结果是生成器<generator object <genexpr> at 0x100e75ff0>

# 如果我非要推导式产生元组，只能产生list后再转换，比如tuple([x+1 for x in range(5)])

# 生成器如何使用呢？

g = (x+1 for x in range(5))	
next(g) # 1

‼️如何选择？如果一次遍历生成器优先，效率高，速度快。如果要反复使用/切片等操作就推导式。
```



## 顶层函数查询和常见函数 

```python
import builtins  # Python会自动导入这个模块
dir(builtins)
```

结果一般分为几个部分：异常类、双下划线开头和结尾的是内置标识符、值常量和函数

###  异常类——略

### 内置标识符

```python
• __name__			类型：str，		含义：当前模块名；在主模块中是 '__main__'
print(__name__)
if __name__ == "__main__":
    print("我是在主程序中运行")

• __doc__			类型：str，		含义：当前模块的文档字符串
"""这是模块文档"""
def add(x, y):
    """返回x与y的和"""
    return x + y

print(__doc__)       # 输出：这是模块文档
print(add.__doc__)   # 输出：返回x与y的和


• __package__		类型：str，		含义：当前模块所属包名
# 在包结构 mypkg/
# ├── __init__.py
# └── mod.py

# mod.py 中：
print(__package__)

• __loader__		类型：loader，	含义：用于加载模块的对象
• __spec__			类型：ModuleSpec	含义：模块规范描述对象
• __build_class__	类型：内置函数		含义：用于创建类定义时调用（编译器内部使用）


• __debug__			类型：bool 		含义：是否开启assert检查；python -O启动时为False
print(__debug__)  	# 默认是True


```

### 值常量

```python
• True, False, None，NotImplemented，Ellipsis

• NotImplemented是一个特殊的单例值，用于二元运算符或比较方法中。表示当前操作不支持这个类型组合——不是没实现函数，而是告诉Python去尝试反向操作。
class A:
    def __eq__(self, other):
        if isinstance(other, A):
            return True
        return NotImplemented

    class B:
        pass

print(A() == A())   # True
print(A() == B())   # False （因为A返回NotImplemented，Python尝试B.__eq__，也不行，于是返回False）

• Ellipsis 是一个内置常量对象（类型是ellipsis），字面量写法是...。主要用于：
多维数组/切片（尤其是 NumPy、Pandas）
占位符或未实现功能的标记
def future_feature():
    ...

```

### 内置函数/类

- 数值与类型转换：abs, int, float, complex, round, divmod, pow, bool, bytes, bytearray, memoryview

- 序列与迭代：len, range, enumerate, zip, reversed, sorted, sum, max, min, all, any

- 容器与类型构造器：list, tuple, dict, set, frozenset, str, slice, object
  	

- 反射与属性操作：getattr, setattr, hasattr, delattr, vars, dir, globals, locals, id, type, isinstance, issubclass, callable, super, property, classmethod, staticmethod

- 输入输出与执行：print, input, open, eval, exec, compile, breakpoint, help, exit, quit
- 函数式与迭代器工具：map, filter, next, iter, aiter, anext
- 字符串与编码：chr, ord, ascii, format, hex, oct, bin
- 版权与版本信息：copyright, credits, license仅在交互解释器中有效。

考试重点：chr, ord

# 分支(if, match)、循环结构

略

# 函数/参数

## 传参数

‼️Python的变量里放的都是地址。
‼️Python的参数传递都是传递地址的副本（拷贝地址一份进去函数里）
‼️在函数中修改地址对象的时候，如果这个对象是可变的就直接修改，如果是不可变对象则创建新对象。
例子1: 传地址副本，但是这个地址里是不可变对象

```python
def f(i): # ‼️此处的i收到的是3c3dce78
    i += 1  # ‼️i中的是一个int，即不可变对象，此时创建一个新对象101，将其地址放入函数内部的i中
    print('函数里面的i是:', i)

i = 100  # 假设100的地址是3c3dce78
print('函数外面的i是:', i)
f(i)  # ‼️此处传递的是地址的副本
print('函数外面的i是:', i)
```

例子2: 传地址副本，但是这个地址里是可变对象
```python
def f(l):
    l[0] = 4  # ‼️因为l是可变对象，Python对其直接修改
    l[1] = 5
    l[2] = 6
    print('函数里面的l是:', l)

l = [1, 2, 3,]
print('函数外面的l是:', l)
f(l)
print('函数外面的l是:', l)
```

例子3: 传地址副本，更像例子1，我等于直接创建了一个新对象给了l
```python
def f(l):
    l = [4, 5, 6]
    print('函数里面的l是:', l)

l = [1, 2, 3,]
print('函数外面的l是:', l)
f(l)
print('函数外面的l是:', l)

```

## 函数参数的各种形态

例子1. 参数按位置和按名字传递，以及参数的默认值

```python
def f(x, y=2, z=3):
    print(x, y, z)

f(1)
f(4, 5)      # x=4， y=5
f(4, y=5) 
f(4, z=6)     
f(z=6, 4)    # ‼️错误：在一个既有名字也按顺序的参数传递时，应该先按顺序给定参数的值
f(z=6, x=4)

def f(x=1, y, z=3):    # 错误：一旦函数的参数有名字和默认值之后，就不再允许出现按位置赋值的参数了
    ...

```

‼️我们自己写函数的时候，要么都用按名字，要么都用按顺序，不要混着来。

例子2. 可变数量的参数，用*表示，它‼️实际上就是一个元组

```python
def f(*args):   
    print(args)

f()                   # 结果是()
f(1)                  # 结果是(1, )
f(1,2,3)              # 结果是(1, 2, 3)
f((1,2,3))            # 结果是((1, 2, 3), )

```

例子3. 按名字传递，用**表示，它实‼️际上就是一个字典

```python
def f(**args): print(args)

f()                  # 结果是{}
f(x=1, y=2)          # 结果是{'x': 1, 'y': 2}

```

例子4. 多种参数写在一起的规则

```python
def f(x, y=2, *args, **args1): print(x, y, args, args1)

def g(x, y=2, **args1, *args): ...  # 错误的，**必须出现在最后的位置

def h(x, *args, y=2): ...  # 正确的，*的位置没有要求


```

例子5. 调用函数时的*和**

```python
def f(*args, **args1): print(args, args1)
f()                                 # () {}

f(1, 2, 3, x=1, y=2)                # (1, 2, 3) {'x': 1, 'y': 2}

f(*(1, 2, 3), **{'x': 1, 'y': 2})   # (1, 2, 3) {'x': 1, 'y': 2}
# 直接传一个元组和字典需要在前面加*和**

f((), {'x': 1, 'y': 2})
# args是((), {'x': 1, 'y': 2})
# **args1是{}

def g(**args): print(args)
g({'x': 1, 'y': 2})  # 错误的
g(**{'x': 1, 'y': 2})  # 正确的{'x': 1, 'y': 2}


```

例子6. 单独的星号
单独的星号表示这个星号后面的所有参数都必须通过名字传递，单独的\*后不允许再出现\*和**

```python
def f(x, *, y, z = 1): print(x, y, z)    # 函数f只有三个参数，即x，y和z
f(1, y=2, z=3) 

def f(x, **):...  # Python中暂时没有单独的两个**，这是错误的

```



##  作用域

作用域：一个变量起作用的范围

‼️Python将一个变量名被赋值的位置关联到一个命名空间上。一个命名空间里不能有2个同名变量。

每一个函数默认都有自己的命名空间，我们的交互式终端也有一个默认的命名空间被称为__main__
在每一个模块中定义的变量被称为全局变量，具有全局作用域。
Python存在一个内置作用域，这个内置作用域也是一个模块，叫做builtins


LEGB规则：
L（Local）表示局部作用域
E（Enclosing）表示L外层的作用域: 函数套函数/函数套lambda，比如
def f():
	i = 1        # 这个i对于f来说就是局部作用域
	def g():
		print(i) # 这个i对于g来说就是外层作用域

G（Global）表示全局作用域
B（Built-in）表示内置作用域：在模块中预先定义好的一些名称，比如open，len等等

global：在一个函数内部去访问外部作用域中的变量，global后面可以跟上多个变量，彼此之间用逗号分开。
我们可以将global关键字后的变量理解为它们被映射到了整个模块作用域中
‼️判断一个代码是否会出错，就是看一个命名空间中是否绑定了两个同名的变量
例子1:

```python
X = 38

def func():
    X = 99      # 此处创建了局部变量X是99，覆盖了外部的变量X，也可以说X被绑定在函数func里面
    print(X)
    global X    # 此处会报错，因为global是想说从外部引进X，但是这个func中已经存在局部X
    X = 88

func()
print(X)

```

nonlocal语句
global实际上是全局作用域（模块作用域），但是nonlocal一般只用于外层嵌套作用域.
global可以在模块和函数作用域中书写，但是nonlocal只能在函数作用域中书写。
global后面的变量是可以暂时未定义的，但是nonlocal后面的变量必须在外层作用域中已经存在。

```python
def outer(x):
    out = x
    def inner(y):
        nonlocal out     # nonlocal表示我要用一个外层作用域的变量
        print(out)       # 此处体现了nonlocal可以访问外层作用域中的变量out
        out = y          # 此处体现了nonlocal可以修改外层作用域中的变量out
        print(out)
    return inner

f = outer(1)
f(2)
```

## 闭包

闭包三要素-必须同时满足，缺一不可
	函数被嵌套定义，一个def中包含另一个def
	内部函数引用了外部函数的局部变量/参数。
	外部函数返回了内部函数，且该内部函数在其作用域外被使用。

```python
def make_adder(base):
    def add(x):
        return base + x
    return add

add10 = make_adder(10)
print(add10(3))  # 此处闭包完整了，闭的是base这个10

print(add10.__closure__[0].cell_contents)  # 10（捕获的自由变量）

```

只要闭包对象（返回的内部函数）仍被引用，外层环境就不会被回收。
小心创建大量闭包或捕获大对象导致内存驻留。

```python
def make_adder(base):
    large = Large() # 假设Large是一个非常大的类
    def add(x):
        return base + x
    return add

```

##  lambda

lambda是一个有返回结果的匿名函数的简化写法

```python
# 语法

变量 = lambda 形参列表: 单个表达式
```

形式参数就是函数中的形式参数，表达式一定会产生一个结果。

```python
add = lambda a, b: a + b
# 等价于：
def add_def(a, b):
    return a + b
# 使用lambda和使用函数是没有区别的    
sum = add(1, 2)
print(add(1, 2))
```

只有单个表达式（不能写语句、赋值、return）。立即返回表达式的值。
不支持多语句、类型注解、复杂控制流；复杂逻辑请改用 def。
不要滥用：过多嵌套 lambda 降低可读性。

## 可迭代对象和迭代器

可迭代对象Iterable：	返回迭代器，只需要实现__iter__()方法
迭代器Iterator：		一次性、惰性地产出元素的对象，要求实现了__iter__()和__next()__方法


迭代器Iterator：		一次性、惰性地产出元素的对象，要求实现了__iter__()和__next()__方法

‼️比如：
list就是一个可迭代对象（因为它里面有一个__iter__方法）。
之所以要实现__iter__方法，是因为我们可以通过iter(可迭代对象)来获得一个迭代器。

```python
l = [12,3]
it = iter(l)  # 自动调用__iter__方法，得到一个迭代器存入变量it.
```

为什么用迭代器？
节省内存：不一次性加载所有数据，假设一个列表很长，我们可以next一个一个取出来。
惰性计算：按需生成数据。
通用性强：任何可迭代对象都可以用for循环遍历。

```python
class CountDown:  # 相当于系统中的<class 'list_iterator'>
	"""自定义迭代器"""
    def __init__(self, start):
        self.current = start
    def __iter__(self):
        return self             # 迭代器需返回自身
    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        self.current -= 1
        return self.current + 1

class CountDownIterable:   # 相当于list，也是一个迭代器对象
	"""自定义可迭代对象：每次调用iter()时返回一个新的CountDown迭代器"""
    def __init__(self, start):
        self.start = start

    def __iter__(self):
        return CountDown(self.start)

# 用户自己用的时候，用可迭代对象就可以了

c = CountDownIterable(3)
it1 = iter(c)
it2 = iter(c)
print(next(it1), next(it1),next(it2))
```

与系统中自带的进行比较
```python
l = ['aa', 'bb', 'cc']  # list就是可迭代对象
it1 = iter(l)  # 在l上获得一个迭代器
print(next(it1), n))
it2 = iter(l)  # 在l上获得另一个迭代器
print(next(it2))

it3 = iter(it1)  # 因为迭代器的__iter__应该返回其自身，所以it3就是it1
print(next(it3))  # 结果是cc

# ‼️可迭代对象每次iter都会得到新的迭代器——为了迭代器彼此之间互不干扰

# ‼️迭代器的iter应该返回其自身——为了迭代的连续性


生成器——Generator简化迭代器编写
def countdown(n):
    while n > 0:
        yield n    	# yield是生成器的典型标志，表示生成了一个内容
        n -= 1
        
c = countdown(3)   	# 有了生成器语法，就可以省略__iter__和__next__
it = iter(c)  		# iter其实是多余的
print(next(it))
print(next(it))
print(next(it))
```

yield form语句
当你在一个生成器里需要“嵌套调用”另一个生成器时，用普通的 for 写法很繁琐：

```python
def gen_outer():
    yield 1
    for x in gen_inner():  # 手动遍历子生成器
        yield x
    yield 5

def gen_inner():
    yield 2
    yield 3
    yield 4

print(list(gen_outer()))

# 输出: [1, 2, 3, 4, 5]
```

简化写法：yield from

```python
yield from <iterable> 等价于：
	•迭代子生成器（或任意可迭代对象）
	•自动转发 send()、throw()、close() 调用
	•自动返回子生成器的返回值（Python 3.3+）
```

```python
def gen_outer():
    yield 1
    yield from gen_inner()  # 自动迭代 gen_inner()
    yield 5

def gen_inner():
    yield 2
    yield 3
    yield 4

print(list(gen_outer()))

# 输出: [1, 2, 3, 4, 5]

yield from 会捕获子生成器 return 的返回值（这里是 'done'）；
生成器之间的调用链被简化，不再需要手动循环。


send() —— 向生成器发送数据
生成器不仅可以 yield 数据出去，还可以通过 .send(value) 接收外部传入的数据。
def greeter():
    name = yield "What's your name?"
    yield f"Hello, {name}!"

g = greeter()
print(next(g))          # 启动生成器，运行到第一个 yield
print(g.send("Peter"))  # 向生成器发送值 “Peter”
```

第一次 next() 启动生成器；
yield 暂停并返回 "What's your name?"；
g.send("Peter") 发送值进入生成器，被赋给变量 name；
程序继续执行下一行，产出 "Hello, Peter!"。



```python

l = ['aaa','bbb','ccc']     # l是可迭代对象
it1 = iter(l)   # 利用iter方法从可迭代对象l上获得迭代器it1
it2 = iter(l)   # 利用iter方法从可迭代对象l上又获得了一个迭代器it2

print(next(it1))
print(next(it1))
print(next(it2))
print(next(it1))
```

```python
class CountDownIterable:  # 可迭代对象
    """可迭代对象只要求实现iter，返回一个新创建的迭代器"""
    def __init__(self, start):
        self.start = start

    def __iter__(self):  # 可迭代对象的iter方法应该返回一个全新的迭代器
        return CountDown(self.start)

class CountDown:
    """迭代器要求实现iter和next，next返回迭代器的下一个元素，iter返回自身"""
    def __init__(self, start):
        self.current = start
    def __iter__(self):  
        return self
    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        self.current -= 1
        return self.current + 1

c = CountDownIterable(5)
it1 = iter(c)
it2 = iter(c)
print(next(it1))
print(next(it2))
```

## 异常处理

try: 异常捕获
except: 异常处理
else: try里面没有异常的时候才会执行
finally: 不管是否出现异常都会执行
raise：主动抛出异常

异常的体系结构：
BaseException是所有异常的父类
	  |- SystemExit
	  |- KeyboardInterrupt
      |- ......
      |- Exception常规或者说用户使用的所有异常的父类
             |- 
自定义异常：原则上来说继承任何一个异常类即可，通常这个父类应该Exception。
class MyException(Exception):
    ...
为什么要自定义异常？因为系统中自带的异常种类不够丰富，不能满足项目实际需求。



## assert

assert: 断言
断言一般用在程序调试中，正常代码中很少出现
例子：
assert 1>2
一旦断言条件为False，就会出现AssertError这个异常





## 文件

变量 = open('文件名', '模式')
w：只能对文件写，如果文件不存在，则建立新文件；如果文件存在‼️则内容全部覆盖
r：只能对文件读, 如果文件不存在，那么在open的时候就会直接报错
a：a是append表示追加（不能读），如果不存在则建立新文件；如果文件存在，它就在文件结尾进行追加

⚠️w，r和a还可以被写成wt，rt和at，他们的含义完全相同

r+：读写，即能读也可以写，但是要求文件必须存在否则报错，r+实际上还拥有追加的特点。

seek() 跳到文件的第几个字节处

close()

with：上下文管理器

```python

with open('hello.txt') as f:
 	...
等价于
f = open('hello.txt')
...
f.close()
```

with后面的对象必须提供__enter__和__exit__两个魔术方法。

```python
class Cat():
    def __enter__(self):
        print('进入')
    def __exit__(self, exc_type, exc_val, exc_tb):
        print('结束')

with Cat() as c:
    print('执行')
```



模块
Python的模块以.py结尾
执行Python的模块，只需要 python xxx.py

语法1:

```python
import 模块名      # 当使用import导入模块的时候，会将这个模块完整的执行一次。
import fractions
```

语法2:

```python
from 模块名 import 函数/类等等
from fractions import Fraction
```





















# Python中的Pathlib模块

在Python中，pathlib模块提供了一种面向对象的方式来处理文件系统路径。它允许开发者以一种简洁且一致的方式来编写代码，无论是在Windows还是Unix系统上。pathlib模块中的Path类是最常用的类，它封装了大多数与路径相关的操作。

创建和使用Path对象

要使用pathlib模块，首先需要从模块中导入Path类：

```python
from pathlib import Path
然后，可以创建一个Path对象来代表一个文件或目录的路径：

# 创建一个Path对象

p = Path('D:/python/1.py')
print(p)

# 使用斜杠操作符来连接路径

p1 = Path('D:/python')
p2 = p1 / '123'
print(p2)
Path对象可以用来执行各种文件系统操作，例如获取当前路径、检查路径是否存在、列出目录内容等。
```



常用方法和属性

`Path.cwd()`：获取当前工作目录的路径。

`Path.exists()`：检查路径是否存在。

`Path.glob(pattern)`：返回一个生成器，包含所有匹配给定模式的文件路径。

`Path.is_dir()`：检查路径是否为目录。

`Path.is_file()`：检查路径是否为文件。

`Path.iterdir()`：当路径为目录时，生成包含目录下所有文件和子目录路径的迭代器。

`Path.mkdir(parents=False)`：根据路径创建新目录，parents=True时会递归创建缺失的中间目录。exist_ok：只有在目录不存在时创建目录，目录已存在时不会抛出异常。

`Path.open(mode='r')`：类似于内置的open()函数，用于打开文件。

`Path.rename(target)`：重命名或移动文件或目录。

`Path.rmdir()`：删除空目录。

`Path.name`：获取路径的文件名。

`Path.suffix`：获取路径的文件扩展名。





# 面向对象部分



## 类和对象

```python
class Cat:
    type = '猫科'  # 类属性/类变量

    def __init__(self, name):
        self.name = name  # 属性
    
    def __repr__(self):
        return 'repr'
    
    def __str__(self):
        return 'str'
    
    def eat(self):  # 方法，实例方法
        print(f'{self.name}正在吃饭')
        print(Cat.type)  # 实例方法或者方法可以使用属性或者是类变量/调用类方法
    
    @classmethod
    def get_type(cls):  # 类方法只可以访问类变量和调用其它类方法
        return cls.type
    
    @staticmethod
    def f():
        print('这是一个静态方法，实际上和类没什么关系')
        # 我们用静态方法无非是为了满足面向对象的要求，给它找个类放进去而已


# 每个对象所共有的，不变的应该是类属性

# 每个对象都可能不一样的就是属性

# 如果需要操纵类属性就提供类方法

# 操纵属性就提供方法

c = Cat('Tom')
print(c)
print(repr(c))
print(str(c))
```

## 访问权限

Python中其实没什么权限，它实际上是不太建议或者主张限制权限的。

1. 公有——默认
2. 私有——只能这个类里面自己用
3. 继承——只有父子关系才能使用















