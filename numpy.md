

# numpy

numpy是python库里的一个，可以用于数据处理，常用`import numpy as np`来进行调用

优点：连续存储，可以连续加载多个浮点数到寄存器，从而利用CPU的多核，并行计算

NumPy学习起点：ndarray(N-dimensional array object)，多维数组 和 ufunc（universal function object）对数组进行处理的函数



## 创建array(序列)

创建`ndarray`时，当用到`np.array()`方法进行创建时，可以传入`order=`参数，此时的值为`"C","F","K","A"`

```python
"""    
		'K'   unchanged F & C order preserved, otherwise most similar order
        'A'   unchanged F order if input is F and not C, otherwise C order
        'C'   C order   C order
        'F'   F order   F order
"""
```

```python
import numpy as np
# 创建一维数组的几种方式：
np.arange(10)  		# array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
np.arange(0, 10, 2)		# array([0, 2, 4, 6, 8])
np.linspace(0, 10, 15)      # 结果是在[0, 10]之间划分出15个等值数列
np.array(list)  # 将python中的列表转换成numpy中的ndarray
# 随机形式的数组创建
Np.random.randint(start:int,stop:int,size=(shape)) #生成shape形状的数组，随机数中包含start,但不包含stop
Np.random.randn(shape) # 生成shape形状的数组，数组数据符合正态分布
Np.random.standard_normal(size=(shape)) # 同样是生成符合正太分布数组
rng=np.random.default_rng(seed=12345)
arr1=rng.integers(10,99,size=(2,3))   # 用于创建随机数生成器的推荐方法。通过指定种子，可以确保生成的随机数序列在不同会话中保持一致
# 二维数组创建
np.array([[1, 2, 3], [4, 5, 6]])
# 三维数组创建
np.array([[[1, 2, 3],[4, 5, 6]], [[7, 8, 9],[10, 11, 12]]])   # 另外
# 坐标轴axis的概念: 1维数组axis就是0，2维数组axis最多就是0，1。  3维数组axis最多就是0，1，2


# 其他创建数组的函数，zeros，ones，empty
# zeros: 创建全是0的ndarray； ones：创建全是1的ndarray
# empty：创建没有初始值的ndarray
# 这些函数只需要传入 表示shape的tuple
print(np.zeros(10))
print(np.zeors((3,6))
print(np.empty((2,3,2))
# empty和0值不完全一样，未初始化的值可能包括内存里的垃圾值，给新数组填充数据时，才使用此函数

# full:根据给定数组的形状，和数据n，创建一个全n数组
# ones_like：根据给定数组的形状和数据类型创建一个全1数组
# zeros_like：根据给定数组的形状和数据类型创建一个全0数组
# empty_like： 根据给定数组的形状和数据类型创建一个无填充值的数组

```



### ndarray的数据类型

dtype是将ndarray内存储的数据解释为特定数据类型，属于元数据(metadata)（关于数据的数据）

数据类型是NumPy能与其他系统灵活交互数据的来源，直接映射内存/硬盘。int32：32个bits存储的整数（4字节）；float64：64个bits存储的浮点数（8字节）。

NumPy的常用数据类型：

| 类型         | 类型代码 | 说明                                                         |
| ------------ | -------- | ------------------------------------------------------------ |
| int8, uint8  | i1, u1   | 有符号和无符号的8位（1个字节）整型                           |
| int16,uint16 | i2, u2   | 有符号和无符号的16位（2个字节）整型                          |
| int32,uint32 | i4, u4   | 有符号和无符号的32位（4个字节）整型                          |
| int64,uint64 | i8, u8   | 有符号和无符号的64位（8个字节）整型                          |
| float16      | f2       | 半精度浮点数                                                 |
| float32      | f4或f    | 标准的单精度浮点数。与C的float兼容                           |
| float64      | f8或d    | 标准的双精度浮点数。与C的double和Python的float对象兼容       |
| bool         | ?        | 存储True和False值的布尔类型                                  |
| object       | 0        | 任意python对象                                               |
| bytes_       | S        | 固定长度的ASCII字符串类型（每个字符1个字节）。例如，要创建一个长度为10的字符串，应使用'S10' |



类型转换：通过ndarray的astype方法可以将数组从一种数据类型转换或投射成另一种数据类型：

```python
# 整数数组 -> 浮点数数组
arr = np.array([1,2,3,4,5])
print(arr.dtype)

float_arr = arr.astype(np.float64)
print(float_arr)
print(float_arr.dtype)

# 浮点数数组 -> 整数数组
arr = np.array([3.7,-1.2,-2.6,0.5,12.9,10.1])
print(arr)
print(arr.astype(np.int32))

# 字符串数组 -> 浮点数数组
numeric_strings = np.array(["1.25","-9.6","42"], dtype=np.bytes_)
print(numeric_strings.astype(float))

# 一个数组的类型 -> 另一个数组的类型
int_array = np.arange(10)
float_array = np.array([.22, .27, .357, .38, .44, .5], dtype=np.float64)
print(int_array.astype(float_array.dtype))

# 即使新的数据类型与旧的数据类型相同，调用astype也总会创建一个新的数组

```

## 重塑数组

### `reshape`

既是np级别的方法也是具体数组的方法

```python
# Np.reshape(arr(应为具体数组名),size=(具体形状，用元组表示)，order=’’)
# order见下文
Arr.reshape(size=(具体形状，用元组表示),order=’’)
# 传入的形状维度中的一个值可以是-1，它表示该维度的大小由数据本身推断而来
例如：
arr=np.arange(12).reshape(6,-1)
arr
# array([[ 0,  1],
#        [ 2,  3],
#        [ 4,  5],
#        [ 6,  7],
#        [ 8,  9],
#        [10, 11]])

```



reshape中size需要传递元组，而具体数组的shape属性就是元组，也就意味着别的数组的shape属性可以传递给别的数组

reshape方法是视图，所以并不会改变原先的`ndarra`

==reshape方法严格要求其数据的个数，即原`ndarray.size`必须与传入元组各个数字的累乘结果相一致==

### resize

resize函数与reshape函数相类似，但生成的数组和原始数组不共享内存空间

==与之不同的是，`resize`函数并不需要其传入的元组的累乘与`ndarray.size`完全一样，当乘积小于`ndarray.size`时，会将`ndarray`摊成一个一维数组，然后取其中的与乘积相当个数的元素(按顺序)进行重塑，但是当其乘积大于`ndarray.size`时会分为两种情况：==

1. ==在`np`级别下的`resize`会将摊开的数组进行`.tile`处理，此时的数组整体进行**复制**，复制体在原本的`ndarray`后面，此时再将对应数量的数据取出，进行重塑==
1. ==在示例数据的`.resize`方法并不会再后面进行`.tlie`方法进行复制，取而代之的是**进行零项填充**，，再取出对应数量的数据进行重塑==

```python
a = np.arange(12)
b = np.resize(a, (3, 4))
c = np.reshape(a, (3,4))
c2 = a.reshape((3,4))
# resize生成的数组和原始数组不共享内存空间, reshape生成的数组是视图
print(np.shares_memory(a,b))
print(np.shares_memory(a,c))
print(np.shares_memory(a, c2))

# resize不支持order属性，reshape支持
d = np.reshape(c, (3, 4), order='F')

# resize前后的元素数量不一致不会出异常：resize前元素数量少，现有元素会依次重复使用，resize前元素数量多会抛弃
np.resize(a, (3,3))
np.resize(a, (3,6))

```

数组变量.resize()这个调用中包含一个参数refcheck，这个参数的默认值是True

当默认值是True的时候对于数组元素数量不一致的变换会抛出异常，如果为False行为和np.resize是一致的

a = np.arange(12)

b = a 

b.resize((1, 2)) #此处得到下面的错误，因为我们是对a的引用b进行元素数量不一致的resize，所以导致错误出现

\---------------------------------------------------------------------------

ValueError                Traceback (most recent call last)

Cell In[89], line 1

----> 1 b.resize((1, 2))

 

ValueError: cannot resize an array that references or is referenced

by another array in this way.

Use the np.resize function or refcheck=False

 

但是此时如果加上refcheck=False，则对b进行resize不再报错

a = np.arange(12)

b = a 

b.resize((1, 2), refcheck=False)

a # 结果是array([[0, 1]])

b # 结果是array([[0, 1]])

 

总结：

np.reshape是产生了一个新的视图，np.resize产生了一个新的拷贝/副本

数组变量.resize 在对数组变量本身做修改

np.reshape元素数量不一致会异常，np.resize不会产生异常

一般情况下，"np.reshape/resize"以及"数组.reshape/resize"没区别。

但是"数组.resize"在元素数量不一致且存在一个引用的时候可能会异常。



### 数组扁平化

#### `flatten`方法与`ravel`方法

flatten：假设有数组b是

array([[[0, 1],

​    [2, 3],

​    [4, 5]]])

c = b.flatten() #c的结果是array([0, 1, 2, 3, 4, 5])，b保持不变

 

 ravel效率相对于flatten高

a = np.arange(10).reshape(2, 5)

a.ravel(order='C') # 在最低维度的坐标轴方向上对元素进行读取，拉平

a.ravel(order='F') # 与 C相反，在最高维度的坐标轴方向上对元素进行读取，拉平：array([0, 5, 1, 6, 2, 7, 3, 8, 4, 9])

a.ravel(order='A') # FORTRAN语言的结果存放数组时，A会以Fortran的结果显示，否则与C没有区别

c = np.array([[1, 2, 3], [4, 5, 6]], order='F')

c.ravel(order='A') # FORTRAN语言的结果存放数组, A以Fortran的结果显示

 

a.ravel(order='K') # 以内存中数组存放的顺序进行拉平

a.T.ravel(order='K')

ravel产生的是视图, 但如果展开的元素之间内存不连续，ravel可能生成拷贝；flatten产生的是拷贝





## `ndarray`属性

ndarray的常见属性

1. T属性，让矩阵转置——但是原来的矩阵不会发生变化，当n小于2的时候返回原数组
2. data属性：表示数组中的数据从哪里开始的一个缓冲区对象，这里的结果就是一个内存地址
3. dtype属性：ndarray中元素的具体类型，比如int64（这个结果随计算机不同而不同）
4. flags属性：数组在内存中的相关信息

​	C_CONTIGUOUS : True   以行优先row-major的方式存放数组——坐标轴的编号从小到大进行存放

​	F_CONTIGUOUS : False  以列优先column-major的方式存放数组

假设有一个二维数组

a11 a12 a13

a21 a22 a23

当C_CONTIGUOUS : True的时候，在内存中线性存放的结果是a11 a12 a13 a21 a22 a23

当F_CONTIGUOUS : True的时候，在内存中线性存放的结果是a11 a21 a12 a22 a13 a23

 

二维数组中，列方向使用axis=1来表示，行方向以axis=0表示

c = np.array([[1, 2, 3], [4, 5, 6]], order='F')

5. size属性：数组中元素的数量，是各个维度的乘积
6. itemsize属性：数组中每一个元素所占用的字节数量，比如int64的话结果是8。
7. nbytes属性：数组中所有元素占用的总字节数，这个值一般是size*itemsize
8. strides属性：元素在各个维度上移动所需要跨越的字节数

 通常与数组中的itemsize有关，也与数组的维度有关，

对于任意数组，不妨设为(n,m,l,k)的四维数组，数组个数就是数组的shape属性中元组元素的个数，对于任意数组.shape可以看数组的形状，.ndim可以看数组的维度，

回到strides属性上，会返回一个与.shape同样元素个数的元组，元组最后一位时.itemsize的大小，倒数第二个为k* .itemsize的大小，倒数第三个为l*k*.itemsize的大小，这是基于order=‘C’而言，反之则倒着来形成一个(.itemsize , n*.itemsize,m*n*.itesize,l*m*n*.itemize)的元组

##  `ndarray`计算

数组与数组之间的加减乘除要符合以下规律：

1.同型的数组之间要按照对应元素进行加减乘除

\# 大小相同的数组之间的比较会生成布尔值数组

例如：

```python
Arr1 = np.random.randn(4,5)
Arr2 = np.random.standard_normal((4,5))
Arr1 > Arr2 
# array([[ True, False,  True,  True, False],
#        [ True, False,  True, False, False],
#        [ True, False,  True,  True,  True],
#        [ True,  True, False, False, False]])


# 不同大小的数组之间的运算称为广播
#比如 
Arr2 <1
# array([[ True,  True,  True,  True,  True],
#        [ True,  True,  True,  True,  True],
#        [ True, False,  True,  True,  True],
#        [ True,  True, False,  True,  True]])

```



## 视图与副本

副本与视图最大的区别在于：

视图与原数据是共享存储地址的，而副本与原数据是不共享内存地址的，也就意味着党对视图进行除reshape操作时，其对数据的更改会体现在原本的`ndarray`上，而副本则不会，用视图会更加节省内存空间

```python
a = np.array([1, 2, 3])
d = a.view() # 创建a的一个视图，我们可以把view理解为python中的引用，即d = a (但id(a) != id(d), 而是id(a) == id(d.base))

# np.array, np.zeros, np.xxx操作产生的基本上是副本
Arr.copy()  # 产生的也是一个副本

# 对象不是视图，ndarray的base属性一定是None
Print(arr.base)

# 视图可以节省内存空间
# 在ndarray的四则运算中可能形成副本也可能形成视图
Arr=arr+1
Arr=arr-1
Arr=arr*6
Arr=arr/5
# 上述操作均会产生副本
Arr +=1
Arr -=1
Arr *=6
Arr /=5
# 上述操作不会产生副本，arr均以视图形式出现，可以有效节省内存空间

```

通过改变视图的元素，对于数组本身也会改变，但是如果改变的时副本的元素，对于原数组毫无影响，

视图和副本可以通过np.may_share_memory(arr1,arr2)和np.shares_memory(arr1,arr2)来进行判断

```python
# may_share_memory如果结果为False，可以说明a和b的内存地址一定不相同，我们可以说它们之间一定不是视图关系
# may_share_memory如果结果为True，我们可以说a和b“大概率”上是内存共享的，即它们很有可能是视图关系
# shares比上面多了s，这个方法得到的结果是一定肯定的。
# 但是它的运算速度比may_share_memory慢
# 不推荐用base：base返回的是前一个视图对象，和原始数组的切片不。
```



## 切片与索引

### 普通索引与切片

`ndarray`的切片是原始数组的视图，数据不会被复制，视图上的任何修改都会直接反映到源数组上。

```python
arr = np.arange(10)
arr_slice = arr[5:8]
arr_slice[1] = 12345
print(arr)
arr_slice[:] = 64
print(arr)
# 如何得到切片的副本：arr[5:8].copy()

# 可以通过传入一个逗号隔开索引列表，来选取
Arr[2,3]
# 多维数组中，如果省略了后面的索引，则返回对象会是低维度的ndarray，它包含更高维度上的数据


arr3d = np.array([[[1,2,3],[4,5,6]],[[7,8,9],[10,11,12]]])
print(arr3d)
print(arr3d[0], arr3d.shape)   # [[1 2 3]
 							# [4 5 6]] (2, 2, 3)
arr = np.random.randint(1,15,size=(6,5,4,3))
arr[:,(2,3),(2)]  # .shape 是 (6, 2, 3) 
# 标量值和数组都可以赋值给arr3d[0]
old_values = arr3d[0].copy()
arr3d[0] = 42
print(arr3d)
arr3d[0] = old_values
print(arr3d)
# arr3d[1,0] 也可以访问轴0 索引为1， 轴1 索引为0的那些值
print(arr3d[1,0])
# 等价于 x = arr3d[1], x[0]

```

同时也可以传入多个切片，就像传入多个索引一样

对切片表达式的赋值操作也会被扩散到整个选区

### Bool型索引

布尔型索引是将原索引通过bool形式的ndarray进行提炼，生成新的ndarray

最大的用途就是进行数据筛选，假设arr 是一个m*n的二维数组，此时要找arr中所有大于4小于8的元素就可以用bool型索引进行操作具体如下

```python
arr = np.random.randint(1,9,size=(5,5))
print(arr)
res =  np.where((4<arr) & (arr<8))
arr[res]	#[[8 8 7 4 6]
			# [1 2 5 3 5]
			# [2 7 8 8 3]
			# [7 2 8 2 6]
			# [4 7 8 2 7]]
			#[7 6 5 5 7 7 6 7 7]

```

另外bool型索引还可以用于同型的ndarray进行交互，具体如下：

```python
names = np.array(["Bob", "Joe", "Will", "Bob", "Will", "Joe", "Joe"])
data = np.array([[4,7],[0,2],[-5,6],[0,0],[1,2],[-12,-4],[3,4]])
print(names)
print(data)
# 假设每个名字都对应data数组中的一行，而我们想要选出对应于名字"Bob"的所有行
# 比较运算（==）也是向量化的，对names和”Bob"的比较运算将会产生一个布尔型数组
print(names == "Bob")
# 布尔数组可用于数组索引
print(data[names == "Bob"])
# 注意：布尔型数组的长度必须和被索引的轴的长度一致。
a_test = np.array([10, 20, 30, 40, 50])
# 错误：布尔索引数组长度为 3，而 a 的长度是 5
mask_test = np.array([True, False, True])  # 长度不一致
print(a_test[mask_test])  # 将报错
# 还可以将布尔型数组跟切片，整数混合使用
print(data[names == "Bob", 1:])

# 要选取除”Bob“之外的数据，可以用 != 或 ~ 对条件求反
print(names != "Bob")
print(~(names == "Bob"))
print(data[~(names == "Bob")])

cond = (names == "Bob")
print(data[~cond])

# 组合多个布尔条件，需要使用布尔算术符&（与） 和 | （或）
mask = (names == "Bob") | (names == "Will")
print(mask)
print(data[mask])
# 注意：通过布尔型索引选取数组中的数据，是创建了新的数据副本（和索引切片不一样），即使返回了一模一样的数组也是如此。
# 注意：and 和 or不用于布尔型数组，要用 &（和） |（或）

# 布尔型数组设置值，相当于 用等号右边的值 替换 布尔数组中为True的值
# 将data中的所有的负值都设置为0：
data[data < 0] = 0
print(data)
# 通过一维数组设置整行 或 整列的值：
data[names != "Joe"] = 7
print(data)

# 注意布尔索引会降维：
A = np.array([[1, 2], [3, 4]])
mask = np.array([[True, False], [False, True]])

print(A[mask])  # [1 4]

```

### 花式索引

```python
arr = np.zeros((8,4))
arr[:] = np.arange(1,9).reshape((8,1))
print(arr)
# 以特定顺序选取行子集，只需传入用于指定顺序的整数列表或ndarray
print(arr[[4,3,0,6]])
# 负数索引从某尾开始选取行
print(arr[[-3, -5, -7]])

# 复杂的花式索引 （配合连续索引）
print(arr[[1,5,7,2]][:,[0,3,1,2]])

# 对花式索引赋值，会修改被索引的值
arr[[1,5,7,2],[0,3,1,2]] = 0
print(arr)


```

## 数组转置

\# 通用的转置方法：轴对换。`swapaxes`方法，接收一对轴编号，兑换标记的轴以重排数据

`Swapaxes(arr,axis1,axis2)` `arr`是需要进行转置的数组，`axis1`和`axis2`是需要置换的轴（对于n维数组，其`axis` = 0,1,2.3,4……n-1）

\# `swapaxes`也是返回源数据的视图，不进行复制

 

`Arr.transpose() `括号内传入axis的顺序，而且必须将轴全部传入，此方法也是返回源数据的视图，不进行复制

对于二维矩阵，`A.transpose()`不传入参数是默认对矩阵进行转置，同时A.T也是求矩阵转置





## 随机数与伪随机数

`Numpy`有内置的random函数可以调用

```python
# 随机数，np.random类提供了和随机数相关的函数
Np.random.randn(m,n) 返回一个m*n的符合标准正态分布的数组
Np.random.rand() 返回一个【0，1）均匀分布的数组
np.random.standard_normal(size=(m,n)) 生成一个m*n的数组，数组内的数据符合正态分布
np.random.uniform(low,high) 生成一个均匀分布的数组，区间在【low,high】
np.random.randint(5, 10, size=(2, 3))  生成 【5，10）的整数数组，数组形状为2*3
# 常见公式：生成x～y之间的随机数 (y - x) * np.random.rand(y) + x

# 种子：相同的种子可以生成相同的随机数，我们看似没有种子的时候默认生成的随机数一般都是以系统时钟等为种子计算出来的。
np.random.seed(33)
np.random.rand()       
np.random.seed(33)
np.random.rand()    # 会得到相同的两次随机数
# 我们之所以指定种子，多半是要进行测试操作。也就是说我们生成随机序列，但是需要反复生成相同的随机序列来观察结果。
另外一种伪随机：
rng=np.random.default_rng(seed=12345)
同理可以调用randn，uniform，integers，standard_normal等方法

languages = ['C', 'C#', 'Python', 'C++', 'Java', 'SQL']
np.random.choice(languages, 3)
np.random.choice(languages, 30, p=[0.7, 0.1, 0.1, 0.05, 0.02, 0.03])    # p中元素数量要和列表长度相等，元素加起来和必须是1

a = np.arange(10)
np.random.shuffle(a)   # 混洗

```

## 通用函数

对ndarray中的数据执行元素级运算的函数，可以看作简单函数的快速向量化。接收一个或多个标量值，并生成一个或多个标量值。

一元通用函数：

`Abs`(取绝对值)，`fabs`(浮点数的绝对值）, `sqrt`(求平方根), `square`(求平方), `exp`(求e的幂), `log`(求以e为底的对数), `log10`(求以10为底的对数), `log2`(求以2为底的对数), `sign`（计算各元素的符号，1，0，-1）； `ceil`(向上取整)，`floor`(向下取整)，`rint`（四舍五入到最接近的整数，保留`dtype`），`modf`(将数组的小数部分和整数部分以两个独立数组的形式返回）。三角函数相关：`cos`，`cosh`，`sin`，`sinh`，`tan`，`tanh`，`arccos`，`arccosh`，`arcsin`，`arcsinh`，`arctan`，`arctanh`，`ptp`(计算轴向内封顶和峰谷的差值)

 

二元通用函数：

`Add`(两元素相加),`subtract`(两元素相减), `multiply`(乘),`divide`(除), `floor_divide`(相当于 // ，元素级别的求商的整数部分),`power`(元素级别的求幂，`arr1`，`arr2`，`arr1`的`arr2`次幂), `maximum`(传入两个`ndarray`，同型或广播后同型，在同位置上取较大的元素生成新的`ndarray`), `minimum`(与`maximum`正好相反), `mod`(传入两个数组，支持广播，计算`arr1`%`arr2`), `copysign`(第二个数组中的元素值的符号复制给第一个数组中的元素），`modf`(将传入的数组分解成小数部分和整数部分两个数组进行返回，先小数部分后整数部分)。

比较相关：`greater`, `greater_equal`, `less`, `less_equal`, `equal`, `not equal`, 返回布尔数组

位运算相关：`logical_and`, `logical_or` `logical_xor`

 

\# `ufunc`可接收一个out可选参数，这样就能对存在的数组直接赋值，而不必新建数组

```python
# ufunc可接收一个out可选参数，这样就能对存在的数组直接赋值，而不必新建数组
out = np.zeros_like(arr)
np.add(arr,1)
np.add(arr, 1, out=out)
print(out)

```

## 向量化与np.where

用数组表达式代替循环的做法叫向量化。向量化数组运算要比等价的纯Python快非常多

```python
# 在一组网格数据上计算函数 sqrt(x^2+y^2)
# numpy.meshgrid: 接收两个一维数组，并产生两个二维矩阵，对应于两个数组中所有的(x,y)对
import numpy as np
import time

# 创建点坐标
points = np.arange(-5, 5, 0.01)
xs, ys = np.meshgrid(points, points)

### 向量化实现 ###
start_vec = time.time()
z_vec = np.sqrt(xs**2 + ys**2)
end_vec = time.time()
print(f"向量化执行时间: {end_vec - start_vec:.4f} 秒")

### for 循环实现 ###
start_loop = time.time()
z_loop = np.zeros_like(xs)
for i in range(xs.shape[0]):
    for j in range(xs.shape[1]):
        z_loop[i, j] = np.sqrt(xs[i, j]**2 + ys[i, j]**2)
end_loop = time.time()
print(f"for 循环执行时间: {end_loop - start_loop:.4f} 秒")
# 可以看出for循环明显慢了很多

# 条件逻辑向量化
# np.where: 三元表达式x if condition else y的向量化版本
xarr = np.array([1.1,1.2,1.3,1.4,1.5])
yarr = np.array([2.1,2.2,2.3,2.4,2.5])
cond = np.array([True, False, True, True, False])
# 根据cond中的值选取xarr和yarr的值，当cond中的值为True时，选取xarr的值，否则从yarr中选取
# 列表推导式：
result= [(x if c else y) for x,y,c in zip(xarr, yarr, cond)]
# 问题：大数组的处理速度慢；无法应用到其他形状的数组
# 使用numpy.where
result = np.where(cond, xarr, yarr)

# where后的两个参数可以是标量
# 例子：随机生成的矩阵，希望所有正值替换为1，负值替换为-1
arr = rng.standard_normal((4,4))
print(arr)
print(arr>0)
print(np.where(arr>0,1,-1))

print(np.where(arr>0,1,arr)) # 仅将正值设为1

# np.where可以只有一个参数，np.where(condition) 的行为是：
# 对于一个布尔条件，它返回 满足条件的元素的下标（索引），每个维度一个数组。
arr = np.array([[1, 6, 3],
                [7, 2, 8],
                [4, 9, 0]])

```



## any/all

```python
# all和any运算操作
a = np.array([
       [1, 1, 1],
       [1, 0, 0],
       [1, 0, 1]
])
np.all(a)  # all要求整个数组全都 不是0，None，空等等结果才是True，否则结果是False 
a = np.ones((3, 3))   # 生成全1的数组
np.all(a)  # 只有所有元素都非0的时候，结果才是True
np.all(a<2)   # 条件判断，如果数组中所有元素小于2，结果为True
np.all(a==2)  # 条件判断，如果数组中所有元素都是2，结果为True
np.all(a=2)   # 赋值，a就成了2，结果是True
np.all(a=0)   # 赋值，a就成了0，结果是False
np.all(a, axis=0)  # array([ True, False, False])
np.all(a, axis=1)  # array([ True, False, False])
和之前变形的例子类似，all也可以通过数组直接进行调用，比如
a.all()
a.all(axis=1)
(a<2).all()     # 注意条件的写法，不写成a.all(a<2)


```

all要求所有元素都满足条件的时候才是True，any只需要任何一个元素满足条件即可
对布尔类型数组：any用于检查数组中是否存在一个或多个True，all检查数组中的所有值是否都是True

## `Numpy`中的统计

`NumPy`统计：关于计算整个数组统计值或关于轴向数据的数学函数。

聚合函数：`sum`，`mean`，`max`, `min`,` argmax`, `argmin`。既可以作为数组的实例方法调用，也可以当成的`numpy`模块内的函数。（`np.sum`)

`Sum`与`mean`是求和与求平均，`max`与`min`返回最大值最小值，`argmax`和`argmin`返回最大值与最小值的索引

这几种方法可以传递维度，即`axis=` ，此时要注意的是当传入维度是会将原本的数组进行聚合，需要传入的是聚合降低的维度

```python
# 现有一个ndarray，A  
# array([[0.1, 0.2, 0.3, 0.4],
#       [0.5, 0.6, 0.7, 0.8]])
np.max(A,axis=0) # 会在axis=0 的维度上进行聚合，从(2,4)变成(1,4)
# array([0.5, 0.6, 0.7, 0.8])


```

`cumsum`和`cumprod`不聚合，产生一个由中间结果组成的数组

`sumsun`是按顺序累加，`cumprod`是按顺序累乘

两者也可以传入维度，这样就会在传入的维度上进行累加(乘) 

```python
# 例如：
# A同上
np.cumprod(A,axis=0) # 会返回(2,4)的数组
# array([[0.1 , 0.2 , 0.3 , 0.4 ],
#       [0.05, 0.12, 0.21, 0.32]])

```

常用的统计方法：

| 方法            | 说明                                                |
| --------------- | --------------------------------------------------- |
| `sum`           | 对数组中全部或某轴向的元素求和，零长度的数组的和为0 |
| `mean`          | 算术平均数，零长度的数组的`mean`为`NaN`             |
| `std,var`       | 标准差和方差                                        |
| `min,max`       | 最小值和最大值                                      |
| `argmin,argmax` | 最小值元素的索引和最大值元素的索引                  |
| `cumsum`        | 从0开始的元素累计和                                 |
| `cumprod`       | 从0开始的元素累计积                                 |
| `median`        | 计算中位数                                          |

在统计方法中，布尔值会被强制转换为1（True）和0（False）。因此,sum常用于计算布尔型数组中True的个数

## 结构化数组
