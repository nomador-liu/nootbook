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





















