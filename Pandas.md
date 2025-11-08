# Pandas

包含多种数据结构和数据操作工具，使数据清洗和分析工作更快，更简单。pandas经常配合NumPy，数据可视化库matplotlib一起使用。pandas从NumPy那汲取了很多面向数组计算的语言风格（面向数组，向量化）
NumPy: 更多是全是数字的数组，适合数据结构是全类型相同的数组。
Pandas：更像带表头的Excel表格，可以处理字符串，数字，日期等各种类型混合的数据，易于数据清洗，分析和统计

Series(序列)和DataFrame(数据框架)是pandas最常用的数据结构

## Series(序列)

Series类似一维数组对象，它由一组（类似NumPy数据类型的）数据 以及一组与之相关的数据标签（索引）组成，仅由一个数组即可创建简单的Series

```python
Obj =pd.Series([1,2,3,4])
# 可以直接把数组传进去
# Series索引位于左边，值位于右边，由于没有为数据指定索引，所以自动创建一个
# 0到N-1的整数型索引

# 通过Series的array和index属性获取其数组值和索引对象
print(obj.array) # PandasArray对象，通常封装成一个NumPy数组
print(obj.index)

在创建Series时可以手动输入index属性值
# 创建索引为标签的Series，用标签作为索引获取各个数据
obj2 = pd.Series([4,7,-5,3], index=["d","b","a","c"])
print(obj2)
print(obj2.index) # Index(['d', 'b', 'a', 'c'], dtype='object')

# 通过标签索引Series中的单个值 或一组值
print(obj2["a"])
print(obj2["d"])
print(obj2[["c", "a", "d"]])
# ["c", "a", "d"]是索引列表, 包含的是字符串而不是数字
# 因为在series的索引是标签形式所以obj1[-1]会报错，KeyError
# 基于Series 同时索引多个值时，会返回一个series

# 使用NumPy函数或类似NumPy的运算，比如用布尔型数组进行过滤，标量乘法，应用数学函数，都会保留索引值的链接
Obj2>0  # 会把索引标签保留下来，里面的数据根据条件变成布尔型
print(obj2[obj2>0]) # 对应True的数据保留，False的会消失，返回的依然是一个Series
print(obj2 * 2) # series也会将数据向量化


# 可以把Series看作长度固定的有序字典，-> 索引的标签 和数据值的 对应
# 在可能使用字典的场景中，也可以使用Series:
print("b" in obj2)
print("e" in obj2)
# 放在一个python字典中，可以通过传入这个字典来创建Series
sdata = {"Ohio": 35000, "Texas": 71000, "Oregon": 16000, "Utah": 5000}
obj3 = pd.Series(sdata)
print(obj3)

obj.to_dict() # 会将Series变成字典

# 如果只传入一个字典，则产生的Series中的索引会遵循字典的键的顺序，
# 而键的顺序依据的是字典的keys方法，keys方法又取决于键的插入顺序
# 可以将字典键按照想要的顺序传给构造函数，从而指定生成的Series的索引顺序
states = ["California", "Ohio", "Oregon", "Texas"]
obj4 = pd.Series(sdata, index=states)
print(obj4)
# sdata中跟states索引相匹配的三个值放到了相应的位置上，但由于"California"没有对应的值，所以其结果为NaN（not a number，非数字），在pandas中，它用于标记缺失值或NA值。因为"Utah"不在states中，所以它会从结果中出去

# python中的字典是无顺序的，也就是无法通过位置进行索引操作，只能通过key进行索引，但是Series是有顺序的

# 缺失，NA，空值表示缺失数据;pandas的isnull和notnull函数用于检测缺失数据 isna,notna既是pandas级别的方法，也是类方法
print(pd.isna(obj4))
print(pd.notna(obj4))
print(obj4.isna())

# series内置的属性和方法
# 方法
Obj.sum()
Obj.product()
Obj.mean()
Obj.std

# 属性
Obj.size
Obj.is_unique # 查看obj中的值是否唯一
Obj.values  # values 是 ndarray

# Series功能：在运算中，自动对齐索引标签
print(obj3+obj4) # 匹配（相加）索引一样的数据，然后相加
# 这个功能会将拥有同样索引的值进行相加，而如果任意一方的值是Nan，那么相加后的结果都会是Nan，相加后会显示两个Series中所有的内容

# Series对象本身及其索引都有name属性
obj4.name = "population"
obj4.index.name = "state"
# Series的属性可以通过赋值的方法就地修改
print(obj)
obj.index = ["Bob", "Steve", "Jeff", "Ryan"]
print(obj)


```

## Dataframe

DataFrame是矩形的数据表，它含有一组有序且有命名的列，每一列可以是不同的数据类型（数值，字符串，布尔值等）。DataFrame既有行索引，也有列索引。可以把每一列看做Series, Series的index就是DataFrame的行索引，列名就是Series的name。

值为共用Dataframe行索引的Series的字典，字典的键是列名

```python
# 有多种创建Dataframe的方式
# 创建DataFrame的方式：传入一个值为等长列表或等长NumPy数组  构成的字典
data = {"state":["Ohio", "Ohio", "Ohio", "Nevada", "Nevada", "Nevada"], "year": [2000, 2001, 2002, 2001, 2002, 2003],
"pop": [1.5, 1.7, 3.6, 2.4, 2.9, 3.2]}
frame = pd.DataFrame(data)
print(frame)
# 这种方式创建DataFrame， 字典的键是 DataFrame的列， 字典的值是这一列的数据
# 生成的Dataframe会自动加上索引，全部列按照data键的顺序有序排列

# 对于大的DataFrame，head方法会只选取前5行
print(frame.head()) # 默认值是5
print(frame.tail())  # 默认值也是5

# 指定了列的序列，则DataFrame的列就会按照指定顺序进行排列
print(pd.DataFrame(data, columns=["year","state","pop"])

# 如果字典不包含传入的列，就会在结果中产生缺失值，即Nan

# DaraFrame既有行索引，又有列索引
Frame.index()
Frame.columns()

# 通过类似字典标记的方式或者点属性的方式，可以将DataFrame的列获取为一个Series
print(frame2["state"])
print(frame2["year"])
print(frame2.year)
# 注意：
# frame2[column]适用于任意列的名，但frame2.column只有在列名是合理的Python变量才能使用，并且不能与任意的DataFrame的方法名冲突

# 通过赋值的方式可以修改列。例如，我们可以将空的列debt赋值为标量值或数组
frame2["debt"] = 16.5
print(frame2)
 
frame2["debt"] = np.arange(6.)
print(frame2)
# 将列表或数组赋值给某个列时，其长度必须跟DataFrame的长度相匹配。

# 如果赋值的是一个Series，它的索引（标签）就会精确匹配DataFrame的行索引
# 所有的空缺都将填上缺失值
val = pd.Series([-1.2, -1.5, -1.7], index=["two","four","five"])
frame2["debt"] = val
print(frame2)
# 这里的Series的索引没有一个匹配上了Dataframe的行索引
# 另注：这里行索引没有匹配上时是不会新加新的一行的

# 为不存在的列赋值会创建一个新的列
frame2["eastern"] = frame2["state"] == "Ohio"
print(frame2)
# 不能用frame2.eastern的方式创建新的列
# frame.insert(frame.shape[1],’列名’,value)也可以为DataFrame创建一个新列,但是已经有这个列时，再用这个方法会报错

# 关键字del可以像在字典中那样删除列
del frame2["eastern"]
print(frame2.columns)
# 注意：通过索引方式从DataFrame返回的列只是底层数据的视图而已，而不是copy的副本 -> 对返回的Series做修改会反映到DataFrame上。必要时应当通过Series的copy方法来复制列
# 通过字典嵌套字典，创建DataFrame
populations = {"Ohio": {2000: 1.5, 2001: 1.7, 2002: 3.6},
"Nevada": {2001: 2.4, 2002: 2.9}}
frame3 = pd.DataFrame(populations)
# pandas会将外层字典的键解释为列，将内层字典的键解释为行索引
print(frame3)
# 用类似NumPy数组的方法，可以对DataFrame进行转置（交换行和列）
print(frame3.T)
 
# 也可以指定索引，替代内层字典的键
print(pd.DataFrame(populations, index=[2001, 2002, 2003]))
 
# Series作为值的字典，用法类似
pdata = {"Ohio": frame3["Ohio"][:-1], "Nevada": frame3["Nevada"][:2]}
print(pd.DataFrame(pdata))
 
# 如果设置了DataFrame的index和columns的name属性 信息会显示在DataFrame上
frame3.index.name = "year"
frame3.columns.name = "state"
print(frame3)
# 不同于Series，DataFrame本身没有name属性
 
# DataFrame转成numpy的二维ndarray
print(frame3.to_numpy())
print(frame2.to_numpy()) # 触发数据类型兼容

另注：dataframe的赋值，当通过索引改变单独一个元素是虽然会给警告，但仍会执行
df = pd.DataFrame({'A':[1,2,3],'B':['zhangfen','guanyu','liubei']})
df['A'][1] = 9  # warning: FutureWarning: ChainedAssignmentError: behaviour will change in pandas 3.0!
 
#     A	        B
# 0	  1	    zhangfen
# 1   9	     guanyu
# 2	  3	     liubei

```



什么样的类型，可以用来构造pd.Dataframe, 具体的效果是什么样子

| 类型                                     | 说明                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| 二维ndarray/由数组，列表或元组组成的列表 | 数据矩阵，传入可选的行标签和列标签                           |
| 数组/列表/元组作为值的字典               | 每个序列会变成DataFrame的一列，所有序列的长度必须相同        |
| NumPy的结构化数组                        | 结构化数组的索引标签作为列，每个数据作为行                   |
| Series作为值的字典                       | 各个Series构成各个列，如果没有显示指定索引，各Series的索引会被合并成结果的行索引 |
| 字典作为值的字典                         | 各个内部字典构成各个列，内部字典的键会被合并成DataFrame的行索引 |
| 字典或Series的列表                       | 列表各项会成为DataFrame的一行；字典键或Series索引合并成DataFrame的列标签 |
| 另一个DataFrame                          | 该DataFrame的索引将被沿用，除非显式指定了其他索引            |

```python
# 字典的列表
data = [
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 30, "city": "New York"},
    {"name": "Charlie", "city": "San Francisco"}
]
 
df = pd.DataFrame(data)
print(df)
 
# series的列表
s1 = pd.Series({"name": "Alice", "age": 25})
s2 = pd.Series({"name": "Bob", "age": 30, "city": "New York"})
s3 = pd.Series({"name": "Charlie", "city": "San Francisco"})
 
df = pd.DataFrame([s1, s2, s3])
print(df)


```

## 索引对象

pandas的索引对象负责存储轴标签 和 其他元数据（轴名称）。构建Series或DataFrame时，所用到的任何数组或者其他标签序列都会转成索引对象

```python
obj = pd.Series(np.arange(3), index=["a","b","c"])
index = obj.index
index
index[1:]
 
# 索引对象是不可变的，用户不能对其进行修改
# index[1] = "d" # 报错
 
# 不可变性可以使索引对象在多个数据结构之间 安全共享
labels = pd.Index(np.arange(3))
labels
obj2 = pd.Series([1.5,-2.5,0], index=labels)
obj2
 
obj2.index is labels
 
populations = {"Ohio": {2000: 1.5, 2001: 1.7, 2002: 3.6},
"Nevada": {2001: 2.4, 2002: 2.9}}
frame3 = pd.DataFrame(populations)
 
# 索引像数组
frame3.columns
"Ohio" in frame3.columns
2003 in frame3.index
 
# pandas的索引可以包含重复的标签
pd.Index(["foo", "foo", "bar", "bar"])
# 选择重复的标签，会选取所有对应的结果
 
# 索引也像个集合，有集合的方法和属性
# append: 连接另一个索引对象，得到新的索引
idx1 = pd.Index([1, 2, 3])
idx2 = pd.Index([4, 5])
 
result = idx1.append(idx2)
print(result)
 
# difference: 计算差集，并得到一个索引
# A和B的差集：在A里，但不在B里的
idx1 = pd.Index([1, 2, 3, 4])
idx2 = pd.Index([3, 4, 5])
 
result = idx1.difference(idx2)
print(result)  # [1,2]

# intersection: 计算交集
#  A和B的交集：在A 也在B
idx1 = pd.Index(['a', 'b', 'c'])
idx2 = pd.Index(['b', 'c', 'd'])
 
result = idx1.intersection(idx2)
print(result)  # idx1 与 idx2 都有的元素集合 [‘b’,’c’]

# union：计算并集
idx1 = pd.Index([1, 2, 3])
idx2 = pd.Index([3, 4, 5])
 
result = idx1.union(idx2)
print(result)

# isin： 计算一个指示各值   是否都包含在参数集合中的布尔型数组
idx = pd.Index(['apple', 'banana', 'cherry'])
mask = idx.isin(['banana', 'cherry', 'date'])
print(mask)  # 返回bool型数组(numpy数组)，用来看各个元素是否在传进去的参数列表里 [F,T,T]

# delete: 删除索引对象的 索引i处的元素，得到新的索引
idx = pd.Index(['a', 'b', 'c'])
new_idx = idx.delete(1)
print(new_idx)  # 删除索引‘b’，返回[‘a’,’c’]

# drop：删除传入的值，并得到新的索引
idx = pd.Index(['x', 'y', 'z'])
new_idx = idx.drop('y')
print(new_idx) 

# insert: 将元素插入 位置的i处，并得到新的索引
idx = pd.Index([10, 20, 30])
new_idx = idx.insert(1, 15) # 在位置1插入15
print(new_idx)  # Index([10, 15, 20, 30], dtype='int64')

# is_monotonic_: 当各元素均大(小)于等于前一个元素时，返回True/False
# 这是属性，另外字符本身也有大小，基本按照字母表顺序
idx1 = pd.Index([1, 2, 3])
idx2 = pd.Index([3, 2, 1])
 
print(idx1.is_monotonic_increasing) # True
print(idx2.is_monotonic_decreasing) # True

# is_unique: 当索引没有重复值时，返回True
idx1 = pd.Index(['a', 'b', 'c'])
idx2 = pd.Index(['a', 'b', 'a'])
 
print(idx1.is_unique) # True
print(idx2.is_unique) # False

# unique: 计算索引中唯一值，得到去重索引
# 这是方法
idx = pd.Index(['apple', 'banana', 'apple', 'cherry'])
uniq = idx.unique()
print(uniq)

```

## Series和DataFrame的操作

### 重建索引

```python
# reindex : 创建数据   根据传入索引  重新排列的新对象
obj = pd.Series([4.5,7.2,-5.3,3.6], index=["d","b","a","c"])
obj
 
# 数据重排，并引入缺失值
# abcd在数据列表里，这些数据进行重排 带上新的标签：e不在原先的数据列表里，就会生成新的标签，对应的值就是NaN
obj2 = obj.reindex(["a", "b", "c", "d", "e"])

# 重建索引（reindex）可能需要做一些插值/填值处理，method关键字参数可以达到此目的
obj3 = pd.Series(["blue", "purple", "yellow"], index=[0,2,4])
obj3
obj3.reindex(np.arange(6), method="ffill") # forwordfill 根据前面的值进行填充
# ffill: 前向填充值，‘bfill’ 向后填充，另外，当使用method时一定要确保axis上的索引保持单增或是单减

# DataFrame 做reindex 可以修改行索引，列索引，也可以同时修改 dataframe 传入的不仅仅可以是字典，也可以是numpy的二维数组
frame = pd.DataFrame(np.arange(9).reshape((3,3)), index=["a","c","d"], columns=["Ohio", "Texas", "California"])

frame
# 修改行索引
frame2 = frame.reindex(index=["a","b","c","d"])
frame2
# 修改列索引 
states = ["Texas", "Utah", "California"]
frame.reindex(columns=states)
# 行列索引都会将元数据按照新的索引进行重排，没有的行或列回填上NaN，新列表内没有的会删除
# 另外可以同时重新排列 行与列
# 因为Ohio不在states中，所以这一列从结果中删除

# 另一种重建索引的方式是传入新的轴标签作为位置参数，然后用axis关键字对指定轴进行重建索引
frame.reindex(states, axis="columns")

# reindex参数说明
# labels: 用作索引标签的新序列，既可以是索引对象(pd.Index)，也可以是其他序列型的Python数据结构(list)。     索引对象被直接使用，无须复制
# index: 使用传入的序列作为新的索引标签  ：行索引
# columns: 使用传入的序列作为新的列标签  ： 列索引
# axis: 要重建哪个轴，可以是行，也可以是列 默认是行 # - method: 用于指定填充缺失值（NaN）的方法，常用于时间序列数据的对齐。可选值有：
- None（默认）：不进行填充，缺失值为NaN。
- 'ffill' 或 'pad'：用前一个有效值进行向前填充（forward fill）。
- 'bfill' 或 'backfill'：用后一个有效值进行向后填充（backward fill）。
- 仅当新索引与原有索引不完全重合，且需要用已有数据填补缺失项时才用到。

# 使用loc运算符重建索引
# 使用loc重建索引，标签必须在DataFrame中已经存在
# 注意loc后面是方括号
frame.loc[["a","d","c"], ["California", "Texas"]]

```

### 删除指定轴上的数据

```python
# drop方法返回的是一个 在指定轴上删除了指定值的 新对象
# 要drop的索引标签 一定已经在Series/Dataframe里面，否则会报错
# 对series做drop
obj = pd.Series(np.arange(5.), index=["a","b","c","d","e"])
obj
new_obj = obj.drop("c")
new_obj
 
obj.drop(["d", "c"])

# 对DataFrame做drop
# 对dataframe做drop，可以删除任意轴上的索引
data = pd.DataFrame(np.arange(16).reshape((4,4)), index=["Ohio", "Colorado", "Utah", "New York"], columns=["one","two","three","four"])

# 第一个位置参数，默认对行索引做drop
data.drop("New York")
data.drop(["New York", "Ohio"]) # 一次性删除多行

# del dataframe[列的索引名] 这是直接对dataframe进行修改，而且列的索引名必须存在
# 可以指定删除行的标签 或者是 列的标签
data.drop(index=["Colorado", "Ohio"])
data.drop(columns=["two"])
 
# 可以传入axis=1，或axis="columns”，从列删除值
data.drop("two", axis=1)
data.drop(["two", "four"], axis="columns")

```

### 索引、选取和过滤

在Series中的操作：

==loc方式更可取，它一定是基于索引的标签来索引数据==

> ```python
> # Series索引工作方式类似于NumPy数组的索引，但Series的索引值可以不仅仅是整数
> obj = pd.Series(np.arange(4.), index=["a","b","c","d"])
> obj
> obj["b"]
> obj[1] # 这里会给出警告，因为1 并没有在obj的索引列表里
> obj[2:4] # 这里没有警告，会把整数当成位置，这种情况只能在索引全是字符串不含整数的时候才能正常执行
> obj[“c”:”d”] # 这里与python切片不同，是会取到上限的数据的
> obj[["b","a","d"]]  # 返回一个Series，多组数据会保留标签
> obj[[1,3]]  # 会警告同obj[1]
> obj[obj<2]
> 
> # 虽然可以直接[] 来用标签选取数据，但是更可取的方式是使用特殊的loc运算符选取索引值
> obj.loc[["b","a","d"]]
> # loc方式更可取，它一定是基于索引的标签来索引数据
> # 如果索引包含整数，基于[]的索引会将整数用作标签，索引选取因数据类型而变化
> obj = pd.Series(np.arange(4.), index=["a", 2,0,1])
> obj[0] # 2.0
> obj[2] # 1.0
> # obj[3] # 报错，因为索引标签里只有0，1，2
> # 对与Series而言，无论是基于位置的索引与切片，和布尔型的索引 尽量用.loc
> # 基于位置而言尽量用.iloc
> 
> # loc运算符只使用标签 /布尔型索引，iloc运算符只使用整数（位置），无论索引标签是否包含整数，都能使用iloc （Series有位置）
> # 可以用loc，使用标签来切片，但区别python的切片方式，loc的切片是包含末端的
> # 使用.iloc 进行切片和python是一样的，有首无尾
> 
> # 切片可以用对相应部分赋值
> obj2.loc["b":"c"] = 5
> obj2
> # 注意：loc和iloc不是像函数那样调用，正确的方式是用方括号进行索引
> 
> ```

在Dataframe中的操作：

```python
# 单个值或序列对DataFrame进行索引，获取的是单列或者多列
data = pd.DataFrame(np.arange(16).reshape(4,4), index=["Ohio", "Colorado", "Utah", "New York"], columns=["one", "two", "three", "four"])
 
data
data["two"] # del data["two"]
data[["three","one"]]
 
# 单个值和序列的时候筛选的是列;
# 切片和布尔型Series筛选的是行
data[:2]
data[data["three"]>5]
 
# 另一种用法：通过布尔型DataFrame进行索引，比如由标量比较运算得出的DataFrame，所有值都是与一个标量比较得出的布尔值
data < 5
data[data<5]
data[data<5] = 0 # 赋值给等于True的位置
 
# 用loc和iloc选取DataFrame
# 与Series一样，DataFrame有两个特殊属性loc和iloc，分别用于标签索引和整数位置索引。
# 由于DataFrame是二维的，因此可以用Numpy风格的语法，选取行和列的子集
data
data.loc["Colorado"]
# 取出的单独一行是Series，它的索引是DataFrame的列标签
 
# 选取多行，可以传入标签序列
data.loc[["Colorado", "New York"]]
 
# 如果要用loc同时选取行和列，可以用逗号将选取过程分隔开
data.loc["Colorado", ["two", "three"]]
 
# 再用iloc 整数位置索引做一些类似的选取操作
data.iloc[2]
data.iloc[[2,1]]
data.iloc[2,[3,0,1]]
data.iloc[[1,2],[3,0,1]]
 
# 除了单个标签，多个标签，这两个索引函数也可以使用切片
data.loc[:"Utah","two"]
data.iloc[:,:3][data["three"] > 5]
 
# loc可以使用布尔型数组，但iloc不能使用
data.loc[data["three"]>=2]

```

==有多种方式可以选取和重排存储在pandas的数据，对DataFrame的数据选取方法做简短总结：==

![image-20250628123118173](C:\Users\lxy12\AppData\Roaming\Typora\typora-user-images\image-20250628123118173.png)

### 整数索引中的陷阱

==整数索引中的陷阱==

1. ==pandas索引里包括了整数时， 进行整数索引时，pandas会按照标签来索引，因为这种时候标签索引和位置索引有歧义，标签索引==

  ==所以pd.Series(np.arange(3.))[-1]这种代码会报错，因为-1不是Series的标签==

2. ==非整数的索引标签，没有歧义，用整数索引会按位置进行。==
3. ==切片操作总是基于位置的==
4. ==进行索引时，推荐使用loc（基于标签）和iloc（基于位置的整数索引），语义明确==
5. ==赋值时避免使用链式索引==



```python
import pandas as pd
import numpy as np
ser = pd.Series([1,2,3,4,5])

ser
# ser[-1]  # 报错：-1不在标签里，标签只有0 ，1，2，3，4

ser2 = pd.Series(np.arange(3.), index=["a", "b", "c"])
ser2
# ser2[-1]  # 虽然能正常进行，但警告 __getitem__ (对象能够[]操作，因为类里实现了__getitem__), 这种索引方式会被一直当作标签

# 切片操作基于位置
# ser2[:2]
#
# 推荐用iloc表示位置定位
# ser.iloc[-1]
# ser2.iloc[-1]
# ser2.iloc[:2]
#

# 通过标签，或者 布尔型索引赋值（对True赋值）
data = pd.DataFrame(np.arange(16.).reshape((4,4)), index=["a","b","c","d"], columns=["one", "two", "three", "four"])
data.loc[:, "one"] = 1
data
#
# data.loc[data>5]  # 报错， loc里的数组不能超过1维
# data[data>5]  # 这里体现DataFrame 直接索引，有必要性的
#
# data.iloc[2]
data.iloc[2] = 5
data
#
data.loc[data["four"] > 5] = 3  # 第四列 大于5的那些行的数据 全部改成3
data
#
#
# # 链式选取后 去赋值会抛出警告 SettingWithCopyWarning, 警告用户正在设置临时值，而不是data本身
# data.loc[data.three==5], type(data.loc[data.three==5])
# data.loc[data.three==5]["three"] = 3


#   data.three:  data["three"]; 列名是合法变量名，又不是dataFrame里的内置方法/属性的时候， data_frame.列名
# data.loc[data.three==5].loc[:, "three"] = 3  这种也是链式索引赋值
data.loc[data.three==5, "three"] = 3  # 正确写法：避免赋值时链式索引

# print(data)

```

### 算术运算和数据对齐

1. 不同索引的对象做算术运算，结果中的索引是所有索引的并集
2. 索引并集对 不在两个对象中都出现的索引标签 会引发缺失值
3. DataFrame的索引并集操作会同时发生在行和列上
4. 当某个轴的标签不在另一个对象中时，可以指定填充值
5. 算术方法有对应的字母r开头的副本，表示参数反转 a*b -> b*a
6. DataFrame和Series可以运算，类似NumPy的广播，默认按行广播，匹配列索引



```
s1 = pd.Series([7.3, -2.5, 3.4, 1.5], index=["a","c","d","e"])
s2 = pd.Series([-2.1,3.6,-1.5,4,3.1], index=["a", "c","e","f","g"])
s1
s2
#  两个series做加法运算的时候，会让两个Series的标签对齐  （两个Series的标签Index对象，做并集操作）
# s1：                     +    s2：
# a     7.3                     -2.1
# c     -2.5                    3.6
# d     3.4                     NaN
# e     1.5                     -1.5
# f     NaN                     x
# g     NaN                     y

print(np.nan + 1)  # nan
s1+s2  # 引入缺失值，缺失值会在计算中传播

# # DataFrame，索引并集（对齐）同时发生在行和列上
df1 = pd.DataFrame(np.arange(9.).reshape((3,3)), columns=list("bcd"), index=["Ohio", "Texas", "Colorado"])
df2 = pd.DataFrame(np.arange(12.).reshape((4,3)), columns=list("bde"), index=["Utah", "Ohio", "Texas", "Oregon"])
df1
df2
#
df1 + df2
# # c,e列没有都存在于df1和df2里，所以这两列是缺失值；Colorado, Oregon和Utah行也是如此
#
# # 如果没有共用的行和列标签的DataFrame相加，结果全部为NaN
df1 = pd.DataFrame({'A': [1,2]})
df2 = pd.DataFrame({'B': [3,4]})
df1
df2
#  df1   A   B
# 0      1   nan
# 1      2    nan

#   df2  A      B
#    0   nan    3
#    1   nan    4
# df1 + df2
#
# # 当某个轴的标签不在另一个对象中时，指定填充值  (某个轴标签，在算术运算的某一方是nan的时候，我可以指定填充值，填充值会生效，双方都是NaN，填充值无效）
df1 = pd.DataFrame(np.arange(12.).reshape((3,4)), columns=list("abcd"))
df2 = pd.DataFrame(np.arange(20.).reshape((4,5)), columns=list("abcde"))
df2.loc[1,"b"] = np.nan
df1
#
df1 + df2
# +: dataframe对象里的add方法，  df.add(df2, fill_value=遇到缺失值，填充值是多少）
df1.add(df2, fill_value=0) # 通过add方法，以及fill_value关键字参数，传入值替换运算中的nan
#
# 每个算术方法，add/radd，sub/rsub，mul/rmul，div/rdiv  a.div(b)  a/b , a.rdiv(b), b / a
1 / df1  # 每个算术方法（add(+),sub(-),mul(*),div(/),floordiv(//),power(**)）都有一个r方法，r->re 后面，就是交换位置减数变成被减数，除数变成被除数
df1.rdiv(1)  # 和上面等价

#  如果想填充缺失值，得用算术方法，而不是运算符

# 重建索引（reindex)时，也可以指定不同的填充值
df1.reindex(columns=df2.columns, fill_value=0)

# # DataFrame和Series的运算
arr = np.arange(12.).reshape((3,4))
arr
arr[0]
#

# （3，4）  -
#     4)
arr - arr[0]  # arr[0]复制了3行，numpy广播
#
# DataFrame和Series之间的运算机制类似
frame = pd.DataFrame(np.arange(12.).reshape((4,3)), columns=list("bde"), index=["Utah", "Ohio", "Texas", "Oregon"])
#
series = frame.iloc[0]  # 定位dataframe第一行
frame
series
#
# frame - series  # 默认series的索引去匹配frame的列，然后沿着行进行广播
#
# 如果索引值在DataFrame的列或Series的索引中找不到，则它们会重建索引，形成并集，也是列匹配，沿着行广播
series2 = pd.Series(np.arange(3), index=["b", "e", "f"])  # ["b","e","f"], data_frame: ["b","d","e"] -> ["b","d","e","f"]
#     series2 -> 数据[0,NaN,1,2]
series2.reindex(list("bdef"))
# series2
frame + series2
#

# 如果希望行匹配，沿着列广播，必须使用算术运算方法，在方法内用关键字参数指定 axis=“index"
# dataframe 和 它其中一列去做运算  （在行做索引匹配，沿着列广播）
# frame = pd.DataFrame(np.arange(12.).reshape((4,3)), columns=list("bde"), index=["Utah", "Ohio", "Texas", "Oregon"])

# （4，3）  （4，）
series3 = frame["d"]
series3
frame.sub(series3, axis="index")  # 匹配行索引，进行列广播

```

### 函数应用和映射：apply 和 map

apply是行或列进行操作模块，而map是元素级方法，对Dataframe中的每一个元素进行操作

my_dataframe.apply()

```
# 1. numpy函数操作DataFrame

frame = pd.DataFrame(np.random.standard_normal((4,3)), columns=list("bde"),
                     index=["Utah", "Ohio", "Texas", "Oregon"])
frame
np.abs(frame)

# # 2. 将函数应用到 各行，各列形成的一维数组上， apply
# #    按行 / 按列处理DataFrame，返回一个数字（聚合），或者返回Series
frame.mean()   # 求整个列的平均值， 取到整个列的series，对列做一个求平均值的操作:  整个列->数字 ； 消掉了行索引的series，标签是列的名字
frame.mean(axis="index")
frame.mean(axis="columns")   # 整个行的series  -> 数字， 消掉了列索引的series，series的标签的行标签

#  dataframe的apply方法： 自定义：怎么把行/列series  ——> 数字
#   data_frame对象.apply(函数):   函数： 参数：series  返回值：数字  注意：把函数当成参数传给data_frame的apply方法

#     data_frame对象.apply(函数),  默认这个函数的参数 是data_frame其中一列 （默认对某列做聚合，消掉行标签）
# frame["d"].mean()
#
def my_mean(a_series):
    return a_series.mean()

def my_custom_func(a_series):
    return a_series.iloc[0] * a_series.iloc[1]
# data_frame.apply(函数）  # 函数：默认 处理 data_frame的列的series ->  数字
frame.apply(my_mean)
frame.apply(my_custom_func)
frame.apply(lambda col : col.mean())
frame.apply(lambda col : col.mean(), axis=0)
frame.apply(lambda col : col.mean(), axis="index")

#   apply的函数 去处理frame里的每一行的数据：  data_frame.apply(函数, axis=1)   data_frame.apply(函数, axis="columns")
frame.apply(lambda row: row.mean(), axis=1)
frame.apply(lambda row: row.mean(), axis="columns")
#
# # sum, max
frame.max()  # 求frame所有列的最大值，最后得到一个Series，标签列名，数据是每列的最大值
frame.max(axis="index")
frame.max(axis="columns")  # 求frame所有行的最大值，最后得到一个Series，标签 行标签，数据是每行的最大值4

# frame.max() 等同于：
frame.apply(lambda col : col.max())

# frame.max(axis="columns") 等同于
# frame.apply(lambda row : row.max(), axis="columns")

# frame.sum()
# frame.sum(axis="index")
# frame.sum(axis="columns")
#
frame.apply(lambda col: col.max() - col.min())
frame.apply(lambda row: row.max() - row.min(), axis="columns")
#
# apply传入的函数，返回值可以是Series，
#   Series -> Series
# data frame apply的结果，还会是个DataFrame

# 得到每列的    [最大值 最小值] -> pandas的Series pd.Series([最大值 最小值])
def my_custom_func(a_series):
    return pd.Series([a_series.max(), a_series.min()], index=["max", "min"])

frame.apply(my_custom_func)  # 每一列处理的结果（返回值） Series
frame.apply(lambda col: pd.Series([col.min(), col.max()], index=['min', 'max']))
frame.apply(lambda row: pd.Series([row.min(), row.max()], index=['最小', 'max']), axis="columns")


#  apply对 Series做操作
#  数据级的操作：data_frame.map(函数）
#                函数：data_frame里的数据的值 ->  新的一个数据的值
# # 3. 使用map，可以将函数应用到DataFrame里每个数值上  -> 新的DataFrame，里面的数 函数(原来dataframe里的数）
frame.map(lambda x : x if x >= 0 else -x)
frame.map(lambda x: f"{x:.2f}") # frame中各个浮点数值 转成格式化字符串
#
# series里也有元素级的map方法：
frame["e"].map(lambda x: f"{x:.2f}")

```

### 排序和排名

1. sort_index: 对索引排序

​		axis="columns"/"index"/"row",根据传入轴向进行行索引或是列索引的排序

​		ascending=False/True,指定顺序默认升序排列

​		level=0/1/...，这是在df的传为轴向为多层次索引时才会用到的，对指定的层次的索引进行排序

​		inplace=True/False,sort_index函数不会对原本的得分产生影响的，只有当inplace=True时会改变原df

2. sort_values: 对Series/DataFrame的数值排序，DataFrame要指定依据的列

​		by=  ，传入行索引或者列索引，行列索引必须与axis相对应

​		axis = "columns"/"index"/"row",默认为index，列索引<->index，行索引<->columns

​		ascending=True/False，指定顺序默认升序排列，这里的by参数可以传入列表，第一个为主，后面的优先度以此降低，此时如果传入同长度的bool型列表，此时会根据两者顺序相对应来进行升序还是降序排列

​		na_position="first"/"last",指df中在排序时nan是排在头部亦或是末尾，默认为last

3. rank：返回数据的排名

​		axis = index/columns/row，默认为index，此时df按列来进行排序

​		ascending=True/False

​		method=，是排名的方法

​			=="averaget": 默认：并列的 分配平均排名==

​			=="min": 并列的  使用最小排名==

​			=="max": 并列的  最大排名==

​			=="first": 按值在原始数据中出现的顺序分配排名==

​			=="dense": 类似于method="min", 但排名在组间增加1，而不是组中相等元素的数量==

```
obj = pd.Series(np.arange(4), index=["d","a","b","c"])
obj
obj.sort_index()  # 对索引排序

frame = pd.DataFrame(np.arange(8).reshape((2,4)), index=["three", "one"], columns=["d","b","a","c"])
frame
frame.sort_index()  # sort 行索引 默认行为
#
frame.sort_index(axis="columns") # sort 列索引
frame.sort_index(axis="rows")  # sort 行索引
frame.sort_index(axis="index") # sort 行索引
frame.sort_index(axis="index").sort_index(axis="columns")
#
frame.sort_index(axis="columns", ascending=False)  # 降序排列
#
# # 排序：值排序 ： sort_values
obj = pd.Series([4,7,-3,2])
obj.sort_values()  # 注意索引会跟着数据
#
obj = pd.Series([4, np.nan, 7, np.nan, -3, 2])
obj.sort_values()  # nan默认排在某尾

#    sort_values(na_position="first")
obj.sort_values(na_position="first") # nan排在前面
#
# #  将一列或者多列中的数据，作为排序键(排序的语句），传一个或者多个列名给sort_values
frame = pd.DataFrame({"b": [4,7,7,-3,2,4,-3,2], "a":[0,1,0,1,2,8,9,-1]})
frame
frame.sort_values(by="b")  # 注意sort虽然根据b的列，但是 整个行在排序
frame.sort_values("b")

frame.sort_values("b", ascending=False)    #降序

#  现根据 b这一列排序，b的值一样，再根据a排序   传入的第一列值一样，才会考虑第二列的顺序
frame.sort_values(["b","a"])  # 默认都是升序
frame.sort_values(["b", "a"], ascending=False) # 降序
frame.sort_values(["b", "a"], ascending=[True, False])  # b升序，a降序

# rank
# 排名从数组中的最小值开始，从1一直到数组中有效数据的数量。
obj = pd.Series([7,-5,7,4,2,0,4])
print(obj)
# series.rank()  -> series  标签对应排名
obj.rank()
obj.rank().sort_values()
# # 处理平级关系：为各组分配平均排名：4 5->4.5; 6,7->6.5
#
# # 其他处理平级关系的方式：根据值在原数据中出现的顺序来排名  method="first"
#    rank(method="first")
obj.rank(method="first").sort_values()  # 行索引0 2数值都是7，排在最后两位，0比2更早出现，0第6，2第7

# rank(ascending=False)
obj.rank(ascending=False).sort_values() # 使用降序进行排名
#
# DataFrame可以在行 或者在列上计算排名
frame = pd.DataFrame({"b":[4.3, 7, -3, 2], "a":[0,1,0,1], "c":[-2, 5, 8, -2.5]})
frame
print(frame)
frame.rank()  # 把整个行索引的数据取来排名  对每一列的数据做了排名 ->  data_frame
frame.rank(axis="index")
frame.rank(axis="columns") #  把整个列索引数据取来排名

#
# # 排名中处理平级的各种方法：rank(method=?)
# # "averaget": 默认：并列的 分配平均排名
# # "min": 并列的  使用最小排名
# # "max": 并列的  最大排名
# # "first": 按值在原始数据中出现的顺序分配排名
# # "dense": 类似于method="min", 但排名在组间增加1，而不是组中相等元素的数量
frame = pd.DataFrame({"b": [4,7,7,-3,2,4,-3,2], "a":[0,1,0,1,2,8,9,-1]})
print(frame)
frame.rank(method="min")
frame.rank(method="max")
# frame.rank(method="min")["a"].sort_values()

```

### 小结

Pandas中的广播机制和numpy中是不一样的，numpy的广播要求同维度上其中一个为1才能广播，但是pandas的广播是基于索引的同索引广播，无索引填np.nan

在pandas运算当中，会进行扩充，当进行两个dataframe的运算时，两者的index和columns会进行union，在对两个dataframe进行reindex，形成两个新的dataframe，缺失值处填充np.nan，fill_value参数会将缺失值进行填充，填入的值就是传入的值，但是这种方法只能填充其中一个，如果同索引处的值在两个新dataframe中都是np.nan，那么运算后还np.nan

### 带有重复标签的轴索引

索引操作的返回值会根据标签是否重复而发生变化

在series中当通过索引没有重复的情况下返回单个值，但是有重复的情况下会返回所有重复索引的series，切片会从第一个索引到木表索引

==注：当索引有序的时候或者重复索引全部都在一起时才会显示正常的切片，切片范围包含了范围内全部的重复索引==

```python
mport numpy as np
import pandas as pd
obj = pd.Series(np.arange(5), index=["a","a","b","c","b"])
obj
obj['a':'b']   # KeyError: "Cannot get right slice bound for non-unique label: 'b'"

obj = pd.Series(np.arange(5), index=["a","a","b","b","c"])
obj['a':'b']
# a    0
# a    1
# b    2
# b    3
# dtype: int32


df = pd.DataFrame(np.random.standard_normal((5,3)), index=["a","a","b","b","c"])

df
df.loc["a"]  # 得到DataFrame
df.loc["c"]  # 得到Series
df.loc["a":]
df.loc[:"b"]
```

### 描述性统计的汇总和计算

统计中axis时要聚合的维度，skipna跳过缺失值，默认为True

df.sum/mean/product(): 

​	axis = "columns"/"index"/"row",根据指定的轴向求，默认为index，此类聚合函数的轴向输入是需要聚合的轴，默认是将行聚合消除index轴

​	skipna=True/False，计算求是对待np.nan是否跳过

df.idxmax() ：不能对整个行或者列都是缺失值的dataframe和series求最大值最小值索引

​	axis="columns"/"index"/"row",默认为index

​	skipna=True/False，默认为True

df.cumsum/cumprod():

​	axis="columns"/"index"/"row",默认为index

​	skipna=True/False，默认为True

df.describe()

```python
np.sum(np.array([np.nan, 1,2]))  # np.sum不处理缺失值

df = pd.DataFrame([[1.4, np.nan], [7.1,-4.5], [np.nan, np.nan], [0.75, -1.3]], index=["a","a","b","b"], columns=["one", "two"])
df
df.sum() # 跨行求和，返回包含列之和的Series，忽略缺失值

df.sum(axis="columns")

# 可以决定是否跳过 缺失值
df.sum(axis="index", skipna=False)
df.sum(axis="columns", skipna=True)

df.mean(axis="columns")  # 对行数据求平均值，有一行的数据全是NA，所以平均数也是NA，平均数需要至少一个非NA值

# 聚合方法的常用选项：axis：聚合掉哪个轴； skipna: 排除缺失值，默认为True，level：层次化索引相关

# 达到最小值，或者最大值的索引：
df.idxmax() # 对每一列求在最大值并返回行标签 可以指定轴向
df.idxmin()
# 不能对整个行或者列都是缺失值的dataframe和series求最大值最小值索引

# 累计型统计：
df.cumsum(skipna=False) # 这个不会跳过nan，且之后的数据都是nan
df.cumsum() # 虽然会跳过缺失值，但是仍会在缺失值处生成结果nan
df.cumsum(skipna=True)
df.cumsum(axis="columns", skipna=False)

# 一次性生成多个汇总统计
df.describe()

# 非数字型数据，describe会产生另外一种汇总统计
obj = pd.Series(["a","a","b","c"]*4)   
obj.describe()  # count     16
# unique     3    去重后的元素个数
# top        a    出现最多的元素
# freq       8     出现最多元素的频率
# dtype: object

```

### 协方差与相关系数

1. 协方差（Covariance）

- 定义：衡量两个变量之间总体的线性关系方向（同涨同跌还是一涨一跌）。
- 公式：

  $$
  \mathrm{Cov}(X, Y) = \frac{1}{n-1} \sum_{i=1}^{n} (x_i - \bar{x})(y_i - \bar{y})
  $$

- 解读：
    - 协方差为正，表示两个变量大致同增同减；
    - 协方差为负，表示一个变量增 意味着另一个减；
    - 数值大小受变量单位和尺度影响，无法直接比较不同变量的协方差大小。


2. 相关系数（Correlation Coefficient, Pearson's r）  皮尔逊系数

- 定义：协方差的标准化版本，衡量两个变量之间的线性相关强度与方向，取值范围 [-1, 1]。
- 公式：

  $$
  r_{XY} = \frac{\mathrm{Cov}(X, Y)}{\sigma_X \sigma_Y}
  $$

- 解读：
    - r = 1，完全正相关；
    - r = -1，完全负相关；
    - r = 0，无线性相关；
    - 相关系数无单位，便于不同变量间比较。


总结：协方差关注强度和“方向”，相关系数关注方向，(-1到1),且去除了单位影响。

```python
# 相关系数：反应两个变量的线性相关程度，取值范围[-1,1]
#   -1:负相关；0：没有关系； 1：正相关
# 协方差：相关系数乘上两个变量 的标准差的乘积，可以把相关系数 看成标准化的协方差
price = pd.read_pickle("examples/yahoo_price.pkl")
volume = pd.read_pickle("examples/yahoo_volume.pkl")

price
volume

# 计算股价的百分比变化
returns = price.pct_change()

# Series的corr方法用于计算两个Series中重叠的，非NA的，按索引对齐的值的相关系数；
#         cov用于计算协方差

returns["MSFT"].corr(returns["IBM"])
returns.MSFT.corr(returns.IBM)
returns["MSFT"].cov(returns["IBM"])

# DataFrame的corr和cov方法将以DataFrame的形式 返回完整的相关系数和协方差矩阵
returns.corr()
returns.cov()

# 利用DataFrame的corrwith方法，可以计算一个DataFrame中列或行 与另一个Series或DataFrame之间的相关系数
returns.corrwith(returns["IBM"])
# 传入一个Series时，将会返回一个按列计算的相关系数值的Series

# returns.corrwith(returns["IBM"], axis="columns")  # 可以跨列计算（按行计算），但行的Series的标签是列索引，计算相关系数之前，所有的数据项目都会按标签对齐，这里按行计算没有和 returns["IBM"]对齐的标签
```

### 唯一值，计数

\- isin():     计算表示 Series 各值是否包含于传入序列的布尔型数组
 \- unique():    计算 Series 中的唯一值数组，按发现的顺序返回
 \- value_counts(): 返回一个 Series，唯一值作为索引，频次作为值，频次按降序排列

```python
obj = pd.Series(["c", "a", "d", "a", "a", "b", "b", "c", "c"])
obj.is_unique

# Series对象的unique方法，用于生成Series中的唯一值的数组：
#  series.unique()   -> numpy数组，数组里全是唯一值
uniques = obj.unique()
uniques, type(uniques)
#
#  Series的value_counts方法 用于计算Series中各值出现的频次
#  series.value_counts()  -> 返回series，标签是series里的唯一值，数值唯一值的出现次数 (按频次的从大到小排列）
obj.value_counts()

#
# pd.Series(np.array([0,1,0,9,6,3,0,2,0,1,2])).value_counts()
# pd.Series(list("asfsdfds")).value_counts()
# pd.Series(list("asfsdfds")).value_counts().iloc[:5]

# isin可以执行向量化的成员属性检查，
# 可用于以Series或DataFrame中一列的形式，将数据集过滤为子集

#   series.isin(列表/元组/numpy数组)  对series的每个数据，判断 是否 in 给定的序列里  ->  标签沿用了原来的series，数据True/False
obj
mask = obj.isin(["b","c"])
mask

obj.loc[mask]

# 索引对象的get_indexer方法，检查Series的数值，在索引对象内出现的序号
#   要检查的数据 创建 索引对象(pd.Indexer);  索引对象 get_indexer方法，检查series数值在这个索引出现的序号是多少

to_match = pd.Series(["c","a","b","b","c","a", "e"])


# 检查 to_match 在 ["c","b","a"]这个列表里，的出现顺序是什么
unique_vals = pd.Series(["c", "b", "a"])
# unique_vals = pd.Series(["c","c", "b", "a"])  # 能用get_indexer的方法索引对象，索引里必须唯一
indices = pd.Index(unique_vals).get_indexer(to_match)
indices  # numpy数组， to_match里的每一个值，在unique_values里的出现位置是什么
# 索引对象内的标签必须唯一，否则会报错

# 对DataFrame的其中一列，做value_counts
data = pd.DataFrame({"Qu1": [1,3,4,3,4],
                     "Qu2": [2,3,1,2,3],
                     "Qu3": [1,5,2,4,4]
                     })

# data
data["Qu1"].value_counts()  # series: 这一列每个标签出现频次；索引本身的内容是这一列的唯一值  索引的 .index.name列的名字，series的名字count
data["Qu1"].value_counts().sort_index()

# # 对DataFrame的所有列，做value_counts
#   Qu1  Qu2 Qu3
# 1  1    x   x
# 3
# 4
# 2
# 5

result = data.apply(lambda col  : col.value_counts())
# 函数 ：一列 ->  这列唯一值的频次统计的series  每一列生成的series，series标签会对齐 合并成DataFrame

# data_frame的填充缺失值函数 r
result.fillna(0)  # 生成的dataframe
# result
# result.fillna(0, inplace=True)  # 对result本身做fillna的操作
# result
#
# DataFrame本身可以做value_counts,它会将DataFrame的每行当成元组，针对不同行的计数, 返回一个series，series的值 是每行的出现次数
print(data)
data.value_counts()
# print(type(data.value_counts()))  #
#
# 行索引不再是单独的标签，而有多个 ，这个是层次化索引/多级索引，后续讲解
# data.value_counts().index

```

### 日期操作

pandas特有的数据类型datetime64，而当建立dataframe并没有对应的语言将某一列当成日期，此时可以用以下方法：

```python
date=pd.to_datetime(df["date"]) # 将dataframe中的date列转变成datetime64
date.dt.date.year # 因为是日期的series  有dt（date time）属性 .dt获取的就是series里的日期数据操作对象也可以是month/day
date.dt.time # datetime64 不仅有年月日还可以有具体的时间，此时可以获取到具体时间
```



## 文本类文件读写： CSV, JSON, XLSX

\- pd.read_cvs
 \- pd.read_json
 \- pd.read_excel

表格型数据读取为DataFrame: pd.read_csv

### Cvs读取

函数的选项划分为以下几类：

1. 索引
2. 类型推断和数据转换
3. 日期和时间解析
4. 迭代：对大文件进行逐块迭代
5. 不规整数据：跳过行，页脚，注释
6. pandas read_csv参数数量多，读取csv遇到问题考虑设置read_csv的参数解决，参数作用和示例参考线上文档：https://pandas.pydata.org/docs/user_guide/io.html#io-read-csv-table

 

```python
pd.read_csv("E:\\kecheng\\618\\pandas618\\examples\\ex2.csv", header=None)  # header: 哪一行是列名，为None表示第一行是数据，不是列名，自动生成整数索引
pd.read_csv("E:\\kecheng\\618\\pandas618\\examples\\ex2.csv", names=["a","b","c","d", "message"])  # names参数指定列名

# 使用表里某一列作为每一行的索引
names = ["a", "b", "c", "d", "message"]
pd.read_csv("E:\\kecheng\\618\\pandas618\\examples\\ex2.csv", names=names, index_col="message")

#   a  b  c  d
# message            
# hello 1  2  3  4
# world 5  6  7  8
# foo   9  10 11 12

# 将多列做成层次化索引（多级索引），只需传入由列编号或者列名 组成列表即可
parsed = pd.read_csv("E:/kecheng/618/pandas618/examples/csv_mindex.csv", index_col=["key1", "key2"])
parsed
#      value1 value2
# key1  key2      
# one   a  1  2
# b 3  4
# c 5  6
# d 7  8
# two   a  9  10
# b 11 12
# c 13 14
# d 15 16

# 有些表格使用的可能不是固定的分隔符，而是空白符或其他方式分隔字段

# 处理不同数量的空白字符间隔： sep="\\s+"
result = pd.read_csv("E:/kecheng/618/pandas618/examples/ex3.txt", sep="\\s+")
print(result)

# 列名的数量比数据的行数少一个，所以pandas.read_csv推断第一列作为DataFrame的索引

# 用skiprows跳过下面文件的第一行，第三行和第四行
# pd.read_csv("examples/ex4.csv", skiprows=[0,3])
pd.read_csv("examples/ex4.csv", skiprows=[0,2,3])

# skiprows只填整数n，代表跳过文件的前n行
#  只想跳过第一行，不能写skiprows=0，   应该写skiprows=[0]

# 缺失数据：空字符串，NA,NULL
result = pd.read_csv("examples/ex5.csv")
result
# NA， 空字符串是缺失值

# pandas将缺失数据输出为NaN，result中有两个缺失值
# pd.isna(data_frame)
pd.isna(result)

#   "", NA当成缺失值  NULL -<
# # na_values参数可以接收字符串序列，read_csv会把这些字符串当成缺失值
result = pd.read_csv("examples/ex5.csv", na_values=["无", "NULL", "?"])
result
#
# # keep_default_na=False 参数可以让默认被解析成NaN 的值失效
result2 = pd.read_csv("examples/ex5.csv", keep_default_na=False)
result2
result2.loc[1,"c"]
result2.isna()

# # keep_default_na=False后，可以继续指定被解析成缺失值的字符串
result3 = pd.read_csv("examples/ex5.csv", keep_default_na=False, na_values=["NA"])
result3
result3.isna()
#
# # na_values参数可以指定为字典，字典的键对应表文件的列，可以针对各个列指定不同缺失值标识
sentinels = {"message": ["foo", "NA"], "something": ["two"]}
pd.read_csv("examples/ex5.csv", na_values=sentinels, keep_default_na=False)

## 逐块读取文本文件：处理大文件或找出正确的参数集以处理大文件时，可能只想读取文件的一小部分或逐块对文件进行迭代

# print(pd.options.display.max_rows)  # pandas块的配置选项： pandas有关的数据 最多显示多少行
pd.options.display.max_rows = 10 # 设置pandas显示的最多行数，使显示更加紧凑
#
result = pd.read_csv("examples/ex6.csv")  # 读取大文件
# result

#  dataframe  下面有info方法 ：有关这个dataframe的各种信息：列的数据类型，内存占用
# result.info()
#
#  nrows = n；读取n行
# pd.read_csv("examples/ex6.csv", nrows=5) # 读取5行（避免读取整个文件），通过nrows进行指定
#
# # 要逐块读取文件，可以指定chunksize 代表行的数量
#  如果指定了chunksize参数=n， 返回的不是DataFrame
# # pandas.read_csv所返回的TextFileReader对象，根据chunksize 对文件进行逐块迭代
chunker = pd.read_csv("examples/ex6.csv", chunksize=1000)
print(chunker)  # TextFileReader对象
print(type(chunker))
#

# next(chunker)
# next(chunker)
for piece in chunker:
    print(piece)
#
# next(chunker)  # 迭代结束了,这里报错
#
chunker = pd.read_csv("examples/ex6.csv", chunksize=1000)

# 一个更有用的 迭代处理的例子
# 迭代过程中，对value_counts进行累加
tot = pd.Series([], dtype="int64")  # 初始化    sum=0
for piece in chunker:
    # print(piece["key"].value_counts())
    tot = tot.add(piece["key"].value_counts(), fill_value=0)
#
tot = tot.sort_values(ascending=False)
tot.head(3)
# tot.head()

# dataframe -> 文本文件

# 将数据写入文本格式
# 数据也可以 输出为分隔符格式的文本，
data = pd.read_csv("examples/ex5.csv")
data

# dataframe的方法 to_csv(文件路径)
#  data.to_csv(某个路径) data 转成csv文件 存到这个路径里面
data.to_csv("examples/out.csv")  # 数据导出到 examples/out.csv里，逗号分隔
#
# 用其他分隔符，直接输出到标准输出（sys.stdout), 标准输出也是文件
#  to_csv(文件的形式：系统的文件路径 / 任意能被当成文件的东西）
#   to_csv 文件路径：    dataframe输出到文件路径里
#   to_csv sys.stdout： dataframe输出到 标准输出（控制台）
import sys
data.to_csv(sys.stdout, sep="|")
#
# # 缺失值在输出结果中默认会表示为空字符串，可以表示为其他标记值
# data.to_csv, na_rep="NULL“ 输出用什么内容替代NaN
data.to_csv(sys.stdout, na_rep="无")
#
# # 输出默认有行和列的标签，它们也都可以被禁用：
#    输出到文件里，禁用data的行标签：to_csv(index=False)
data.to_csv(sys.stdout, index=False)
print("*" * 100)
data.to_csv(sys.stdout, index=False, header=False)
#
# 还可以只输出列的一部分，并以指定的顺序排列
data.to_csv(sys.stdout, index=False, columns=["c", "b", "a"])

# 使用 StringIO 把字符串当“文件”读入 pandas
# StringIO：把字符串变成“伪文件", 方便所有只认文件对象的函数直接读取和写入
from io import StringIO

csv_text = """col1,col2
A,1
B,2
"""

# 三个引号：python的字符串
#    三个引号的好处：可以在引号内部任意换行

#  read_csv, usecols=[列的名字]
#    usecols: 决定读出来的dataframe要哪几列
buffer = StringIO(csv_text)          # 创建内存文件对象,  直接模拟 读文件内容 到内存里的过程
df = pd.read_csv(buffer, usecols=["x"], names=["x","y"], skiprows=[0])             # 像读文件一样读取； 文件 -> 读进内存里，按字节形式存储 （buffer：字节缓冲区；文件在内存里的表现形式）
#  跳过第一行，指定列名是x y， 只要x列

buffer = StringIO(csv_text)
df = pd.read_csv(buffer)
print(df)

# pandas 读csv 默认类型推断
#    - 整列都是数字 → 自动变 int/float
#    - 整列都是数字和空值 -> 自动变float
#    - 混有文本 → 退化为 object
#    - True/true/TRUE false/False/FALSE -> 布尔类型
csv_text = """num,txt,mixed,布尔值
1,hello,3,TRUE
2,world,"?",false
3,foo,5,True
"""
df = pd.read_csv(StringIO(csv_text))
print(df.dtypes)  # 得到每一列数据类型的series
# df = pd.read_csv(StringIO(csv_text), na_values=["?"])
# print(df.dtypes)

# 自定义数据类型转换
csv_txt = StringIO("""
id,price,on_sale
001,12.50,TRUE
002,8.99,FALSE
003,5.33,yes
004,,no
""")

# read_csv
#  dtype 字典： 键是列的名字，值是这一列要被解析成的数据类型（用字符串表示的数据类型）
#  converters 字典： 键是列的名字，值是一个函数本身， 对某列的数据自定义转换；转换：列对应的函数

df = pd.read_csv(
    csv_txt,
    # 显式指定列类型
    dtype={
        "id": "string",        #  强制为字符串（001 不会被转成 1）
        "price": "float32"     # 直接转为 32 位浮点
     },
    converters={
        "on_sale": lambda x: str(x).strip().lower() in ["true", "yes", "1"]    # 把这一列所有值 都转成True/False  ；转换规则：数据小写是是不是true/yes/1
    },
    na_values=["", "na", "none"]
)
print(df)
print(df.dtypes)

print("*" * 100)
# 日期解析
csv_text = StringIO("""date,value
2024-12-01,100
2024-12-02,110
2024-12-03,95
""")

df = pd.read_csv(csv_text)
print(df.dtypes)
print(df)
print("-" * 100)
#

csv_text = StringIO("""date,value
2024-12-01,100
2024-12-02,110
2024-12-03,95
""")

# parse_dates= 列表， 列表里装的是列名，告诉pandas 哪几列要被当成datetime格式处理 不是普通字符串
# 与parse_dates配合的另一个关键字参数 date_format=

# df = pd.read_csv(
#     csv_text,
#     parse_dates=["date"],            # 哪列要变 datetime64
#     date_format="%Y-%m-%d"           # 明确告诉日期格式：年-月-日
# )

df = pd.read_csv(
    csv_text,
    parse_dates=["date"]           # 哪列要变 datetime64
)
print(df.dtypes)
print(df)

```

总结
 已用参数总结：
 \- filepath_or_buffer: 表示文件系统位置，URL，或者是文件转入内存后的字节
 \- sep： 用于对行中各字段进行拆分的字符序列 或 正则表达式
 \- header: 用作列名的行号，默认为0（第一行），如果没有表头则为None
 \- index_col: 用作结果中行索引的列编号或列名，可以是单个名称或数字，也可以是名称或数字组成的列表，也可以用于层次化索引
 \- names：指定列名的列表
 \- usecols: 指定读出来的dataframe里要哪些列
 \- skiprows: 从文件开始处算起，需要忽略的行数或需要跳过的行数列表 （从0开始）
 \- na_values: 一组被解析成NaN的值序列，将其添加到默认列表，如果keep_default_na=False, 没有默认列表，不添加
 \- keep_default_na: 是否使用默认的NA值列表（默认为True)
 \- dtype：指定列的数据类型映射，如dtype={"colA":"int64", "colB":"float32"}, 若类型不匹配会报错
 \- converters: 指定 列名->函数的字典，对该列每个单元格执行函数再解析
 \- parse_dates: 指定列名或者列名的列表，告诉pandas这些列需要解析为datetime64
 \- date_format: 配合parse_dates使用，当parse_dates指定列满足统一格式时，告诉pandas按什么日期格式字符串解析
 \- encoding: 文本编码格式。utf-8表示用UTF-8编码的文本



```python
# 文件内容：

file_contents = \
"""# 订单数据（2023年）
订单编号,客户ID,下单日期,金额,状态
1001,C001,2023/01/15,1200.5,已完成
1002,C002,2023/01/18,,取消
1003,C003,2023/01/22,980.0,已完成
1004,C004,2023/01/25,875.5,进行中
1005,C005,2023/01/28,NaN,取消
"""

# 1. 复习python写文件，把这个file_contents写到 examples/orders_2023.csv,
# with open("examples/orders_2023.csv", 'w', encoding='utf-8') as f:
#     f.write(file_contents)
# 2 -- 6: 使用pd.read_csv 读取examples/orders_2023.csv
#    2. 读的时候，跳过第一行注释
file_path = "examples/orders_2023.csv"
# pd.read_csv(file_path, skiprows=[0])
# pd.read_csv(file_path, skiprows=2)  跳过前2行
#    3. 将"订单编号列“作为行索引
# pd.read_csv(file_path, index_col="订单编号", skiprows=[0])
#    4. 指定“金额”列为 float32 类型。
df = pd.read_csv(file_path, skiprows=[0], dtype={"金额": "float32"})
print(df.dtypes)
#    5. 把状态为“NaN”或“取消”的金额统一识别为缺失值（NaN）
df = pd.read_csv(file_path, skiprows=[0], na_values=["取消", "NaN"])
print(df)
#    6. 跳过表里原本的列，指定列名为 ["OrderID", "CustomerID", "Date", "Amount", "Status"]
df = pd.read_csv(file_path, skiprows=[0,1], header=None, names=["OrderID", "CustomerID", "Date", "Amount", "Status"])
df

```

### JSON数据

\- JSON已经成为通过HTTP请求在Web浏览器和其他应用程序之间发送数据的标准格式之一
\- 比表格型文本格式（如CSV）灵活的数据格式

sys模块下的stdout是直接显示在的，而不是去创建文件

```python
import pandas as pd
import sys
obj = """
{"name": "Wes",
 "cities_lived": ["Akron", "Nashville", "New York", "San Francisco"],
 "pet":null,
 "siblings":[{"name": "Scott", "age":34, "hobbies":["guitars","soccer"]},
             {"name": "Katie", "age":42, "hobbies":["diving", "art"]}]
}
"""

# 除了空值null和一些其他的细微差别（列表某尾不允许存在多余的逗号），JSON非常接近python代码
#  JSON的基本类型：字典，列表，字符串，数值，布尔值以及空值
# [1,2,3,]
# 对象中所有的键必须是字符串

import json
result = json.loads(obj)  # json.loads: json字符串 -> python的数据
result
#
asjson = json.dumps(result)  # json.dumps: python数据 -> json字符串
asjson
#
pd.DataFrame(result["siblings"])
#

#  pd.read_json(文件路径.json)  -> DataFrame/Series
# # pd.read_json可以自动将JSON数据集 转换为指定形式的Series或DataFrame
data = pd.read_json("examples/example.json")
data
# dataframe 转回成json文件
#  data_frame.to_json(文件路径/文件对象)
data
data.to_json(sys.stdout)  # {"a":{"0":1,"1":4,"2":7},"b":{"0":2,"1":5,"2":8},"c":{"0":3,"1":6,"2":9}}
# data.to_json(sys.stdout, orient="records") # orient="records" 按行展示json字符串  [{"a":1,"b":2,"c":3},{"a":4,"b":5,"c":6},{"a":7,"b":8,"c":9}]

```

### 读取Microsoft Excel文件 -- 二进制文件

```python
# 读取Microsoft Excel文件： xlsx后缀名
# pd.ExcelFile类 或 pd.read_excel函数
# 使用上述功能，依赖xlrd openpyxl包

# conda install openpyxl xlrd
# pip isntall openpyxl xlrd
# pd.ExcelFile, 传入xls 或 xlsx文件的路径
xlsx = pd.ExcelFile("examples/ex1.xlsx")
type(xlsx) # pandas.io.excel._base.ExcelFile

xlsx.sheet_names # 得到excel文件里所有的表名 列表

# 利用ExcelFile对象的parse方法，将工作表中的数据读取成pandas的DataFrame
xlsx.parse(sheet_name="Sheet1")

#
# # 这张Excel表有一个索引列，可以用参数index_col指定索引列
xlsx.parse(sheet_name="Sheet1", index_col=0)  # 在read_csv里 index_col也可以是序号
# xlsx.close()

# ExcelFile读取多张工作表，也可以将文件名传给pandas.read_excel

# pd.read_excel同时读取文件里的所有工作簿（sheet); 指定sheet_name=None
#  返回的是字典 ：键：工作簿的名字 值：工作簿转成DataFrame
# frame_map = pd.read_excel("examples/ex1.xlsx", sheet_name=None)
# print(type(frame_map))
# print(frame_map)
# frame_map["Sheet1"]

frame = pd.read_excel("examples/ex1.xlsx")
frame

frame = pd.read_excel("examples/ex1.xlsx", sheet_name="Sheet1", index_col=0)
frame

# to_csv, to_json, to_excel
#
# # dataframe直接转成excel文件，传递文件路径
frame.to_excel("examples/ex2_test.xlsx", index=False) # index=False 不要行标签
#
# 打开一个 Excel 文件写入通道 （pd.ExcelWriter→ 把 DataFrame 写进去 → 自动保存并关闭
# pd.ExcelWriter: 专门用来写入excel的一个类 （可以看成 open(filename, 'w')
with pd.ExcelWriter("examples/ex3_test.xlsx") as writer:
    frame.to_excel(writer, sheet_name="工作簿1")  # to_excel可以不是文件路径，现在是一个 Excel文件写入对象

# writer自动关闭 不用手动写writer.close()

# 随堂练习
# 学校里老师派一个任务 ：给了班级里所有学生 各个科目的考试成绩表，要你根据这个表，生成个新的xlsx， 新xlsx记录每个科目的最高分,中位数（median)和平均分，完成这个任务
# 表格：examples/grades_cn.xlsx

# 生成的表格：
#       math english physics
# 最高分  x    x       x
# 中位数  x    x       x
# 平均分  x    x       x
# 1. 读excel  pandas读 -> DataFrame
scores_df = pd.read_excel("examples/grades_cn.xlsx", sheet_name="Scores", index_col="student_id")
# 2. DataFrame 生成每个科目的平均分 最高分 中位数 （dataframe里的方法）  -> DataFrame
#  观察： 对分数表的每一列 做一样的处理 得到一个   pd.Series(最高分:x; 中位数：x，平均分：x)
pure_scores_df = scores_df[["math","english","physics"]]
result_df = pure_scores_df.apply(lambda col: pd.Series({"最高分":col.max(), "中位数": col.median(), "平均分": col.mean()}))
pd.DataFrame
# 3. 把第二步的DataFrame给写回成新的excel
result_df.to_excel("examples/班级分数统计信息.xlsx")


# 随堂练习扩展，怎么给原xlsx追加新的sheet，而不破坏原来的xlsx；不生成新的xlsx
# pd.ExcelWriter参数：
#   mode="a"   追加写入
#   engine="openpyxl"  openpyxl 追加时会保留已有单元格格式、公式、图表、筛选等；  其他引擎做不到。
#  但建议一定先备份，再去对原xlsx做修改
df_test = pd.DataFrame(np.arange(9).reshape((3,3)))
df_test
with pd.ExcelWriter("examples/grades_cn.xlsx", engine="openpyxl", mode="a") as writer:
    df_test.to_excel(writer, sheet_name="测试追加写入原表格", index=False)  # 在原本的sheet中添加

```

## 与数据库交互

Python自带了与sql交互的方法，import sqlite3 即可使用

操作步骤：

1. 首先导入sqlites3的库

2. 与数据库建立连接并赋值给一个变量，以conn为例语法为conn=sqlites3.connect(数据库的名字.db)

3. 将操作数据库连接的句柄赋值给新的变量，cur=conn.cuesor()

4. 通过.execute函数传入数据库的操作语句实现对数据库的操作，语法为cur.execute(数据库操作语言)

   另注：在建立表时可以用executemany详见下面代码块41行

```python
import sqlite3  # python自带的 支持数据库操作 （本地数据库）

# 建立数据库连接
conn = sqlite3.connect("steam.db")  #　连接steam.db 不存在的话会创建　steam.db
cur = conn.cursor()  # 操作数据库连接的句柄 （操作数据库连接的行为）

# 数据库内的操作 是专门的语言（编程语言）去操作： 1. 定义表  表整体的操作（删表 建表） 定义表和表之间的关系（不同表之间 去关联它们的列），  2. 对数据表 增删改查   SQL

 #             志愿者的表：志愿者这个人的各种信息，  这个人的身份证（主键列），年龄，身份，学校/工作单位
 #              活动表：   活动id，活动名字，活动日期
 #             志愿者参加活动表：   活动id  志愿者id  参加的时间

#   数据库之间实现细节有差异，但是都可以用SQL语言进行操作

# 删除旧表（可选）
cur.execute("DROP TABLE IF EXISTS games")

# 创建表

# PRIMARY KEY: 对表里的每一行数据，做唯一标识
# AUTO INCREMENT: 自增： 给表里插入数据的时候，没有指定id，自动加1
cur.execute("""
CREATE TABLE games (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    release_year INTEGER,
    price REAL,
    positive INTEGER,
    negative INTEGER
)
""")

# 插入数据
games = [
    ("GTA V", 2015, 29.99, 350000, 50000),
    ("The Witcher 3", 2015, 39.99, 450000, 30000),
    ("Stardew Valley", 2016, 14.99, 250000, 10000),
    ("Cyberpunk 2077", 2020, 59.99, 300000, 150000),
    ("CS:GO", 2012, 0.00, 600000, 100000),
]
cur.executemany("INSERT INTO games (name, release_year, price, positive, negative) VALUES (?, ?, ?, ?, ?)", games)

conn.commit()  # 提交，对数据库的修改 真的写到磁盘里

# 查询：评分最高的三个游戏
# SQL:
# 1. 来自数据库的哪张表：FROM 数据库的表名字
# 2. 过滤：（WHERE ...)  根据列的值 对数据表里每一行数据判断这个列的值满足给定条件，条件为False的话 不取这个数据  （行的筛选）
#         WHERE 表的列名 比较运算  具体数值
# 3. SELECT 列名1，列名2，列名3, 自定的运算结果 AS 自定义的列   （对列的选择）
# 4. 选出来的数据 排序
# 5. 限制选出来数据的数量 （行的数量）


# 数据库里的操作： SELECT * FROM 表的名字  # 选出表里的所有数据 -> 数据库的表的形式展现

query = """
SELECT name, price, positive, negative,
       ROUND(1.0 * positive / (positive + negative), 3) AS rating
FROM games
ORDER BY rating DESC
LIMIT 3
"""

# sqlite模块的  数据库操作对象 去执行sql语句
for row in cur.execute(query):
    print(row)

# pandas可以直接执行sql pd.read_sql(SQL语句的字符串, 连接对象）
#    对连接的数据库，做SQL语句的操作   -> DataFrame
# pd.read_sql(query, conn)

```



小结：从python引入的sqlite3，有几下几个用法：

| sqlite3级别的方法             | .connect()    | "数据库名称.db" | 连接数据库，可以用变量名承接成为具体的数据库实例             |
| ----------------------------- | ------------- | --------------- | ------------------------------------------------------------ |
| 实例方法(conn.)               | cursor()      |                 | 操作数据库连接的句柄 （操作数据库连接的行为）,也可以用变量名承接 |
|                               | commit        |                 | 提交，将对数据库的修改写入磁盘                               |
| 实例方法(conn.)下的操作(cur.) | execute()     | str             | 输入的是由数据库语言所组成的字符串                           |
|                               | executemany() | str             | 输入的是由数据库语言所组成的字符串                           |
|                               |               | list            | 列表内容是在str中所需的内容，在str中需要的内容用?表示，而列表由多个填写内容所构成的元组组成 |



sqlalchemy与pandas的read_sql方法结合：

1. 导入sqlalchemy库  ->import sqlalchemy as sqla

2. 建立与数据库的连接，db = sqla.creeate_engine("sqlite:///数据库名字.db")

3. 使用pd.read_sql读取数据库的信息，语法为pd.read_sql()

   ​	sql：select * from 表名 ->数据库的读取语言

   ​	con：与数据库建立连接的变量名，例如db

   ​	index_col：指定某一列作为行索引，这样就可以读取到一个dataframe

```
# SQLAlchemy是python SQL工具集，抽象地去除了SQL数据库之间的许多常见差异
# sqlite3， mysql， oracle， postgresql：  SQL语句操作

#  通过SQLAlchemy中间层，可以和很多种数据库做连接
#  SQL语句相似

# pandas的read_sql函数，可以从通用的SQLAlchemy连接轻松读取数据

# conda install sqlalchemy
# pip install sqlalchemy

# 用SQLAlchemy连接同样的SQLite数据库，并从之前创建的表里读取数据
import sqlalchemy as sqla

db = sqla.create_engine('sqlite:///steam.db')  # 数据库的对象 / 数据库的连接对象

# pd.read_sql("SELECT * FROM games", db)  # ->DataFrame
pd.read_sql("SELECT * FROM games", db, index_col="id")  # 把表里的其中一列，当成 行的索引

# 随堂练习：
# 1. 编写sql语句，sql语句选出价格30元以上的游戏的名字 和 发布年份
sql_query = "SELECT name, release_year FROM games WHERE price > 30"

# 2. 利用pd.read_sql执行这个语句，得出相应的data frame
pd.read_sql(sql_query, conn)

# 3. 编写sql语句，获取完整的table 不对行列做任何过滤筛选
sql_query = "SELECT * FROM games"

# 4. 利用pd.read_sql执行这个语句，得出相应的data frame
df = pd.read_sql(sql_query, conn)
df

# 5. 对上一题的data frame， 用pandas dataframe对象的方法 价格30元以上的游戏的名字 和 发布年份
df.loc[df["price"] > 30, ["name", "release_year"]]

```

## 与Web API交互

许多网站都有一些通过JSON或其他格式提供获取数据的公共API。推荐使用requests包向网站发起请求。
 \- pip install requests / conda install requests

操作步骤：

1. 先导入requests库   可以使用requests库发起一个HTTP GET请求   HTTP请求：GET (读网站数据）/ POST （上传/写数据到网站）      ->import requests
2. 通过requests.get(网址)来获取网站上的信息，并用一个变量接收   ->resp = requests.get(url)
3. 使用requests.get之后，调用raise_for_status检查HTTP的错误->resp.raise_for_status()
4. 接受的信息用.json()方法，可以把响应里的字符串 按json解析 （得到python的数据 列表/字典） ->data = resp.json()

```python
# 获取Github上关于pandas的30个最新问题，使用requests库发起一个HTTP GET请求   HTTP请求：GET (读网站数据）/ POST （上传/写数据到网站）
import requests
url = "https://api.github.com/repos/pandas-dev/pandas/issues"
resp = requests.get(url)

resp.raise_for_status()  # 使用requests.get之后，调用raise_for_status检查HTTP的错误

print(resp)
# 响应对象的json方法会返回一个Python对象，其中包含解析过的JSON数据字典或列表（取决于返回的JSON是什么）
data = resp.json()  # .json: 帮我把响应里的字符串 按json解析 （得到python的数据 列表/字典）
print(type(data))

# print(data)
#
# print("-" * 100)
print(data[0]["title"])

# data中的每个元素都是包含Github问题页上所有数据（不包含评论）的字典。我们可以直接将data传递给pandas.DataFrame,并提取感兴趣的字段(列）
issues = pd.DataFrame(data, columns=["number", "title", "labels", "state"])
issues
```



## HTML: 网络抓取

pandas有一个函数pd.read_html，自动将HTML文件中的表格解析为DataFrame对象
 \- 依赖的库：pip install lxml beautifulsoup4 html5lib
 \- conda install lxml beautifulsoup4 html5lib

pandas有对应的.read_html()函数，传入的时网址，在默认条件下它会搜索，尝试解析<table>标签内的所有表格数据，结果是DataFrame对象的列表

```python
# 默认条件下它会搜索，尝试解析<table>标签内的所有表格数据，结果是DataFrame对象的列表
tables = pd.read_html("examples/fdic_failed_bank_list.html")
print(type(tables))  # 列表 pd.read_html(html文件（本地的/网址））  -> 列表：DataFrame的列表
len(tables)

failures = tables[0]

failures.head()

# 展示对dataframe做日期处理：  pd.to_datetime(series)  # series的数据 转成 pandas的日期类型  转完后得到新的series，转成日期类型 可以利用日期内的方法操作
failures["Closing Date"]
close_timestamps = pd.to_datetime(failures["Closing Date"])
close_timestamps.dt.year # 因为是日期的series  有dt（date time）属性 .dt获取的就是series里的日期数据操作对象

close_timestamps.dt.year.value_counts()

```

## 数据清洗

### 处理缺失数据

 \- dropna : 根据各标签的值中是否存在缺失数据，对轴标签进行过滤，可通过阈值调节对缺失值的容忍度
 \- fillna/bfill/ffill: 用指定值或插值方法（如ffill或bfill)填充缺失数据
 \- isnull/isna: 对于非NA值返回False, 对于NA值返回True
 \- notnull/notna: isna的否定式，对于非NA值返回True, 对于NA值返回False

```python
# 1. 处理缺失数据

float_data = pd.Series([1.2, -3.5, np.nan, 0])
float_data
float_data.isna()

# na Not Available

# python的None作为缺失值
string_data = pd.Series(["apple", "pen", None, "applepen"])
string_data
string_data.isna()
#
# # 过滤缺失数据
# 通过isna() notna()过滤
string_data.loc[string_data.notna()]
#
# # 更实用：dropna  series.dropna()
data = pd.Series([1, np.nan, 3.5, np.nan, 7])
# print(data.dropna()) # 对于Series, dropna返回一个仅含非空数据和索引值的Series
# print(data)
# print(data.dropna())
# print(data.dropna(ignore_index=True))   # ignore_index=True 去掉缺失值后， 忽略原来的索引，生成的新的索引（默认 0---n-1）
# print(data.dropna(inplace=True))  # inplace 是否要在原地修改
# print(data)
#
# 对于DataFrame对象，有多种方式去除缺失值。可能希望 全部的行/列 NA 或含有部分NA的行和列。
# dropna默认丢弃任何含有缺失值的行
data = pd.DataFrame([[1., 6.5, 3.], [1., np.nan, np.nan], [np.nan, np.nan, np.nan],
                     [np.nan, 6.5, 3.]])
# print(data)
data.dropna()  # data frame变量.dropna() # dropna默认丢弃任何含有缺失值的行


# # 传入how="all"将只丢弃全为NA的那些行; 默认how="any" 只要 包含NA的行都丢弃
data.dropna(how="any")
data.dropna(how="all")
# # 注意data没有变化，fillna也是如此， 因为默认的关键字参数 inplace=False
# print(data)


# # 想要用这种方式丢弃列，需要传入axis="columns"

data[4] = np.nan
# print(data)
# data.dropna(axis="columns")  #  想要用这种方式丢弃列，需要传入axis="columns"
# data.dropna(axis="columns", how="all")


# # thresh=N   threshold （阈值）=N； 至少有N个 非缺失值的列 / 行保留
# # 至少有N个非NaN值的行/列保留
df = pd.DataFrame(np.random.standard_normal((7, 3)))
df
df.iloc[:4, 1] = np.nan
df
df.iloc[:2, 2] = np.nan
df
#
df.dropna() # 保留没有缺失的行 df.dropna(axis="index", how="any")
print(df)
df.dropna(thresh=3) # 至少有3个非缺失值，可以保留   <3个 非缺失值，删掉这行
df.dropna(thresh=2) # 至少有2个非缺失值，可以保留   <2个 非缺失值，删掉这行

```

==dataframe dropna总结==
 ==\# axis=0/index 表示对行进行 dropna==
 ==\# axis=1/columns 表示对列进行dropna==
 ==\# thresh=N，表示至少有N个非NaN值才保留，否则删除这行/列==
 ==\# ignore_index=True/False; True不保留原来data的索引标签==
 ==\# inplace=True/False; False生成新的数据==
 ==\# how="any"/"all"; "all": 整列/整行全部是缺失值，才去删除； “any": 只要包含缺失值，就删除==

```python
## 填充缺失数据
df
#  data_frame.fillna(替代值）； 缺失值填成 替代值，生成新的dataframe
df.fillna(0)

# 对于data frame来说， 调用fillna时使用字典，实现对不同的列填充不同的值； 键：列的标签；值：填充值
# df.fillna({1:0.5, 2:0})

# df.fillna(method="bfill")  # FutureWarning: DataFrame.fillna with 'method' is deprecated and will raise in a future version. Use obj.ffill() or obj.bfill() instead

#
# ffill/bfill方法, 实现前/后填充
np.random.seed(1)
df = pd.DataFrame(np.random.standard_normal((6,3)))
df.iloc[2:, 1] = np.nan
df.iloc[4:, 2] = np.nan
# df

df.ffill()  # data_frame.ffill()  # 默认会看这一列 前一行的值，来填充；前向填充

df.iloc[5, 1] = 3.14159
print(df)
df.bfill()  # data_frame.bfill() #  # 默认会看这一列 后一行的值，来填充；后向填充
#
df.bfill(limit=2)  # 指定填充的最大范围
df.ffill(axis="columns")  # 沿着列（跨列） 向前填充   看这一行 前一列的值，来填充，前向填充
df.ffill(axis="index") # 这个是默认的轴向
#
# fillna本来可以传method 来指定前向后向填充，但bfill和ffill替代了这个方式，所以fillna现在就关注填充值是什么
# 填充的轴向，限制（limit) 靠bfill ffill去指定
#
# fillna可以传入Series的平均值或中位数以替换缺失值，这种填充策略使用得比较频繁
data = pd.Series([1., np.nan, 3.5, np.nan, 7])
data.fillna(data.mean())
# 随堂练习 · 智能手环一周健康数据清洗
# 熟悉dropna / fillna / ffill / bfill / isna / notna

#  模拟一周数据
# steps:  步数（千步）
# kcal:   消耗卡路里（百卡）
# sleep:  睡眠时长（小时）

data = {
    "day"  : ["Mon","Tue","Wed","Thu","Fri","Sat","Sun"],
    "steps": [8.2,  9.1,  np.nan, 7.5, 8.8,  np.nan, 10.0],
    "kcal" : [2.3,  np.nan,2.6,   2.1, np.nan,3.0,   3.2],
    "sleep": [7.0,  6.5,   6.0,   np.nan,7.8,  8.0,   np.nan]
}
data = pd.DataFrame(data).set_index("day")
print("原始数据：\n", df, "\n")

# 任务 1  缺失值检测
"""
1.a  用 isna 统计每列缺失值数量

1.b  找出“没有缺失值的行”的日期  (拿到没有缺失值行的索引对象）
     提示：用 notna 构造布尔 DataFrame，再用 .all(axis=1)
"""
### TODO 1.a
# data frame isna方法， 按列series 去调用.isna()
# 最终得到的是布尔值的DataFramme， True是缺失，False不是
# print(data.isna().sum())

### TODO 1.b
print(df[df.notna().all(axis="columns")].index)


# 任务 2  删除缺失 & 阈值容忍
"""
2.a  删除“任何含有NaN的列”
2.b  保留 ≥ 2 个非缺失值的行, 提示：删除缺失值方法的thresh参数
"""
### TODO 2.a
df.dropna(axis="columns")
print(df.dropna(axis="columns", how="any"))


### TODO 2.b
print(data.dropna(thresh=2))


# 任务 3  缺失值填补
"""
3.a  对 steps 列用向前填充 ffill
3.b  对 kcal 列用列均值填充
3.c  对 sleep 列用向后填充 bfill，再对剩余 NaN 使用 0 替代
最终将结果合并到 new_df 并打印
"""
### TODO 3
new_df = df.copy()
new_df["steps"] = new_df["steps"].ffill()
new_df["kcal"]  = new_df["kcal"].mean()
new_df["sleep"] = new_df["sleep"].bfill().fillna(0)
print(new_df)


# 任务 4  一步完成多列不同策略填充
"""
使用 DataFrame.fillna(dict)：
   - steps: 前向填充
   - kcal : 均值
   - sleep: 0
验证结果与任务 3 相同
提示：fillna可以传字典，字典的键是列名，字典的值是 series/数字（表示这一列缺失值要填充成什么）
{
    "steps": df["steps"].ffill(),
    "kcal" : df["kcal"].mean(),
    "sleep": 0
}
"""
### TODO 4

# dataframe 的fillna可以传字典；  列名 对应 这一列缺失值要填成什么（可以是series，可以是数字）
df.fillna({
    "steps": df["steps"].ffill(),
    "kcal" : df["kcal"].mean(),
    "sleep": 0
})

```

==ffill/bfill(前项填充/后项填充)：==

==\- limit = 数字，表示填充的最大次数==

==\- axis = "index"/"columns",表示填充的轴向，默认为index==

==df.fillna()==

==\- 直接填数字或者有返回值的函数，例如mean，或者median，这样就会用数字填充，填充的轴向，限制（limit) 靠bfill ffill去指定==

==\- method="ffill"/"bfill" 同ffill和bfill==

==\- fillna可以传字典，字典的键是列名，字典的值是 series/数字（表示这一列缺失值要填充成什么）==



### 数据转换：去重，转换，替换

\- duplicated(英文“一摸一样的”)：标记重复出现 

​	 keep='first' (默认): 除了第一个，后面重复的都标记为True

​	keep='last': 除了最后一个，前面重复的都标记为True

​	keep=False: 所有重复值都标记为True

\- drop_duplicates: 只保留了duplicated()返回结果为False 的数据

​	subet=[列表]/列名，可以根据某一列或某几列进行去重

​	keep=  同上

\- map: 利用函数或字典映射进行数据转换

​	Series的map方法除了可以接收一个函数，也可以接收一个含有映射关系的字典型对象 字典的键->值 来实现键值转换，也可以传入函数

\- replace：map的简单版

​	有两种方式：第一种，（被替换的单个值/多个被替换值所组成的列表，替换的值或者列表），多值替换可以广播

​	第二种，（{被替换值1：替换值1，被替换值2：替换值2，......}）

```python
# 数据转换
# 处理缺失数据之外，另一类重要的操作：过滤，清理和转换

# Series的duplicated方法： 标记每个元素是否重复出现 （即之前已经出现过)
s = pd.Series(['a','b','a','c','b','d'])
# print(s)
# print(s.duplicated())
# print(s.duplicated(keep='first'))  # duplicated方法有keep的关键字参数


# # 参数说明：
# # keep='first' (默认): 除了第一个，后面重复的都标记为True
# # keep='last': 除了最后一个，前面重复的都标记为True
# # keep=False: 所有重复值都标记为True
# # 返回一个布尔型Series，表示每个元素是否是"重复值"

data = pd.DataFrame({"k1": ["one", "two"] * 3 + ["two"], "k2": [1,1,2,3,3,4,4]})
data
#
# DataFrame的duplicated方法返回一个布尔型Series，表示各行是不是重复行, 机制和Series类似
data.duplicated()
data.duplicated(keep=False)
#

# data.loc[~data.duplicated()]
# drop_duplicates: 返回一个DataFrame，  其中只保留了duplicated()返回结果为False 的行
data.drop_duplicates()
data.drop_duplicates(keep=False) #  data.loc[~data.duplicated(keep=False)]
data.drop_duplicates(keep='last')
#
# # data frame的drop_duplicated和duplicated方法默认会判断全部列，可以指定部分列判断是否重复。
# # 假设希望只根据"k1"列过滤重复项：
data["v1"] = range(7)  # 0 1 2 3 ... 6  # list(range(7))
data
data.drop_duplicates(subset=["k1"])
#
# drop_duplicates默认保留第一个出现的， 传入keep="last"则保留最后一个
data.drop_duplicates(subset=["k1", "k2"], keep="last")
#
# 传入keep=False， 丢掉全部重复过的
data.drop_duplicates(["k1", "k2"], keep=False)

# 注意： drop_duplicates是生成新的data frame
data


# 利用函数或映射(字典）进行数据转换
# 根据某个数组，Series或DataFrame列中的值来实现转换工作

data = pd.DataFrame({
    "食物": ["培根", "手撕猪肉", "培根", "熏牛肉", "咸牛肉", "培根", "熏牛肉", "蜜汁火腿", "烟熏三文鱼"],
    "斤": [4, 3, 12, 6, 7.5, 8, 3, 5, 6]
})

data
meat_to_animal = {
    "培根": "猪",
    "手撕猪肉": "猪",
    "熏牛肉": "牛",
    "咸牛肉": "牛",
    "蜜汁火腿": "猪",
    "烟熏三文鱼": "三文鱼"
}

# # Series的map方法除了可以接收一个函数，也可以接收一个含有映射关系的字典型对象 字典的键->值 来实现键值转换
data["食物"].map(meat_to_animal)
data["动物"] = data["食物"].map(meat_to_animal)
data
#
# 也可以传入函数
data["食物"].map(lambda food: meat_to_animal[food])
# map应用在元素级转换 以及 其他的数据清洗工作

## 替换值
# fillna方法填充缺失数据可以看作值替换的一种特殊情况
# map 修改对象的具体数据
# replace：map更简单的方式

data = pd.Series([1., -999., 2., -999., -1000., 3.])
data
# 把-999替换成缺失数据
# seris变量.replace(原来的值, 新的值)
data.replace(-999, np.nan)  # 生成了一个新的dataframe
# print(data)

#  一次性替换多个值，可以传入一个列表以及一个替换值
# seris变量.replace(原来的值的列表/序列, 新的值)
data.replace([-999, -1000], np.nan)
#
# 每个值可以有不同的替换值，传递一个替换列表
# seris变量.replace(原来的值的列表/序列, 新的值的列表)
data.replace([-999, -1000], [np.nan, 0])
#
# 传入的替换也可以是个字典
data.replace({-999:np.nan, -1000:0})

# 模拟一份学生花名册
df = pd.DataFrame({
    "stu_id": [1001,1002,1003,1002,1004,1005,1005],
    "name"  : ["张三","李四","王五","李四","赵六","钱七","钱七"],
    "major" : ["CS","EE","ME","EE","CS","cs","ME"],
    "grade" : ["A","B","C","B","A","A","B"]
})
print("原始数据:\n", df)

# TODO 1  使用 duplicated 标记出完全重复的行
# dup_flags = df.duplicated()
# print(dup_flags)

# TODO 2  删除完全重复的行，只保留第一次出现
# 提示：为了避免后面的警告，对删除重复行的dataframe来一次复制 （copy方法）
# df_unique = df.drop_duplicates().copy()
# print(df_unique)

# TODO 3  用 map 将 grade 列映射为 GPA 分数 (A=4,B=3,C=2), 并添加到一列 GP到df_unique, 来存分数
df_unique["GA"] = df_unique["grade"].map({"A":4, "B":3, "C":2}) # df.loc[~df.duplicates()]["GA"] = df_unique["grade"].map({"A":4, "B":3, "C":2})
print(df_unique)

# TODO 4  把 major 列大小写混杂的 "cs" 统一改写为 "CS"（用 replace）
df_unique["major"] = df_unique["major"].replace("cs", "CS")

print("\n清洗后:\n", df_unique)

```

### 重命名轴索引

 \- 索引对象的map方法：通过函数或字典映射进行转换，从而得到一个新的索引对象
 \- rename: 对列索引或行索引重命名，data frame内的方法

==另注：虽然rename里有name但是行名和列名并不能通过这个修改，这个修改的时行列索引的名字==

```python
# 重命名轴索引
# 与Series中的值一样，轴标签也可以通过函数或映射进行转换，从而得到一个新的不同标签的对象
data = pd.DataFrame(np.arange(12).reshape((3,4)), index=["Ohio", "Colorado", "New York"],
                    columns=["one", "two", "three", "four"])
data

def transform(x):
    return x[:4].upper()

data.index.map(transform) # 生成新的索引对象
data.index
# data.index.map(transform)
# data.index
#
data.index = data.index.map(transform)
data

#
# # 如果要创建数据集转换后的版本，并且不修改原始数据，比较使用的方法是rename
data = pd.DataFrame(np.arange(12).reshape((3,4)), index=["Ohio", "Colorado", "New York"],
                   columns=["one", "two", "three", "four"])

data

# data_frame.rename(index=..., columns=...)

# 类的实例方法和类方法，属性   存在 类.__dict__
str.__dict__  # 字符串有什么方法

# 字符串的方法： str.方法名

str.split("asdasdas asd")  # "asdasdas asd".split()
# str.split("asdasdas, asdsa", ",")  # "asdasdas, asdsa", ".split(",")

# 行索引标签，去执行 字符串.title();     对于列索引标签： 执行 字符串.upper()
data.rename(index=str.title, columns=str.upper)  # 生成了新的dataframe
#
# # rename可以集合字典型对象实现对部分轴标签更新：
print(data)
data.rename(index={"Ohio": "INDIANA"}, columns={"three":"三"})
#
# data
# # 使用rename，则无须手动复制DataFrame  并给index和columns属性赋新值

data.index = ["x", "y", "z"]
data

```

### 离散化和分箱

 \- 为了便于分析，连续数据常常被离散化或拆分为"箱子"， 各个数据处于某个箱子中
 \- pd.cut # 根据传入的区间范围进行分组，[a,b,c,d,e] -> (a,b],(b,c],(c,d],(d,e]
 \- pd.qcut # 根据数据构成的百分比进行分组 关键词 q = [a,b,c,d,e]  a+b+c+d+e = 1表示series通过从大到小排列后，有占比多少的数据区间，依然可以传入单个整数，表示等分

```python
## 离散化和分箱
# 为了便于分析，连续数据常常被离散化或拆分为"箱子". 假设有一组人群年龄数据，希望可以划分为不同年龄类别
ages = [20, 22, 25, 27, 21, 23, 37, 31, 61, 45, 41, 32]
bins = [18, 25, 35, 60, np.inf]  # 18-25, 25-35, 35-60, 60以上

# 将这些数据划分为 18到25，26到35，35到60，以及60以上几个组，使用pd.cut(数据，组)
#  pandas模块的方法 ： pd.cut(数据, 组)  -> 分组后的数据

age_categories = pd.cut(ages, bins)
# print(age_categories, type(age_categories))  # 生成 pandas的类别类型 ，这个类别类型长度和原来的数据是一样的，里面的数据不代表具体的年龄，而代表的是分类类型（分箱/离散化）
# # # pandas返回的是一个特殊的分类对象(Categorical)。结果展示了pandas.cut划分得到的组。每个组都有特殊（唯一的）区间值类型，包含下限值和上限值
#
# #  Categorical类型 里面的属性 和方法
# age_categories.codes  # 类别代码：0，1，2 ...
# age_categories.categories  # 区间索引
# age_categories.categories[0] # 区间值  __repr__;  print() __str__
# # type(age_categories.categories[0])  # pandas专门的区间对象
# age_categories.value_counts()   # categorical对象.value_counts() -> series
# age_categories.value_counts().index  # 类别索引，索引现在是区间对象
#
# # 对于区间的字符串表示，圆括号表示边是开放的（不包括），而方括号则表示边是封闭的（包括）
# pd.cut( right=False，表示边是开放的（不包括）
pd.cut(ages, bins, right=False)
#
# pd.cut 可以通过传递一个列表或数组到labels选项，设置自己的分箱/类别名称
group_names = ["少年", "青年", "中年", "老年"]
age_cats2 = pd.cut(ages, bins, labels=group_names)
age_cats2.codes
age_cats2.categories
#
# 如果向pd.cut传入的不是确切的分箱边界，而是分箱的数量，则会根据数据的最小值和最大值计算得到等长的箱。
# pd.cut(数据, 数字)

# # 这个例子将均匀分布的数据分成4组：
data = np.random.uniform(size=20)
data
pd.cut(data, 4, precision=2) # 选项precision=2限定小数点后只有两位   precision的中文含义是“n.精确性，精密度”
#
# pd.qcut函数类似于pd.cut,它可以根据样本分位数对数据进行划分。根据数据的分布情况，pd.cut可能无法使各个分箱中含有相同数量的数据点。而由于pd.qcut使用的是样本分位数（percentile),因此可以得到数据的数量基本相等的分箱：
data = np.random.standard_normal(1000)
data
quartiles = pd.qcut(data, 4, precision=2)  # 0%  - 100%; 分成4分； 0-0.25， 0.25-0.5, 0.5-0.75, 0.75-1
quartiles
type(quartiles) # Categorical类型
# quartiles, type(quartiles)
#
# # 根据分位数 均分了数据  0-25%, 25%-50%, 50%-75%， 75%--100%
quartiles.value_counts()
#
# # pd.cut可以传自定区间，pd.qcut可以传自定义的分位数（0到1之间的数值，包含端点）   0-x1， x1-x2, x2-1  # 像pd.cut一样 传一个自定义的区间 【
# #     0-10%, 10%-50%, 50%-90%, 90%-100%  [0, 0.1, 0.5, 0.9, 1]
pd.qcut(data, [0, 0.1, 0.5, 0.9, 1.])
pd.qcut(data, [0, 0.1, 0.5, 0.9, 1.]).value_counts()

# 直接对series做cut / qcut;
pd.cut(pd.Series(ages), bins)  # 对series做分箱，得到的也是series；数据类型是区间类型

# type(pd.cut(pd.Series(ages), bins))  # 对series做分箱（cut),得到的还是series

```



### 检测和过滤异常值

```python
## 检测和过滤异常值

# 过滤或转换异常值很大程度上就是运用数组运算。
data = pd.DataFrame(np.random.standard_normal((1000,4)))
data.describe()

# # 假设想要找出某列中绝对值大小超过3的值  （偏离 平均值 3倍标准差的数）
col = data[2]
col.loc[col.abs() > 3]

#
# 选出全部含有 超过3或-3的值 的行，可以在布尔型DataFrame中使用any方法
(data.abs() > 3).any(axis="columns")  #在看每一行是不是有异常值
data.loc[(data.abs() > 3).any(axis="columns")]
# # 注意data.abs()>3 外面的括号是必须的，因为是对这个比较结果调用any方法
#
# # 下面的代码可以将绝对值超过3的值 赋值为-3或3，将所有制限制在-3--3之间
np.sign(data)  # data>0 : 1; data<0 : -1; data==0: 0
data[data.abs()>=3] = np.sign(data) * 3  # 1. 布尔型dataframe 索引复制  2. np.sign应用到dataframe上
data.describe()
#
# # 回顾：np.sign(data)可以生成1和-1
# np.sign(data).head()

```

### 置换和随机采样

numpy.random.permutation函数

它是Numpy中用于打乱顺序（洗牌）的函数，它可以对一个序列进行随机排列，

常用于打乱数据的顺序，做数据集随机划分等场景

用法：

​	np.random.permutation(x)

​	如果x是一个整数n，它会返回一个打乱的0到n-1的整数数组，看成一个打乱的位置索引

​	如果x是一个数组，它会返回一个打乱顺序后的新数组（原数组不变）

np.random.permutation函数可以轻松实现对Series 或 DataFrame的行/列的置换（随机重排序）

data_frame.take(整数数组索引）结合np.random.permutation()实现对Series 或 DataFrame的行/列的置换

​		这个与.iloc相似但是.take可以指定轴向

采样：df.sample()实现数据采样

​	n = 数字，表示采样数据的个数

​	axis = "index"/"columns"，表示采样数据按行还是按列

​	replace = False/True，采样是有放回的采样还是无放回的采样

```python
# numpy.random.permutation函数
#  它是Numpy中用于打乱顺序（洗牌）的函数，它可以对一个序列进行随机排列，
#  常用于打乱数据的顺序，做数据集随机划分等场景

# 用法：
# np.random.permutation(x)
# 如果x是一个整数n，它会返回一个打乱的0到n-1的整数数组，看成一个打乱的位置索引
# 如果x是一个数组，它会返回一个打乱顺序后的新数组（原数组不变）

print(np.random.permutation(5))
arr = np.array(['cat', 'dog', 'bird', "fish"])
shuffled = np.random.permutation(arr)

print(shuffled)
print(arr)

# np.random.permutation函数可以轻松实现对Series 或 DataFrame的行/列的置换（随机重排序）
df = pd.DataFrame(np.arange(5*7).reshape((5,7)))

sampler = np.random.permutation(5)
sampler
df.iloc[sampler]

# data_frame.take(整数数组索引）
df.take(sampler)  # 相当于 df.iloc[sampler]
# df.take(sampler, axis="index")
#
# 在take函数中使用 关键字axis="columns"， 可以进行列交换

# 怎么看dataframe 有多大
df.index.size  # 多少行
df.columns.size # 多少列
print(df.info())
column_sampler = np.random.permutation(df.columns.size)  # 注意索引不超过列索引的最大值
df.take(column_sampler, axis="columns")  # df.iloc[:, column_sampler]
#
# # 如果不想用排列的方式选取随机子集，可以在Series和DataFrame上使用sample方法：

df.sample(n=3)  # 随机取三个数据 （随机取了三行数据)
df.sample(n=3, axis="columns")

# 要通过替换的方式生成样本（允许重复选择），可以在sample中传入replace=True
choices = pd.Series([5,7,-1,6,4], index=['A', 'B', 'C', 'D', 'E'])
choices.sample(n=10, replace=True)   # 随机采样返回Series，replace=True 有放回的采样

```

### 计算分类编码 ：pd.get_dummies

pd.get_dummies，读热编码，它会将整列的数据取唯一值，再将这些唯一值摊开成列，与原本的行索引形成一个df，这个df是bool型的数据，其参数为

​	data：数据，传入列表

​	prefix = 字符串：可以给生成的DataFrame的列名加前缀，

多级分类，可以用字符串方法，series.str.get_dummies(分割符)，可以实现多级分类的读热

```python
# 如果DataFrame的某一列含有k个不同的值，则可以派生出一个k列矩阵 或 DataFrame(其值全为True和False）
# pandas有一个pandas.get_dummies函数可以实现该功能

df = pd.DataFrame({"key": ["b", "b", "a", "c", "a", "b"], "data1": range(6)})
df

pd.get_dummies(df["key"]) # pd.get_dummies(列series)
#  one-hot encdoing 读热编码：  # 每个分类; k-维的向量 [0, 0, ,1, 0, ...0] 除了隶属的类别是1，其他分量都是0
pd.get_dummies(df["key"]).astype(np.int8)  # 把True False改成 1 0

#
# get_dummies的prefix参数，可以给生成的DataFrame的列名加前缀
pd.get_dummies(df["key"], prefix="key")

#
# # 如果DataFrame中的某列的分类能同时有多个，需要str.get_dummies的其他关键字参数 计算分类编码
mnames = ["movie_id", "title", "genres"]
movies = pd.read_csv("datasets/movielens/movies.dat", sep="::", header=None, names=mnames, engine="python")
movies.head(10)


# 特殊的Series方法，str.get_dummies处理有多个分类，同时多分类按 | 分隔的场景：
# str.title, str.upper, str.split
dummies = movies["genres"].str.title  # series一个属性，只要series字符串，.str去拿到series的字符串处理对象,有了它可以拿到字符串内的各种方法
dummies = movies["genres"].str.get_dummies("|")  # 字符串表示多分类，通过分隔符来表示不同类别
dummies
dummies.iloc[:10, :6]


# 两张表基于相同的索引 连接起来 ： 把电影分类表的列 和 电影表 本身连起来 （把列直接连起来，基于索引连接）
movies_genre_encode = movies.join(dummies)
movies_genre_encode
movies_genre_encode.iloc[0]
#
# add_prefix方法给列名添加前缀

# dataframe本身 是可以add_predix的， 给列加前缀
dummies.add_prefix("Genre_")

```

```python
# 对于更大的数据，用str.get_dummies方法 多分类 0-1向量没有那么块，最后使用numpy数组的底层函数，直接写入numpy数组，然后将结果封装在DataFrame中
# 1 模拟十万行多标签列
# N = 100000
# tags_pool = np.array(
#     ["music", "dance", "edu", "game", "tech",
#      "food", "pet", "travel", "comedy", "sport"]
# )
# rng = np.random.default_rng(42)
#
# #
# # 每行随机 1~3 个标签，用 "|" 连接
# def make_sample():
#     k = rng.integers(1, 4)                 # 标签数
#     return "|".join(rng.choice(tags_pool, k, replace=False))  # rng.choice (np.random.choice, 类比今天学的sample）
#
# col = pd.Series([make_sample() for _ in range(N)], name="tags")
# col.head()
col.head(10)
#
#
# 2 拆分为一维标签数组 + 行号数组
# split_lists = col.str.split("|")           # Series[list[str]]
# split_lists
# lens = split_lists.str.len().to_numpy()    # 每行标签个数
# print(split_lists.str.len())
# lens
# row_idx = np.repeat(np.arange(N), lens)    # 对应行号，拉平成 1 维  0,1,1,2 ....
# row_idx[:1000]
#
# flat_tags  = np.concatenate(split_lists.to_numpy()) # 所有标签摊平成 1 维   np.array装的是list，np.concatenate(序列） ： 序列里的所有元素 给拼在一起
# flat_tags
# split_lists.to_numpy()  # 这里转成了一个ndarray 里面装着标签list
#
#
# # 3) 建立“标签 → 整数”映射
# np.unique
# np.unique([1,3,5,2,2])  # 去重
# np.unique([1,3,5,2,2], return_inverse=True)  # return_inverse=True; 不仅返回去重后的列表，还返回原来数据 在去重列表的索引
#
# # flat_tags: 每一行的1个/ 多个标签  平摊成一维数组的样子
#
# uniques, tag_pos = np.unique(flat_tags, return_inverse=True)
# print(uniques)
# print(tag_pos)
# # uniques      : 所有不重复标签（已排序）
# # tag_pos[i]   : flat_tags[i] 在 uniques 中的位置 0..C-1
#
#
# 4 写入 NumPy 0/1 矩阵
n_rows, n_cols = N, len(uniques)
onehot = np.zeros((n_rows, n_cols), dtype=np.uint8)
onehot
onehot[row_idx, tag_pos] = 1               #  一句话完成对分类的编码
onehot

# onehot[[1,2,3],[3,4,5]]   onehot[1,3] , onehot[2,4], onehot[3,5]

# 5 把onehot 封装为 DataFrame
df_dummies = pd.DataFrame(onehot, columns=uniques, index=col.index)
df_dummies
# df_dummies.head(10)

col.str.get_dummies("|")

# 统计应用中，可以把pd.get_dummies和pd.cut结合起来使用
np.random.seed(12345)

values = np.random.uniform(size=10)
values

bins = np.linspace(0, 1, 6)
bins

pd.get_dummies(pd.cut(values, bins))

```

### pandas扩展数据类型

\- pandas原本建立在Numpy功能之上，所以数据类型沿用的numpy，这有一些问题：

1. 整数，布尔值的缺失值被当成浮点数，导致不易察觉的问题
2. 含有大量字符串数据时，计算开销大，同时占用很多内存

```python
# pandas有扩展的数据类型，创建NumPy原本不支持的新数据类型
s = pd.Series([1,2,3,None])
s
# 用pandas的数据类型创建它
s = pd.Series([1,2,3,None], dtype=pd.Int64Dtype())
s
s.isna()
s.dtype

# 这里的NA是pd.NA
s.loc[3]
s.loc[3] is np.nan # False
s.loc[3] is pd.NA  # True

# pandas还有专门扩展的字符串类型，此类字符串数组通常使用更少的内存
s = pd.Series(["one", "two", None, "three"], dtype=pd.StringDtype())
s


```

![image-20250629171430248](C:\Users\lxy12\AppData\Roaming\Typora\typora-user-images\image-20250629171430248.png)

### 扩展类型：分类类型

Categorical对象有categories(分类名)和codes(分类编码)两个属性

df[列名].astype("category")可以将df中的某列转换成分类类型

 pd.Categorical(列表)，可以将列表内容转换成**分类属性**，也可以通过pd.Categorical.from_codes(codes, categories)建立分类类型的对象codes是编码，categories是目录

​	order= True/False，来实现**categories**属性是否有序，也可以通过**.as_ordered()**将无序转变成有序

与字符串方法相同的是分类属性也有类似的方法，series.cat.分类对象方法/属性

```python
# 表中的一列通常会有重复的，包含不同值，使用nunique计算唯一值的数量，unique可以提取出唯一值，使用value_counts可以为唯一值计算频率
values = pd.Series(["apple", "orange", "apple", "apple"] * 2)
values
values.nunique()
values.unique()
values.value_counts()

# 唯一值的用途：作为一个维度表，将实际观测值存储为引用维度表的整数键（索引）
values = pd.Series([0,1,0,0] * 2)
dim = pd.Series(['apple', 'orange'])
values

# 使用take方法存储原始的字符串Series，关联维度表dim，取维度对应的值
dim.take(values)

# 这种通过整数表示,不同数据的方式是分类, 具体类别对应的数字表示称为分类编码
# 分类表示可以节省数据的内存空间 (数据压缩)

fruits = ["apple", "orange", "apple", "apple"] * 2
N = len(fruits)

rng = np.random.default_rng(seed=12345)
df = pd.DataFrame({'fruit': fruits, 'basket_id': np.arange(N), 'count': rng.integers(3, 15, size=N), 'weight': rng.uniform(0, 4, size=N)},
                  columns=['basket_id', 'fruit', 'count', 'weight']
                  )
df
print(df.info())

# 现在df["fruit"]是一个python字符串对象的数组,可以把它转换为分类数据
fruit_cat = df["fruit"].astype("category")

print(fruit_cat) # fruit_cat的dtype变成了category

print(fruit_cat.array, type(fruit_cat.array))  # .array属性是pandas.Categorical实例

# Categorical对象有categories(分类名)和codes(分类编码)两个属性
fruit_cat.array.categories
fruit_cat.array.codes

# 复习python的enumerate
dict(enumerate(fruit_cat.array.categories))

# 这列series转换分类类型后,可将DataFrame的那一列转换为分类
df["fruit"] = df["fruit"].astype("category")
df["fruit"]

# 可以从其他python序列直接创建 pd.Categorical
my_categories = pd.Categorical(['foo', 'bar', 'baz', 'foo', 'bar'])
my_categories

# 已经获取了数据的分类编码 和 具体类别,可以用from_codes把数据的分类编码转成对应类别
categories = ["foo", "bar", "baz"]
codes = [0, 1, 2, 0, 0, 1]
my_cats_2 = pd.Categorical.from_codes(codes, categories)
my_cats_2

# 注意:默认分类没有顺序,from_codes也可以指定顺序
ordered_cat = pd.Categorical.from_codes(codes, categories, ordered=True)
ordered_cat  # foo<bar<baz
my_cats_2.as_ordered()  # 无序的分类对象 可以通过as_ordered排序

# 分类数据可以不是字符串,可以是任意不可变的类型
# 不可变 <-> 不能原地修改 <-> 有唯一的id （哈希值）
# 哈希值字典的作用：查找在不在里面；根据键查找值

# 使用Categorical对象进行计算
import numpy as np
import pandas as pd

rng = np.random.default_rng(seed=42)
draws = rng.standard_normal(1000)
draws[:5]

bins = pd.qcut(draws, 4)
bins
#
# # 样本分位数添加标签名
bins = pd.qcut(draws, 4, labels=['Q1', 'Q2', 'Q3', 'Q4'])
bins  # pandas的Categorical对象
bins.codes
bins.categories
#
# # 带有标签的bins分类不包含数据分箱的数据边界信息,可以使用groupby提取一些汇总统计信息
bins = pd.Series(bins, name='分位数') # 分类对象转成series  pd.Series(列表/numpy数组/字典/pandas的分类对象, name=series的名字）
bins

# 采样的数据 draws 1000个随机数， 去根据 1000个分类数据 来分组  根据分类的唯一数量N，来分成N组；分组后 ： 组1：这个组的数据（series)； 组2：这个组的数据； 组n：这个组的数据

#                                                          得到的分组的对象 （组1：一组series； 组2：一组seires； 组n：一组series）， 我可以按组去做汇总统计

#   分组对象.agg:  agg(aggregate, 聚合）  agg(聚合函数的字符串列表）
results = pd.Series(draws).groupby(bins, observed=True).agg(['count', 'min', 'max'])  # observed=True, 只保留出现过的组
results

results = results.reset_index() # data_frame.reset_index()  效果：生成新的data frame；把原来的dataframe的行索引 变成列，新的索引 默认的 0--n-1
# results
#
results['分位数']  # 结果中的quartile列 保留了bins中的原始分类信息

```

```python
## 随堂练习：
# 你在分析一次广告投放的数据。每一行代表一个用户的点击数据，
#  包括他们看到广告的次数（impressions)、广告点击数(clicks)，以及他们所属的渠道(channels)。
#  你希望将用户按点击率（CTR）划分为四个等级，然后结合渠道分析不同等级在不同渠道中的分布。

n = 100
df = pd.DataFrame({
    "user_id": range(1, n+1),
    "channel": np.random.choice(["Google", "Facebook", "TikTok", "Twitter"], size=n),
    "impressions": np.random.randint(50, 500, size=n),
    "clicks": np.random.randint(0, 50, size=n),
})

# 1. 给df添加点击率列，列名是ctr，计算方式：点击数 / 看到广告的次数
df["ctr"] = df["clicks"] / df["impressions"]

# 2. 将 CTR（点击率）分成四个等级（分位数 0，0.25，0.5，0.75 1），命名为 ctr_level，等级分为：低, 中, 高, 非常高。  (提示：pd.qcut)
ctr_level = pd.qcut(df["ctr"], 4, labels=["低", "中", "高", "非常高"])


# 3. 统计TikTok渠道中，各个 点击率等级 (ctr_level)里的
#                                              用户数量, 最低点击率和最高点击率

tiktok_ctr = df["ctr"].loc[df["channel"] == "TikTok"]
tiktok_ctr.groupby(ctr_level, observed=True).agg(['count', 'min', 'max'])

```

```python
# 分类可以降低内存占用
#  例子:1000万个数据 和 若干不同的分类
# N = 1000_0000  # 数字之间加下划线，也是1千万
# labels = pd.Series(['foo', 'bar', 'baz', 'qux'] * (N // 4))
# labels
# 把lables转换为分类
categories = labels.astype('category')
#
# 两者的内存占用
print(labels.info())
print(categories.info())  # 可以看出分类占用的内存 远低于字符串占用的内存
#
# # 比较两者value_counts性能:内部根据类别进行GroupBy; 可以体现用整数编码的分类GroupBy也更快
# %timeit labels.value_counts()   # %timeit 具体的python代码  ，得出具体python代码的执行时间

# %timeit categories.value_counts()

# 分类方法:包含分类数据的Series有一些特殊方法,类似于Series.str的字符串方法.

#  series.str.字符串方法
#  series.cat.分类对象方法/属性
s = pd.Series(['a', 'b', 'c', 'd'] * 2)
cat_s = s.astype('category')
cat_s

cat_s.cat.codes # 特殊的访问器属性cat提供了对分类属性/方法的访问
cat_s.cat.categories  # 这里直接拿到所有的分类 Index(['a', 'b', 'c', 'd'], dtype='object')
#
# 设置新的分类
actual_categories = ['a', 'b', 'c', 'd', 'e']
cat_s2 = cat_s.cat.set_categories(actual_categories)
cat_s2
#
# 新的分类将反应在操作中
cat_s.value_counts()
cat_s2.value_counts()
#
# # 过滤完数据后,删除没有观察到的分类(对分类对象做修改,数据本身不变)

cat_s3 = cat_s.loc[cat_s.isin(['a', 'b'])]
cat_s3
cat_s3.cat.remove_unused_categories()
#
# 独热编码(one-hot encoding):每个分类都是一列,对于出现的分类用1 (True) 表示, 未出现用0(False); pd.get_dummies
cat_s = pd.Series(['a', 'b', 'c', 'd'] * 2, dtype='category')
pd.get_dummies(cat_s)  # 分类series转成独热编码

```

### 字符串操作

 \- python字符串对象的内置方法能轻松完成大部分文本运算, 对于更复杂的模式匹配和文本操作,则可能需要用到正则表达式
 \- pandas基于python的字符串方法,做了加强,能够对整组数据应用字符串方法和正则表达式,处理缺失数据

```python
val = "a,b, guido"
val.split(",")

# split常常和strip一起使用,以去除空白符(包括换行符)
pieces = [x.strip() for x in val.split(',')]
pieces

# 再用 "::"连接回去
"::".join(pieces)
#
# # 子字符串定位
"guido" in val
val.index(",")
val.find(":")  # -1 没有找到 :
val.find(",")
# val.index(":") # index找不到会抛出异常,find找不到返回-1
val.count(",")             # 出现次数

val.replace(",", "::")     # 替换文本
val.replace(",", "")


```

### 正则表达式

```python
"""正则表达式
  提供在文本中搜索和匹配字符串模式的灵活方式, 正则表达式常称作regex,是根据正则表达式语言编写的字符串
  python的re模块负责对字符串应用正则表达式
  re模块的函数可以分为三类:模式匹配:findall, search, match; 替换: sub, subn, 拆分: split; regex描述的是在文本中定位的模式
"""

# 拆分字符串,分隔符为数量不定的一组空白符(制表符,空格,换行符等) # \t " ", "\n"
import re

text = "foo     bar\t baz  \tqux"
print(text)

re.split(r"\s+", text)  # "\s+": 表示一个或多个空白符的 正则表达式, 字符串之前加r (raw)  表示 \不识别成转义符  也可以写成re.split(“\\s+”,text)
#
# # re.split执行流程: 正则表达式会先被编译,然后再在text上调用其split方法
# 可以自己先编译正则表达式,直接得到可重复使用的正则表达式对象:

#  编译正则表达式 ：re.compile(正则表达式语法写的文本模式)  ->   正则表达式（文本模式对象）
regex = re.compile(r"\s+")   # compile->”汇编”
# 模式对象.split(文本）
regex.split(text)
#
# 找到匹配正则表达式的所有模式,可以使用findall方法 ， 返回列表，列表里和regex 表示的模式匹配上的字符串
regex.findall(text)  # ['     ', '\t ', '  \t']
#
# 打算对许多字符串应用同一条正则表达式,强烈建议通过re.compile创建正则表达式对象, 省掉每次编译的时间
#
# # findall返回字符串中所有的匹配项,而search则只返回第一个匹配项,match只匹配字符串的起始位置
text = """Dave dave@google.com
Steve steve@gmail.com
Rob rob@gmail.com
Ryan ryan@yahoo.com"""
#
pattern = r"[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}"  # 识别电子邮件地址的正则表达式  # 字母/数字/符号 数量不限@字母/数字/符号 数量他不限.2-4个字母
#
regex = re.compile(pattern, flags=re.IGNORECASE) # 正则表达式不区分大小写  # flags=re.IGNORECASE

regex.findall(text)  # 匹配text里所有电子邮件
#
m = regex.search(text)  # search返回文本中的第一个电子邮件地址 （以匹配对象的形式返回）
m
m.start()
m.end()
text[m.start():m.end()]   # 匹配对象中拿到模式在原字符串中的起始位置和结束位置
#
print(regex.match(text)) # regex.match返回None，因为它只匹配出现在字符串开始位置的模式


#
#  模式对象.sub(替换值，原始文本)  -> 新字符串
print(regex.sub("邮箱丢失", text))  # sub方法可以将匹配到的模式替换为指定字符串，并返回得到新的字符串
#
# 假设不仅想找出电子邮件地址，还想将各个地址分成3个部分：用户名，域名以及域名后缀， 需要将待分割的模式的各部分用圆括号括起来
pattern = r"([A-Z0-9._%+-]+)@([A-Z0-9.-]+)\.([A-Z]{2,4})"
regex = re.compile(pattern, flags=re.IGNORECASE)
#
m = regex.match("wesm@bright.net")
m

# 拿到匹配对象 m后 ，m.groups()  返回一个由模式各段组成的元组
m.groups() # 产生的匹配对象，可以通过groups方法返回一个由模式各段组成的元组
#
regex.findall(text)  # 对于带有分组的模式，findall会返回一个元组列表
#
print(regex.sub(r"用户名: \1, 域名: \2, 后缀: \3", text)) # sub能通过诸如\1,\2之类的特殊符号访问各匹配项中的分组，\1对应第一个匹配的组，\2对应第二个匹配的组

```

| 片段           | 含义                                                         |
| -------------- | ------------------------------------------------------------ |
| r""            | **原始字符串**前缀：告诉 Python 不要把反斜杠当转义字符，而是直接交给正则引擎。 |
| (...)          | **捕获组**，方便之后用 `group(1)`… 引用。共有 3 个组。       |
| [A-Z0-9._%+-]+ | **字符类**：允许 A–Z、数字、点、下划线、百分号、加号、减号；`+` 表示「1 次或多次」。这部分匹配邮箱 **用户名**。 |
| @              | 字面量 at 符号                                               |
| [A-Z0-9.-]+    | 第二个捕获组：匹配 **域名主体**（字母、数字、点、连字符）。  |
| \.             | **反斜杠 `\` 的作用：转义**。点 `.` 在正则中代表「任意字符」，`\.` 让它失去特殊含义，变成真正的句点。 |
| [A-Z]{2,4}     | 第三个捕获组：匹配 **顶级域**，由 2\~4 个字母组成，如 `com` / `cn` / `info`。 |

 

### Pandas字符串方法

```python
# pandas的字符串函数

# 理论上，map方法可以让series/dataframe里的字符串做处理，但如果存在NA（空值）就会报错
# Series的str属性既可以访问字符串方法，也可以对里面的每个字符串做处理，自动忽略缺失值
data = {"Dave": "dave@google.com", "Steve": "steve@gmail.com", "Rob": "rob@gmail.com", "Wes": np.nan}
data = pd.Series(data)
data
data.isna()

# "asdsadasd".__contains__("asd")
# "asd" in "asdsadasd"
data.str.contains("gmail")
#
# pandas提供了扩展类型，专门用于处理字符串，整数和布尔值的缺失数据
data_as_string_ext = data.astype(pd.StringDtype())
data_as_string_ext
#
data_as_string_ext.str.contains("gmail")
data_as_string_ext.str.contains("gmail").dtype  # pd.BooleanDtype
#
# # pandas字符串函数，使用正则表达式
pattern = r"([A-Z0-9._%+-]+)@([A-Z0-9.-]+)\.([A-Z]{2,4})"
data.str.findall(pattern, flags=re.IGNORECASE) # flags指定re选项
#
# 向量化元素获取：str.get, str属性上使用索引
#  str.get(索引)
#  str[索引]
x_series = data.str.findall(pattern, flags=re.IGNORECASE)  # series series装着元组列表
x_series.str[0].str.get(1)
#
# 向量量化的字符串列表长度  series.str.len()  -> 转成一个长度的series
data.str.findall(pattern, flags=re.IGNORECASE).str.get(0).str.len()
#
# 使用完pandas字符串方法后，接着使用它，连续应用向量化字符串/列表处理
data.str.findall(pattern, flags=re.IGNORECASE).str.get(0).str.get(1)
#
# # 还可以利用str对字符串进行切片
data.str[:5]  # 对data里的每个字符串 取前5个字符

#
data.str.extract(pattern, flags=re.IGNORECASE) # str.extract方法可以用DataFrame的形式返回正则表达式获取的分组


# str.split 方法根据分隔符的出现位置拆分字符串。Pandas 返回一个分割列表的series
chicago = pd.read_csv("datasets/chicago.csv").dropna(how="all")
chicago

# chicago["Department"].nunique()
chicago["Department"] = chicago["Department"].astype("category")
chicago.head()

# series.str   # 只有series能获取到字符串操作对象
# chicago.str  # 报错：AttributeError: 'DataFrame' object has no attribute 'str'


chicago["Position Title"].str.split(" ").str.get(0).value_counts().idxmax()

# chicago["Position Title"].str.split(" ").str.get(0).value_counts().idxmax()  # 找到岗位列里出现频次最高的第一个词
#
# str.split方法内指定 expand=True，可以返回DataFrame，而不是装着列表的series
chicago["Name"].str.split(",", expand=True)
#

# data_frame[新的列名] = series  # 给data_frame添加一个新列

# data_frame[列的列表] = data_frame_插入  # 给data_frame插入多列

chicago[["姓", "名"]] = chicago["Name"].str.split(",", expand=True)
chicago
#
# 其他str内的方法
# chicago["Position Title"].str.lower()
# chicago["Position Title"].str.upper()
# chicago["Position Title"].str.title()   # 每个单词的首字母大写,单词按照” ”进行分割
# chicago["Position Title"].str.len()
# chicago["Position Title"].str.title().str.len()
# chicago["Position Title"].str.strip()    # 删除字符串两端的空字符串，并返回新的字符串
# chicago["Position Title"].str.lstrip()   # 删除字符串左侧的空字符串并返回新字符串
# chicago["Position Title"].str.rstrip()   # 删除字符串右侧的空字符串并返回新字符串
# chicago["Department"].str.replace("MGMNT", "MANAGEMENT").str.title()

```

```python
## 随堂练习：找到所有姓（last name）的出现频率: name列的格式：  名, 姓 中间名
# str.split 方法根据分隔符的出现位置拆分字符串。Pandas 返回一个分割列表的series
chicago = pd.read_csv("datasets/chicago.csv").dropna(how="all")
chicago

# chicago["Department"].nunique()
chicago["Department"] = chicago["Department"].astype("category")
chicago.head()

chicago["Name"].str.split(", ").str.get(1).str.strip().str.split().str.get(0).value_counts()

```

总结

 \- 数据清洗,准备工作做的好可以让数据分析过程更顺畅,提高生产力
 \- 数据清洗: 缺失值处理, 重复值删除,数据转换和替换,检测和过滤异常值
 \- 数据准备: 重命名轴索引,分箱, 数据重排和采样,pandas扩展类型, 分类类型,字符串方法



## 数据整理与相关

许多应用中，数据可能分散在多个文件或数据库的不同表中，或者排列的形式不利于数据分析，关注多个数据表之间连接，联合，和重塑数据的方法

### 层次化索引

\- 一个轴上拥有多个（两个以上）索引层级，能以低维度形式处理高维度数据

```python
import pandas as pd
import numpy as np

np.random.seed(100)
data = pd.Series(np.random.uniform(size=9), index=[["a","a","a","b","b","c","c", "d", "d"],
                                                   [1,2,3,1,3,1,2,2,3]])
data # 索引之间的间隔表示直接使用上面的标签
data.index  # MultiIndex
# print(data)

# 对于层次化索引对象，可以使用部分索引，使用它选取数据子集更为简单
data.loc["b"]       # -> series
data.loc[("b", 3)]  # -> 数字
data.loc["b", 3]   # 和上一行一样
data.loc["b":"c"]  # 利用部分索引 做切片  ——>  层次索引的series
data.loc[["b","d"]]  # 选b和d对应的数据 -> 层次索引的series
#
# 层次化索引的切片
data.loc[("a",2):("c",2)]
data.loc[("b",1):("c",2)]
#
# 在内部层级进行选取也可以
data.loc[:, 2]  # 定位第二层索引值为2的数据  索引降为1级
data.loc[:, [2]]     # 索引还是多层级
#
#
# 对于DataFrame, 每个轴都可以有分层索引
frame = pd.DataFrame(np.arange(12).reshape((4,3)), index=[["a", "a", "b", "b"],[1,2,1,2]], columns=[["Ohio", "Ohio", "Colorado"],["Green", "Red", "Green"]])
frame
#
# # 各层都可以有名称，注意是names属性，name属性只用于单层索引
frame.index.names = ["key1", "key2"]
frame.columns.names = ["state", "color"]
frame
# print(frame)

frame.index.nlevels # 知道索引有多少层
frame.columns.nlevels
frame["Ohio"] # 使用部分列索引，可以轻松选取列分组
#
# 可以单独创建MultiIndex对象，然后复用。
multi_index_obj = pd.MultiIndex.from_arrays([["Ohio", "Ohio", "Colorado"],["Green", "Red", "Green"]], names=["state", "color"])
multi_index_obj
#
# 获取层次化索引，其中一个层次的所有值：get_level_values, 在层次化索引对象上调用
multi_index_obj.get_level_values("state")
multi_index_obj.get_level_values(0)
multi_index_obj.get_level_values(1)
#
frame.index.get_level_values(0)
frame.index.get_level_values(1)
frame.index.get_level_values("key2")

# 随堂练习：
bigmac = pd.read_csv("datasets/bigmac.csv", parse_dates=["Date"], date_format="%Y-%m-%d", index_col=["Date", "Country"]).sort_index()
bigmac.head(10)

print(bigmac.head(10))

# 1. 预测以下代码输出的内容大概有什么，数据类型是什么（DataFrame/Series/具体数值/具体布尔值 ，然后运行以下代码验证你的预测
bigmac.loc["2000-04-01"]
#
bigmac.loc["2000-04-01", "Canada"]
bigmac.loc["2000-04-01", "Price in US Dollars"]  # 这里是series， ,之前是行， ,之后是列

bigmac.loc[("2000-04-01", "Canada")]  # 没有对列做筛选
bigmac.loc["2000-04-01", "Canada"] == bigmac.loc[("2000-04-01", "Canada")]  # series 和 series做比较
#
start = ("2000-04-01", "Hungary")
end = ("2000-04-01", "Poland")
bigmac.loc[start:end]  # 还是dataframe，选取了一些行
#
bigmac.loc[("2019-07-09", "Hungary"):]
#
bigmac.loc[("2012-01-01", "Brazil"): ("2013-07-01", "Turkey")]
bigmac.loc[("2012-01-01", "Brazil"): ("2013-07-01", "Turkey"), "Price in US Dollars"]  # series
#
# # 2. 取到2018-01-01 中国（China) 到 2018-01-01 丹麦（Denmark)的 巨无霸价格
bigmac.loc[("2018-01-01", "China"):("2018-01-01", "Denmark"), "Price in US Dollars"]

```

### 重排序和层级排序

1. df.swaplevel() 传入多层次索引当中的两个层次名，这样会将两个层级进行调换，并返回新的df

2. df.sort_index()：

   ​	level = 数字/[列表],level既可以传入索引名字也可以传入索引的位置

   ​	ascending=True/False/[True,False],要与传入层级个数匹配，当传入层级为多个，但ascending为单个时，会进行广播

3. 

   ​	

```python
frame
# 有重新调整某条轴上各层级的顺序，swaplevel
frame.swaplevel("key1", "key2")  # 返回一个层级互换的新对象
#
# 指定层级上的值对数据排序: sort_index默认根据所有索引层级中的字母顺序对数据进行排序
frame.sort_index()  #  (a,1), (a,2), (b,1), (b,2)
#
frame.sort_index(level=1) # 通过传入level参数只选取单层级 来排序
frame.sort_index(level=[0,1], ascending=[False, True])  # 通过传入level参数选取多个层级来排序，一个降序，一个升序
#
frame.swaplevel("key1", "key2").sort_index(level=0)

# 索引排序的意义：如果索引从最外层开始是按字母顺序排序的，即数据是执行了sort_index()之后的结果，则对数据进行选取的性能会高很多
#  选取操作：df.loc["某个值"]
#          df.loc["start":"end"]
# 如果索引是排序的，Pandas 可以用 二分搜索（O(log n)) 来定位。  # O(log n)  n是输入的数据大小 （输入数据有多少个）； log(n)执行次数； O(log(n)) 代表执行次数（程序的时间复杂度）是log(n)这个级别
# 如果索引是无序的，就只能 逐个扫描（线性搜索 O(n))。           # O(n)      n是输入的数据大小 ， n执行次数，O（n 代表执行次数（程序的时间复杂度）n这个级别
#               一个级别：  通过乘上一个固定的数字 / 或者加上一个固定的数字得到

# 按层级进行汇总统计
# 先按层级做分组groupby，分组时可以指定level，再对groupby的结果做聚合（sum,min,max等等）
print(frame)
frame.groupby(level="key2").sum()

# 构造一个 MultiIndex 的 DataFrame
arrays = [
    ["North", "North", "South", "South", "East", "East"],
    ["Jan", "Feb", "Jan", "Feb", "Jan", "Feb"]
]
my_index = pd.MultiIndex.from_arrays(arrays, names=["Region", "Month"])
my_index
#
df = pd.DataFrame({"Sales": [200, 220, 150, 180, 100, 130]}, index=my_index)
df
df.groupby(level="Region").sum() # 按region进行汇总
df.groupby(level="Month").sum()  # 按month进行汇总


# 使用DataFrame的列 做为行索引: set_index
frame = pd.DataFrame({"a": range(7), "b": range(7,0,-1), "c": ["one", "one", "one", "two", "two", "two", "two"], "d":[0,1,2,0,1,2,3]})
frame

frame

# # DataFrame的set_index函数会将单列或多列转换为行索引，创建一个新的DataFrame
frame1 = frame.set_index("c")
frame1
#

frame2 = frame.set_index(["c", "d"])  # data_frame.set_index(列名的列表）； 所有列都会行索引； 层次索引（第一个是最外层的索引）
frame2

frame.set_index(["c", "d"], drop=False) # 默认情况下，作为行索引的列会从DataFrame中移除，但也可以通过传入drop=False将其保留下来


frame.reset_index()
frame2.reset_index()  # reset_index的功能与set_index相反，将所有行索引转到列，索引变成的默认的 0，1，2，...

```

```python
# 随堂练习：
# 1
# 将 subway_locations.csv 文件导入 DataFrame。 使用 'index_col' 参数附加一个多层索引，其层级来自 'state' 和 'city' 列（按此顺序）。
# 按两个索引层级对 DataFrame 进行排序（按常规升序）。
# 将生成的 DataFrame 赋值给 'subway' 变量。

subway = pd.read_csv("datasets/subway_location.csv", index_col=["state", "city"]).sort_index()
subway
# 2
# 提取 'state' 层级值为 'OK' 且 'city' 层级值为 'Broken Arrow' 的行。将结果赋值给 'broken_arrow' 变量。
broken_arrow = subway.loc["OK", "Broken Arrow"]
broken_arrow
# 3：
# 提取 'state' 层级值为 'FL' 且'city' 层级值为 'Winter Park' 的行。将结果赋值给 'winter_park' 变量。
winter_park = subway.loc["FL", "Winter Park"]
winter_park

```



```python
# transpose 方法反转 DataFrame的水平和垂直轴
start = ("2018-01-01", "China")
end = ("2018-01-01", "Denmark")
bigmac.loc[start:end]
bigmac.loc[start:end].transpose()

# `stack` 方法将列索引移动到行索引。 Pandas 将返回一个 MultiIndex Series  (层级索引的series)
# 可以将其想象成 堆叠（stacking）成了层次化索引
world = pd.read_csv("datasets/worldstats.csv", index_col=["year", "country"]).sort_index()
world.head()
world.stack()
world.index
type(world.stack())    #  pandas.core.series.Series

#  一维的numpy列表.reshape((-1,1))   to_frame series转成dataframe，数据不变
world.stack().to_frame()
#
# # unstack 方法将行索引移动到列索引（`stack` 方法的逆操作）。  （series.unstack())
# 默认情况下，unstack 方法将移动最内层的索引。
# 可以使用 level 参数自定义要移动的索引, level 参数接受层级的索引位置或其名称。它也可以接受位置/名称的列表。
world = world.stack()
world.unstack()
world.unstack(level=-1)  # level=-1, -1表示层级化索引的最后一个，默认行为
world.unstack(level=2)  # unstack 2nd  index, 最内层，默认行为
world.unstack().unstack()  # dataframe.unstack()
# world.unstack().unstack().index

world.unstack(level=0)
world.unstack(level="year")  # level=“year" year那一层的索引 移动到列
world.unstack(level=-3)

world.unstack(level=1)
world.unstack(level="country")
world.unstack(level=-2)
#
world.unstack([1, 0])  # year 国家 人口/GDP
world.unstack(["country", "year"])

#
world.unstack([0, 1])
world.unstack(["year", "country"])
#
# 在 column index排序
world.unstack(["year", "country"]).sort_index(axis="columns").columns

```

```python
#  dataframe的 pivot方法将数据从高格式重塑为宽格式。   Pivot 枢纽
# - 添加更多数据，数据会朝哪个方向扩展。
# -  高/长格式向下扩展。宽格式向外扩展。
#  pivot函数的参数：
# - index 参数设置 pivot后的DataFrame 的行索引。
# - columns 参数设置 pivot后的DataFrame的列索引 （columns=原来的dataframe的列名，列索引就会变成这一列的值)。
# - values 参数设置 pivot后的DataFrame的值。Pandas 将根据行索引 和 列的交集填充正确的值

# 使用pivot：高格式 改成 宽格式
sales = pd.read_csv("datasets/salesmen.csv")
sales

#           Sharon   Oscar  Salesman 1  Salesman 2  新的销售员
# Date
# 1/1/2025   7172   1864
# 1/2/2025   7543   7105
# 1/3/2025   1053   6851

# data as index, separate column for every salesman
sales_horizontal = sales.pivot(index="Date", columns="Salesman", values="Revenue")


# `melt` 方法是 `pivot` 的逆方法。它接受一个“宽”数据集并将其转换为一个“高”数据集。
#  当有多个列存储 同类数据时，可以选择melt方法。

# 三个参数：
# id_vars（“固定不变”的列）   希望保留的列，不参与melt  这些列的值会被重复，以配合其他列变成长格式

# var_name（新的列名：来自原来的除掉id_vars里的“列名”）
# 可以给这一列指定一个名字

# value_name（新的列名：来自原来单元格的“值”）  原来各个列中的值，变成了一列里的值
quarters = pd.read_csv("datasets/quarters.csv")
# quarters
#
quarters.melt(id_vars="Salesman", var_name="季度", value_name="利润")
sales_horizontal.reset_index().melt(id_vars="Date", var_name="销售员", value_name="利润")

# sales_horizontal.reset_index().melt(id_vars="Date", var_name="销售员", value_name="利润")

```

```python
# pivot_table` 方法的操作类似于 Excel 中的数据透视表功能。 数据透视表是一种表，其值是来自另一个表的多组值的聚合(统计）。
# - values 参数接收要聚合其值的列, 这一列是数值类型
# - aggfunc 参数声明聚合函数（默认值为平均值）。
# - index 参数设置数据透视表的索引标签。允许使用层次化索引。
# - columns 参数设置数据透视表的列标签。允许使用层次化索引。

foods = pd.read_csv("datasets/foods.csv")
print(foods.head())
foods.pivot_table(values="Spend", index="Gender") # 男性和女性的平均花费， 男性和女性是行索引 ——> DataFrame
#
foods.pivot_table(values="Spend", index="Gender", aggfunc="mean")
foods.pivot_table(values="Spend", index="Gender", aggfunc="sum")
foods.pivot_table(values="Spend", index="Item", aggfunc="sum")
#
# #  跨列做透视表
# foods.pivot_table(values="Spend", index=["Gender", "Item"],  aggfunc="sum")
foods.pivot_table(values="Spend", index=["Gender", "Item"], columns="City", aggfunc="sum")
foods.pivot_table(values="Spend", index="Item", columns=["Gender", "City"], aggfunc="sum")
foods.pivot_table(values="Spend", index="Item", columns=["Gender", "City"], aggfunc="mean")
foods.pivot_table(values="Spend", index="Item", columns=["Gender", "City"], aggfunc="count")
foods.pivot_table(values="Spend", index="Item", columns=["Gender", "City"], aggfunc="max")
foods.pivot_table(values="Spend", index="Item", columns=["Gender", "City"], aggfunc="min")

```

### 分组 (GroupBy)

\- **分组** 是一种根据列值对数据进行组织/分类/分组的方法。
 \- `groupby` 方法返回一个 **DataFrameGroupBy** 对象。它类似于一个字典结构中的 **DataFrames** 组/集合。
 \- **DataFrameGroupBy** 对象可以对其中的**每个**组执行聚合操作。

```python
fortune = pd.read_csv("datasets/fortune1000.csv", index_col="Rank")
fortune.head()

# 每个行业的总收入
fortune[fortune["Sector"] == "Retailing"]["Revenue"].sum()
# # 限制: 对每一个行业, 写这样的代码
#
sectors = fortune.groupby("Sector") # data_frame.groupby(data_frame的列名)
sectors # <pandas.core.groupby.generic.DataFrameGroupBy object at 0x000002335BEEBD10>;  字典  行业名字：dataframe(只包括这个行业）；
len(sectors)  # 组对象的长度
fortune["Sector"].nunique()
#
sectors.size()  # 按行业分组后, 获取每个行业组的大小  （series）
fortune["Sector"].value_counts()

sectors.first()  # 每个组的第一个数据  一整行
sectors.last()   #　每个组的最后一个数据
#
# DataFrameGroupBy 对象上的 get_group 方法检索属于特定组/类别的嵌套 DataFrame
sectors.get_group("Energy")  # 基于列名 Energy 查找 这个组的DataFrame
sectors.get_group("Technology")
#
# # 使用方括号索引 DataFrameGroupBy 对象，从原始 DataFrame 中“提取”一列。
# # 生成的 SeriesGroupBy 对象将包含可用的聚合方法。
# # Pandas 将对集合中的每个组执行计算。
sectors["Revenue"].sum()  # 统计每个行业的总收入（Revenue)
# 验证:
#     get_group，其中一个group的revenue综合 是和上面的数据能对上的
sectors.get_group("Apparel")["Revenue"].sum()


# sectors["Employees"]  # seriesGroupBy对象
sectors["Employees"].sum()

sectors["Profits"].max()  # 每个行业的利润的最大值
sectors["Profits"].min()

#
sectors["Employees"].mean()
sectors["Employees"].min()


# groupby 索引 可以同时按组去索引多列
sectors[["Revenue", "Profits"]].sum()
# sectors[["Revenue", "Profits"]].mean()
#
# # 将列名列表传递给 groupby 方法， pandas会按这一列里的值 来分组
# # 定位某一列索引 SeriesGroupBy 对象，然后使用方法执行聚合，Pandas 将返回一个 层次化索引的 Series，索引就是每个组
# sectors = fortune.groupby(["Sector", "Industry"])
# sectors.size()
# sectors.size()["Technology"]
#
# sectors["Revenue"].sum()
# sectors["Employees"].mean().head(20)

```

```python
## agg 方法对不同的列应用不同的聚合方法。
## 直接在 DataFrameGroupBy 对象上调用 `agg` 方法。
## 向该方法传递一个字典，其中键是列名，值是聚合操作 （一个字符串，字符串列表，自定义的函数）

sectors = fortune.groupby("Sector")
# sectors.agg("mean") # 报错

# groupby的对象.agg(字典)
sectors.agg({ "Revenue":["sum", "median"], "Profits":"max", "Employees":"mean", "Industry": lambda industry_seris: industry_seris.str.contains("Computer").sum()})
sectors["Revenue"].agg(["count", "sum", "mean"])  # series group by 不指定列
#
#
# DataFrameGroupBy 对象支持 `apply` 方法（就像 Series 和 DataFrame 一样）。
# apply` 方法会在 DataFrameGroupBy 对象中每个嵌套的 DataFrame 上调用一个函数。
# 它捕获函数的返回值，并将它们收集到一个新的 DataFrame 中（返回值）。
# 使用场景：自定义聚合

# 找到每个行业内，员工最多的两个公司
#    data_frame.nlargest(2, 列名)
fortune.nlargest(2, "Employees")

# for k, v in sectors:
#      apply v.nlargest(2, "Employees)


def top_two_companies_by_employee_count(sector):
    return sector.nlargest(2, "Employees")

# 分组对象.apply(自定的函数）
sectors.apply(top_two_companies_by_employee_count, include_groups=False)
# sectors.apply(lambda sector : sector.nlargest(2, "Employees"))
# include_groups=False 排除掉用来分组的列，只有非分组列参与运算
```

```python
# 随堂练习， 跑这个代码生成数据：
import pandas as pd
import random
from datetime import datetime, timedelta

customers = [1001, 1002, 1003, 1004, 1005]
products = [
    ("T-Shirt", "Clothing", 20),
    ("Jeans", "Clothing", 40),
    ("Laptop", "Electronics", 800),
    ("Phone", "Electronics", 500),
    ("Book", "Books", 15),
    ("Notebook", "Books", 5),
    ("Pen", "Stationery", 1),
    ("Bag", "Accessories", 30)
]

rows = []
for i in range(1, 31):
    order_id = i
    customer_id = random.choice(customers)
    product, category, price = random.choice(products)
    quantity = random.randint(1, 5)
    order_date = datetime(2024, 6, 1) + timedelta(days=random.randint(0, 10))
    rows.append([order_id, customer_id, product, category, quantity, price, order_date.date()])



df = pd.DataFrame(rows, columns=["order_id", "customer_id", "产品", "产品分类", "数量", "价格", "订单日期"])
df


# customer_id,   名字  家庭住址  性别  年龄
#  1001
#  1003
```

```python
# 1. 每位顾客购买的商品总数
# 问题：
# 计算每位顾客购买的商品总数。
df.groupby("customer_id")["数量"].sum()

# 提示：按 customer_id 分组数据，并对数量列求和。

# 2. 每种产品的总销售额
# 问题：
# 计算每种产品产生的总收入。
# （收入 = 数量 × 价格）
df["付款"] = df["数量"] * df["价格"]
df.groupby("产品")[["付款"]].sum()
# df.groupby("产品").agg({"付款": "sum"})
# 提示：
# 创建一个新列 sales = 数量 * 价格，
# 然后按产品分组并计算销售额的和。

# 3. 按产品类别计算平均价格
# 问题：
# 计算每种类别商品的平均价格是多少？
df.groupby("产品分类").agg({"价格":"mean"})

# 提示：
# 按类别分组并计算价格列的平均值。

# 4. 每日订单数量和总销售额
# 问题：
# 显示每个日期的订单数量和总收入。
a_result = df.groupby("订单日期").agg({"order_id": "count", "付款": "sum" })
a_result.columns = ["订单数量", "付款"]
a_result

# 提示：
# 使用 .agg() 将多个聚合函数应用于 order_id 和销售额。

# 5. 按类别划分的客户支出
# 问题：

# 计算每个客户和类别对的总支出。
df.groupby(["customer_id", "产品分类"]).agg({"付款": "sum"})

# 提示：
# 按 customer_id 和 category 分组，然后对 sales 列求和。
```



### 联合与合并数据集



\-pandas.merge: 可以根据单个或多个键（列名）将不同DataFrame中的 列 连接起来 （横向），可以联系SQL相关的关系型数据库，因为它实现的是数据库的join操作

\-pandas.concat: 拼接，沿一条轴将多个对象连接或堆叠到一起

此部分内容与数据库的左连接右连接内连接相似

\- 左连接，传入表a与表b，表a作为左表，整合数据包括a独有的和a与b的交集

\- 右连接，传入表a与表b，表b作为右表，整合数据包括b独有的和a与b的交集

\- 内连接，传入表a与表b，只包含a与b的交集部分即两者索行引相同的部分

\- 外连接，传入表a与表b，左右连接的并集



```python
# 数据集的合并或连接运算是通过单个或多个键将行连接起来的，类似数据库的 SELECT ... FROM 表1 JOIN 表2 ON ...
df1 = pd.DataFrame({"key": ["b", "b", "a", "c", "a","a", "b"], "data1": pd.Series(range(7), dtype="Int64")})
df2 = pd.DataFrame({"key": ["a", "b", "d"], "data2": pd.Series(range(3), dtype="Int64")})

# print(df1)
# print(df2)

pd.merge(df1, df2)
# # 这是多对1连接的实例，df1中的数据有多行的标签为a和b，df2中key列的每个值仅对应一行
# # 上面的例子没有指明要用哪个列进行连接，如果没有指定，pd.merge就会将重叠的列名当作键
pd.merge(df1, df2, on="key")  # 显示指定   SELECT * FROM 表1 JOIN 标2 ON (表1.列名1 == 表2.列名2)
#
# # 如果两个对象的列名不同，也可以分别指定
df3 = pd.DataFrame({"lkey": ["b", "b", "a", "c", "a","a","b"], "data1": pd.Series(range(7), dtype="Int64")})
df4 = pd.DataFrame({"rkey": ["a", "b", "d"], "data2": pd.Series(range(3), dtype="Int64")})

print(df3)
print(df4)

pd.merge(df3, df4, left_on="lkey", right_on="rkey")
#
# # merge之后 "c"和"d"以及与之相关的数据从结果里消失了，默认情况下，pandas.merge做的是内连接（inner），结果中的键(连接基于的列的值）是交集，或者两张表的公共集合
# #  其他连接方式：左连接（left），右连接（right), 外连接（outer）
# #  通过how关键字参数指定
#   left_on, right_on,  how="left"/"right"/"inner"/"outer" "full"

pd.merge(df3, df4, left_on="lkey", right_on="rkey", how="left")  # 表1 左连接 表2；  表1当成左边的表，包括表1存在  但是表2 不存在的值
pd.merge(df3, df4, left_on="lkey", right_on="rkey", how="right")  # 表1 右连接 表2；  表2当成右边的表；包括表2存在 但是表1不存在的值
pd.merge(df3, df4, left_on="lkey", right_on="rkey", how="outer")


# 连接在其中一个DataFrame或两个DataFrame中找到的值。
# Pandas 不介意某个值只存在于一个DataFrame中，而不存在于另一个DataFrame中。
# 如果某个值不存在于其中一个DataFrame中，则会返回 NaN

week1 = pd.read_csv("datasets/restaurant_week_1_sales.csv")
week2 = pd.read_csv("datasets/restaurant_week_2_sales.csv")

week1.head()
week2.head()

week1.merge(week2, how="outer", on="Customer ID", suffixes=[" - Week 1", " - Week 2"])
week1.merge(week2, how="outer", on="Customer ID", suffixes=[" - Week 1", " - Week 2"], indicator=True) # indicator: 加一列，记录每条记录存在于哪张表里

merged = week1.merge(week2, how="outer", on="Customer ID", suffixes=[" - Week 1", " - Week 2"], indicator=True)
merged["_merge"].value_counts()

merged[merged["_merge"].isin(["left_only", "right_only"])]  # 获取只存在于一张表里的记录
```

```python
# 如果两个 DataFrames 中要匹配的列具有相同的名称，则使用 on 参数。
# 如果两个 DataFrames 中要匹配的列具有不同的名称，则使用 left_on 和 right_on 参数。
# 如果要匹配的值位于 DataFrame 的行索引中，则使用 left_index 或 right_index 参数（设置为 True）。

foods = pd.read_csv("datasets/restaurant_foods.csv", index_col="Food ID")
customers = pd.read_csv("datasets/restaurant_customers.csv", index_col="ID")

customers.head()
foods.head()

week1.merge(
    customers, how="left", left_on="Customer ID", right_index=True
)

# week1.merge(
#     customers, how="left", left_on="Customer ID", right_index=True
# ).merge(foods, how="left", left_on="Food ID", right_index=True)


# join 方法是通过行索引标签合并两个 DataFrames 的快捷方式。
times = pd.read_csv("datasets/restaurant_week_1_times.csv")
times

week1.merge(times, how="left", left_index=True, right_index=True)
week1.join(times)
week1.join(times, how="inner")
```

```python
# 随堂练习：

import random

# 生成客户表
customer_ids = list(range(1001, 1011))  # 10 customers
names = ['Alice', 'Bob', 'Carol', 'Dave', 'Eve', 'Frank', 'Grace', 'Heidi', 'Ivan', 'Judy']
cities = ['New York', 'Los Angeles', 'Chicago', 'Houston', 'Phoenix']

customers = pd.DataFrame({
    'customer_id': customer_ids,
    'name': names,
    'city': [random.choice(cities) for _ in range(10)]
})

# 生成订单表
orders = []
for i in range(1, 21):  # 20 orders
    customer_id = random.choice(customer_ids + [1011, 1012])  # Add some unknown customer_ids
    product = random.choice(['Laptop', 'Book', 'Phone', 'T-Shirt', 'Backpack'])
    price = random.choice([1200, 30, 500, 25, 60])
    orders.append([i, customer_id, product, price])

orders = pd.DataFrame(orders, columns=['order_id', 'customer_id', 'product', 'price'])

# 打印预览
print("Customers:\n", customers.head())
print("\nOrders:\n", orders.head())
```

```python
# 1. 内连接：订单及客户详情
# 问题：合并订单和客户，仅显示现有客户的订单。
#
# 提示：基于 customer_id 进行连接
#
# 2. 左连接：保留所有订单，如有客户信息则添加。
# 问题：显示所有订单，包括未知客户的订单。
#
#
# 3. 右连接：显示所有客户及其匹配的订单。
# 问题：列出所有客户及其订单， 包括没有订单的客户
#
# 4. 外连接：显示所有客户和所有订单。
# 问题：将两个表完全合并，显示每个客户和每个订单。
# 提示：使用外连接。
#
# 5. 基于索引连接：以城市为索引的客户信息。
# 问题：假设customers表以城市为行索引。将其与另一个以城市为行索引的DataFrame进行连接。
#
# 提示：将城市设置为客户中的索引，然后加入它。
```

```python
##pd.concat 函数 将一个 DataFrame 连接到另一个 DataFrame 的末尾。
# 默认情况下，将保留原始索引标签。将 ignore_index 设置为 True 可生成新索引。
# keys 参数使用指定的键/标签创建层次化索引。

foods = pd.read_csv("datasets/restaurant_foods.csv")
customers = pd.read_csv("datasets/restaurant_customers.csv")
week1 = pd.read_csv("datasets/restaurant_week_1_sales.csv")
week2 = pd.read_csv("datasets/restaurant_week_2_sales.csv")

len(week1)
len(week2)

pd.concat([week1, week2])
# pd.concat([week1, week2], ignore_index=False)

pd.concat([week1, week2], ignore_index=True)  # 生成新的索引
pd.concat([week1, week2], keys=["Week 1", "Week 2"]).sort_index()  # keys列表匹配dataframe的顺序，生成层次化索引

# Pandas 会沿行/索引轴连接 DataFrames。
#  Pandas 会包含任一 DataFrame 中存在的所有列。如果没有匹配的值，Pandas 将使用 NaN 值。
# 我们可以向 axis 关键字传递一个 columns 参数，以便在列轴上进行连接。
df1 = pd.DataFrame([1, 2, 3], columns=["A"])
df1

df2 = pd.DataFrame([4, 5, 6], columns=["B"])
df2

pd.concat([df1, df2])  # 没有匹配的值, pandas使用NaN.
pd.concat([df1, df2], axis="index")

pd.concat([df1, df2], axis="columns")  # 连接列轴
```



## 绘图可视化

- 数据探索过程的一部分
- 找出异常值，必要的数据转换，得出建模的想法
- pip install matplotlib / conda install matplotlib



```python
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

data = np.arange(10)
data

plt.plot(data)
```

### 散点图和折线图

散点图plt.scatter  参数

​	\- **x**，横轴数据组成各个散点的横坐标

​	\- **y**，纵轴数据组成各个散点的纵坐标

​	\- **c**，颜色，传入字符串

​	\- **marker** 是点的类型，星形"*",圆形/点型"o"，叉形"x"，三角形"^"，正方形"s"

​	\- **s** size，大小指点的大小

​	\- **alpha=**  传入小数，表示点的透明度

折线图plt.plot参数

​	\- 先传入横轴坐标信息，再传入纵轴坐标信息两者相对应

​	\- **c、marker、s**与散点图一样

​	\- **linestyle = **  线的风格，"-"实线，"--"虚线，"dashed"虚线

​	\- **lw= 数字**  表示显得宽度

```python
# 散点图和折线图
X_data = np.random.random(50) * 100
Y_data = np.random.random(50) * 100

# plt.scatter(X_data, Y_data)  # 根据x，y画散点图
# plt.scatter(X_data, Y_data, c="red", marker="*", s=150)

# 透明度 alpha=0.3，可以体现数据的集中和分散程度，
# X_data = np.random.standard_normal(10000) * 100
# Y_data = np.random.standard_normal(10000) * 100
# plt.scatter(X_data, Y_data, c="red", marker="*", s=150, alpha=0.1)

years = [2006 + x for x in range(16)]
weights = np.random.randint(65, 80, 16)
plt.plot(years, weights, c="pink", lw=3, linestyle='--') # 画折线图  # c颜色，lw线的粗细, linestyle: 线的风格
# plt.scatter(years, weights)
plt.show()
```

### 柱状图和直方图

柱状图plt.bar 参数

​	\- x,y 同上

​	\- **color=** 颜色

​	\- **align="edge"/"centre"**，柱状图的位置

​	\- **width=** ，柱状图的宽度

​	\- **edgecolor=**，边缘的颜色

​	\- **lw= 数字** 边缘的宽度

直方图plt.hist()

​	\- 传入np数组作为data，统计数组再区间的个数

​	\- **cumulative=True/False**  是否累加

```python
# 柱状图和直方图
x = ["C++", "C#", "Python", "Java", "Go"]
y = [20, 50, 140, 1, 45]

# plt.bar(x,y)  # 柱状图
# plt.bar(x, y, color="r", align="edge", width=0.5, edgecolor="green", lw=6)

ages = np.random.normal(20, 1.5, 1000)  # 正态分布 20的数学期望，1.5的标准差，1000个数字
# plt.hist(ages, bins=20)
plt.hist(ages, bins=20, cumulative=True)
plt.show()
```

### 饼状图

饼状图plt.pie，参数：

​	\- **votes** 数据，plt会自己计算各组数据所占比重

​	\- **labels**  标签，是每个数据对应的内容，与votes一一对应

​	\- **autopct="%.1f%%"**  固定语法，显示每部分的所占百分比，并保留一位小数

​	\- **explode=[]**   列表内数据与votes一一对应，表示每个数据的离心程度

​	\- **startangle=90**  votes中的第一个数据开始的角度，并逆时针生成饼状图

```python
# 饼状图
langs = ["Python", "C++", "Java", "C#", "Go"]
votes = [50, 24, 14, 6, 17]

# plt.pie(votes, labels=langs)
plt.pie(votes, labels=langs, autopct="%.1f%%", explode=[0.3, 0, 0, 0, 0], startangle=90)
plt.show()
```

### 箱线图

箱线图: 展示数据的中位数（第二个 25%分位数）， 第一个25%分位数，第三个25%分位数，最大值，最小值，离群值

plt.boxplot()，直接传入一维数组

```python
# 箱线图: 展示数据的中位数（第二个 25%分位数）， 第一个25%分位数，第三个25%分位数，最大值，最小值，离群值
heights = np.random.normal(172, 8, 300)
# plt.boxplot(heights)

first = np.linspace(0, 10, 25)
second = np.linspace(100, 200, 25)
third = np.linspace(200, 210, 25)
fourth = np.linspace(210, 230, 25)

data = np.concatenate((first, second, third, fourth))
plt.boxplot(data)
plt.show()
```

### 图标信息添加

​	\- **plt.title()**   给图加标题

​	\- **plt.xlabel()**  给x轴（横轴）加标签

​	\- **plt.ylabel()**  给y轴（纵轴）加标签

​	\- **plt.yticks()**  列表，和列表推导式 可以给y轴加刻度

​	\- **plt.legend()**  给图加说明（图例），拥有关键字**loc=**来规定图例的位置，其有效的值为**'best', 'upper right', 'upper left', 'lower left', 'lower right', 'right', 'center left', 'center right', 'lower center', 'upper center', 'center'**

```python
# 给图加自定义的信息
years = np.arange(2014, 2022)
income = [55, 56, 62, 61, 72, 72, 73, 75]

income_ticks = list(range(50, 81, 2))
plt.plot(years, income)
plt.title("Income of X (in RMB)")  # 给图加标题
plt.xlabel("Year")     # x轴标签
plt.ylabel("Yearly Income in USD") # y轴标签
plt.yticks(income_ticks, [f"{x}k"  for x in income_ticks])  # y轴的刻度
plt.show()


# 自定义信息，多个plot
stock_a = [100, 102, 99, 101, 101, 100, 102]
stock_b = [90, 95, 102, 104, 105, 103, 109]
stock_c = [110, 115, 100, 105, 100, 98, 95]

plt.plot(stock_a, label="Company1")  # plt.plot(数组) 图的横轴是索引，纵轴是数值
plt.plot(stock_b, label="Company2")
plt.plot(stock_c, label="Company3")
plt.legend()  # 给图加说明  (图例）

plt.legend(loc="lower center")  # 把图例放到 中下部

plt.show()
```


### 图和子图
\-  matplotlib的图像位于Figure对象中，可以用plt.figure创建一张新图

\-  不能在空Figure对象上绘图，必须用add_subplot创建一个或多个subplot才能真正绘图

步骤：

	1. 通过给plt.figure()命名(fig)来获得可操作的可插入子图的对象
	1. fig.add_subplot() 传入三个数字，前两个数字确定确定子图分布，第一个数字是子图行数，第二个数字是子图的列数，这样总共会生成不超过**行数*列数**个子图，第三个数会确定当前子图的位置，位置从第一行第一列横向往下数
	1. 各个子图对象可以调用之前的绘图方法进行各种图形的绘制

```python
fig = plt.figure()

# add_subsplot, 添加子图  （一个画板，同时有多张子图）
#  2*2，4张子图，编号从1开始
ax1 = fig.add_subplot(2,2,1)
ax2 = fig.add_subplot(2,2,2)
ax3 = fig.add_subplot(2,2,3)
#
# # 重新执行每个代码框后，图形会被重置。需要所有的绘图命令放在同一代码框中
#
# 绘图轴对象（ax1，ax2，ax3）有多种方法用于创建不同类型的图 使用轴的plot方法绘制线形图
ax3.plot(np.random.standard_normal(50).cumsum(), color="black", linestyle="dashed")  # 区别plt.plot(), ax3.plot()直接画到对应的子图上
#
# [<matplotlib.lines.Line2D at ...]的输出可以忽略，或者代码末尾加分号 / plt.show()屏蔽
# color="black", linestyle="dashed"指要绘制的是黑色虚线

# 调用ax1和ax2的实例方法，在其他的空格子里画图
ax1.hist(np.random.standard_normal(100), bins=20, color="black", alpha=0.5)  # alpha=0.5 设置图像透明度
ax2.scatter(np.arange(30), np.arange(30) + 3*np.random.standard_normal(30))
plt.show()
```

另一种子图绘制方法`plt.subplots`

plt.subplots, 可以创建一个新的图形对象，返回一个NumPy数组，其中包含创建好的子图对象

`plt.tight_layout()`  子表的横坐标可能和其余子表的表头堆叠，此时可以用这个函数解决

`fig, axes = plt.subplots(2,2)`返回的两个可操作对象

**fig**

​	\-  **suptitle()**   传入字符串，作为整个表的表名

**axes**  各个可操作的图表对象可以用索引来获得

​	\- **.set_title("表名")**  给子表命名	

**plt.savefig(path, format=fig_extension, dpi=resolution) ** # 保存图片到本地

​	\- **path**是存放的路径

​	\- **format=** 是图片存储的格式，"jpg"，"png"等

​	\- **dpi=300**  是图片的清晰度

```python
# 更方便创建网格化子图：plt.subplots, 可以创建一个新的图形对象，返回一个NumPy数组，其中包含创建好的子图对象
fig, axes = plt.subplots(2,2)
# print(fig)
# print(axes)
# print(type(axes))

x = np.linspace(-20, 20, 200)
axes[0,0].plot(x, np.sin(x))  # plt.plot(x, y)
axes[0,0].set_title("sin func")  # 每个子图可以自定义信息   # plt.title(
#
axes[0,1].plot(x, np.cos(x))
axes[0,1].set_title("cos func")
#
axes[1,0].plot(x, 1 / (1 + np.exp(-x)))
axes[1,0].set_title("sigmoid func")
#
axes[1,1].plot(x, x**2)
axes[1,1].set_title("parabola func")
#
fig.suptitle("Four plots")  # 整个图的大标题

plt.tight_layout()  # 解决下标题的堆叠

# plt.savefig(path, format=fig_extension, dpi=resolution)  # 保存图片到本地
# plt.savefig(路径, format="png", dpi=300）)

# 随堂练习，画sigmoid函数 ： f(x) = 1/(1+e**(-x)); 图像命名为sigmoid func，这个图替掉左下角的子图


plt.show()
```





## 使用pandas绘图
pandas自身有内置的方法，简化针对DataFrame和Series的绘图

Series

```python
# plot方法，生成折线图
s = pd.Series(np.random.standard_normal(10).cumsum(), index=np.arange(0, 100, 10))
s

# Series的索引会被传给matplotlib, 并用于绘制x轴，可以通过在plot方法内设置use_index=False禁用这个功能
ax = s.plot(use_index=False)
plt.show()

# series.plot方法的参数：
# label: 用作图的说明的标签
# ax：绘图所用的matplotlib的子图对象，如果没有设置，则使用当前的matplotlib的子图
# title: 图的标题
# use_index: 是否将对象的索引用作刻度标签
# xlabel: x轴的名称
# ylabel: y轴的名称
# xticks: 用做x轴刻度的值
# yticks: 用做y轴刻度的值
# xlim: x轴的界限（例如 [0,10])
# ylim: y轴的界限
# logx: 传入True，则x轴使用对数缩放
# logy: 传入True，则y轴使用对数缩放
```

DataFrame

```python
# DataFrame的plot方法将各列绘制成同一子图中不同的线，并自动创建图例 (图的说明)
df = pd.DataFrame(np.random.standard_normal((10,4)).cumsum(0), columns=["A", "B", "C", "D"], index=np.arange(0, 100, 10))
df
df.plot()
plt.show()

# DataFrame有一些用于对列进行灵活处理的选项，例如，要将所有列都绘制到同一个子图，还是分别创建各自的子图
# subplots: 将DataFrame的列绘入不同的子图
# layout: 用于子图布局的二元组（行，列)
# sharex: 如果subplots=True, sharex=True则共享x轴，关联刻度和界限
# sharey: 如果subplots=True, sharey=True则共享y轴，关联刻度和界限
# legend: 添加子图图例（默认为True)
# sort_columns: 以字母顺序绘制列；默认只用列已有的顺序

df.plot(subplots=True, layout=(2,2), sharex=True, sharey=True)
plt.show()

```



```python
# 不同的绘图类型，plot属性会包含很多方法，df.plot()等价于df.plot.line()
# plot.bar()和plot.barh()分别用于绘制水平柱状图和垂直柱状图

fig, axes = plt.subplots(2,1)
data = pd.Series(np.random.uniform(size=16), index=list("abcdefghijklmnop"))

data.plot.bar(ax=axes[0], color="black", alpha=0.7)
data.plot.barh(ax=axes[1], color="black", alpha=0.7)
plt.show()

# 对于DataFrame，柱状图会将每一列的值分为一组，并排显示
df = pd.DataFrame(np.random.uniform(size=(6,4)), index=["one", "two", "three", "four", "five", "six"],
                  columns=pd.Index(["A", "B", "C", "D"], name="Testing"))
# print(df)

# df.plot.bar()
# df.plot.bar(subplots=True)  # 画到不同的子图
df.plot.bar(legend="upper right")  # 调整图例的位置
plt.show()

    # 传入stacked=True,即可为DataFrame生成堆积柱状图，这样每列的值就会水平堆积在一起
df.plot.barh(stacked=True, alpha=0.5)
plt.show()
```



```python
## 可视化demo
# 使用value_counts对Series的各值出现频率进行可视化非常有效
emoji_en_map = {
    "😂": "laugh",
    "❤️": "love",
    "👍": "thumbs_up",
    "🔥": "fire",
    "🥰": "in_love",
    "😭": "cry",
    "👏": "clap",
    "😊": "smile",
    "🤔": "thinking",
    "🎉": "party"
}

rng = np.random.default_rng(100)
ser = pd.Series(rng.choice(list(emoji_en_map.values()), size=500), name="emoji")
ser

ser.value_counts().plot.bar(color="green")
plt.show()

```





## 可视化demo（未搞懂）



```python
# 1. 获取数据
from pathlib import Path
import pandas as pd
import tarfile
import urllib.request


def load_housing_data():  # 网上下载了压缩文件，返回了pandas的DataFrame
    # tarball_path = Path("datasets/housing.tgz")  # 文件路径的对象 Path
    # if not tarball_path.is_file():
    #     Path("datasets").mkdir(parents=True, exist_ok=True)
    #     url = "https://github.com/ageron/data/raw/main/housing.tgz"
    #     urllib.request.urlretrieve(url, tarball_path)  # url下载文件，到本地目录
    # with tarfile.open(tarball_path) as housing_tarball:  #
    #         housing_tarball.extractall(path="datasets")  # 解压缩文件
    return pd.read_csv(Path("datasets/housing/housing.csv"))

housing = load_housing_data()
```

```python
# 2. 简单看下数据
# housing["median_income"].corr(housing["median_house_value"])
# housing.head()
housing.info()
housing.describe()
housing["ocean_proximity"].value_counts()
```

```python
#  3. 定义一个保存图片的函数，如果要保存图片，直接调用

IMAGES_PATH = Path() / "images" / "pandas_visualization_demo"
IMAGES_PATH.mkdir(parents=True, exist_ok=True)

def save_fig(fig_id, tight_layout=True, fig_extension="png", resolution=300):
    path = IMAGES_PATH / f"{fig_id}.{fig_extension}"
    if tight_layout:
        plt.tight_layout()
    plt.savefig(path, format=fig_extension, dpi=resolution)
```

```python
# 4. 可视化数据： 画出每个数值列的直方图
import matplotlib.pyplot as plt
plt.rcdefaults()   # 恢复绘图相关变量为默认


# housing.plot.hist()
# housing.plot.hist(subplots=True, layout=(3,3), bins=50, sharex=False, sharey=False)

# 表格.hist(bins,figsize)
housing.hist(bins=50, figsize=(12, 8))
plt.show()
```

```python
# 5. 清洗数据
# housing["total_bedrooms"].isna().any()

housing_features =housing.drop("median_house_value", axis="columns")
housing_labels = housing["median_house_value"].copy()

housing_labels

# 1. 删掉对应NaN数据
housing_features.dropna(subset=["total_bedrooms"])
# 2. 删掉整个属性
housing_features.drop("total_bedrooms", axis="columns")

# 3. 填充中位数，0，平均数 (归责）
median = housing_features["total_bedrooms"].median()
housing_features["total_bedrooms"] = housing_features["total_bedrooms"].fillna(median)
housing_features
#
housing_features["total_bedrooms"].notna().all()
```

```python
# 6. 对收入列，按收入范围分箱，并画图每个箱的频率
housing_features["income_cat"] = pd.cut(housing_features["median_income"], bins=[0., 1.5, 3.0, 4.5, 6, np.inf], labels=[1,2,3,4,5])
housing_features["income_cat"]

# housing_features["income_cat"].value_counts().sort_index()
housing_features["income_cat"].value_counts().sort_index().plot.bar(rot=8, grid=True)
# rot=rotation, 设定刻度标签的倾斜角度，这里x轴下方的标签倾斜角度为0度
# grid=True/False，是否打开网格线显示
plt.show()
```

```python
# 7. 可视化数据的经纬度
# housing_features.plot.scatter(x="longitude", y="latitude", grid=True)
# housing_features.plot(kind="scatter", x="longitude", y="latitude", grid=True)  # kind=绘图类型（直方图，散点图，直线图）

housing_features.plot(kind="scatter", x="longitude", y="latitude", grid=True, alpha=0.2)  # 让散点透明  直接体现数据的集中程度
plt.show()
```

```python
# 8. 通过散点图，可视化更多信息：
# 1. 人口越多的点越大
# housing_features.plot(kind="scatter", x="longitude", y="latitude", grid=True, s=housing["population"]/100)

# 2. 房价中位数越贵的地方，颜色越红，房价越便宜的地方，颜色越蓝
#   注意这里要用 有标签（房价中位数）的数据


# data_frame.plot(kind=xxx, x,y,c都是指定成data_frame的列名)
# housing.plot(kind="scatter", x="longitude", y="latitude", grid=True, c="median_house_value", cmap="jet", colorbar=True)

# 3. 给散点添加标签，用于图例，结合1和2
housing.plot(kind="scatter", x="longitude", y="latitude", grid=True, s=housing["population"]/100, label="population", c="median_house_value",cmap="jet", colorbar=True, legend=True, sharex=False, figsize=(10,7))
plt.show()
```



| 参数                          | 取值          | 作用与可视化结果                                             |
| ----------------------------- | ------------- | ------------------------------------------------------------ |
| `kind="scatter"`              | 字符串        | 指定绘图类型为 **散点图**；等价于 `housing.plot.scatter(...)`。 |
| `x="longitude"`               | 列名          | scatter 的 **x 轴坐标** 取自 `housing["longitude"]`。        |
| `y="latitude"`                | 列名          | scatter 的 **y 轴坐标** 取自 `housing["latitude"]`。         |
| `grid=True`                   | 布尔          | 显示背景网格线（默认灰色虚线）。                             |
| `s=housing["population"]/100` | 序列或标量    | 点的 **面积(size)**；这里人口数除以 100，使人口大的区县圆点更大。 |
| `label="population"`          | 字符串        | 给这一组散点添加标签，用于图例。                             |
| `c="median_house_value"`      | 字符串 / 数组 | 点的 **颜色值**；取自列 `median_house_value`。               |
| `cmap="jet"`                  | 颜色映射      | 选用 Matplotlib 的 **Jet** 渐变色盘，将房价数值映射到蓝→红。 |
| `colorbar=True`               | 布尔          | 在图旁显示一个 **颜色条**，说明颜色与房价的对应关系。        |
| `legend=True`                 | 布尔          | 显示图例；此处图例仅会标注 `'population'` 这一组散点。       |
| `sharex=False`                | 布尔          | 若与其它子图同处一 Figure，**不共用** x 轴刻度（独立缩放）。 |
| `figsize=(10, 7)`             | 元组(宽,高)   | 整个图像画布大小，以英寸计；这里宽 10 inch，高 7 inch。      |



```python
# 9. 利用可视化的观察，对数据变换
# housing_features["population"].hist(bins=50)

# 当一个特征的分布有 重尾（heavy tail)时，远离平均值的值不是指数级稀有，缩放特征后它也是重尾
# 缩放特征前，对特征进行变换，缩小重尾，使得分布大致对称
#  右侧重尾，可以用log和平方根来替换特征
# np.log(housing_features["population"]).hist(bins=50)
np.sqrt(housing_features["population"]).hist(bins=50)

plt.show()
```







## pandas的时间序列分析

时间序列数据是一种重要的结构化数据形式，应用于多个领域，包括金融学、经济学、生态学、神经科学、物理学等。在多个时间点重复记录的值就形成了时间序列。很多时间序列是固定频率的，也就是说，数据点是根据某种规则定期出现的，比如每15秒、每5分钟或每月一次。时间序列也可以是不定期的，没有固定的时间单位或单位之间的偏移量。如何标记和引用时间序列数据取决于具体的应用，主要有以下几种：
- 时间戳特定的时刻。
- 固定的周期如2017年1月整月或2020年全年。
- 时间区间由起始时间戳和结束时间戳表示。周期可以看作时间区间的特例。
- 试验时间或运行时间每个时间戳都是相对于特定起始时间的度量（例如，饼干放入烤箱后每秒烘烤的直径）,从0开始

主要讲解前三种时间序列。许多技术都可用于处理试验时间序列，其索引可能是整数或浮点数，表示距试验起始时刻已经过去的时间。最简单的时间序列是按时间戳索引的。

pandas提供了许多内置的时间序列处理工具和算法。因此，你可以高效地处理大型时间序列，轻松地进行切片/切块、聚合，以及对不定期或固定频率的时间序列进行重采样等。其中一些工具特别适合金融和经济领域，当然你也可以用它们来分析服务器日志数据。

### 日期和时间数据的类型及工具
Python标准库包含用于日期和时间数据的数据类型，并且还包含日历相关的功能。我们主要会用到datetime、time以及calendar模块。datetime.datetime（简写为datetime）是使用广泛的数据类型：

```python
from datetime import datetime
now = datetime.now()
now
now.year, now.month, now.day
```

```python
delta = datetime(2011, 1, 7) - datetime(2008, 6, 24, 8, 15)
delta   # 产生的时间差对象包含 天数 和 秒数
delta.days
delta.seconds
```

可以给datetime对象加上（或减去）一个或多个timedelta，这样会生成一个新的日期对象：

```python
from datetime import timedelta
start = datetime(2011, 1, 7)
start + timedelta(12)
start - 2 * timedelta(12)
```

datetime模块中的数据类型。虽然本章主要讲的是pandas数据类型和高级时间序列处理，但你肯定会在其他的Python使用场景碰到基于datetime的数据类型。

| 类名      | 功能说明                                         |
| --------- | ------------------------------------------------ |
| date      | 日期对象（包含年月日）                           |
| time      | 时间对象（包含时，分，秒，微秒（毫秒的下一级）） |
| datetime  | 日期时间对象（即date与time）                     |
| timedelta | 时间间隔（天，秒，微秒）                         |
| tzinfo    | 时区信息对象                                     |



### 字符串和datetime的相互转换

利用str或strftime方法并传入指定格式，可以将datetime对象和pandas的Timestamp对象（稍后就会介绍）格式化为字符串：

```python
stamp = datetime(2011, 1, 3)
str(stamp)
stamp.strftime("%Y-%m-%d")

# %Y: 四位数的年
# %y: 两位数的年
# %m: 两位数的月
# %d：两位数的日
```



| 符号 | 说明                                                  |
| ---- | ----------------------------------------------------- |
| %y   | 两位数的年份表示（00-99）                             |
| %Y   | 四位数的年份表示（000-9999）                          |
| %m   | 月份（01-12）                                         |
| %d   | 月内中的一天（0-31）                                  |
| %H   | 24小时制小时数（0-23）                                |
| %I   | 12小时制小时数（01-12）                               |
| %M   | 分钟数（00=59）                                       |
| %S   | 秒（00-59）                                           |
| %a   | 本地简化星期名称                                      |
| %A   | 本地完整星期名称                                      |
| %b   | 本地简化的月份名称                                    |
| %B   | 本地完整的月份名称                                    |
| %c   | 本地相应的日期表示和时间表示                          |
| %f   | 整数形式的微秒，零填充（000000到999999）              |
| %j   | 年内的一天（001-366）                                 |
| %p   | 本地A.M.或P.M.的等价符                                |
| %u   | 用整数表示星期几，从1开始，1是星期一                  |
| %U   | 一年中的星期数（00-53）星期天为星期的开始             |
| %w   | 星期（0-6），星期天为星期的开始                       |
| %W   | 一年中的星期数（00-53）星期一为星期的开始             |
| %x   | 本地相应的日期表示                                    |
| %X   | 本地相应的时间表示                                    |
| %z   | 以+HHMM或-HHMM表示的UTC时区偏移量，如果没有时区则为空 |
| %Z   | 当前时区的名称                                        |
| %%   | %号本身                                               |
| %F   | %Y-%m-%d的快捷缩写                                    |
| %D   | %m/%d/%y的快捷缩写                                    |

使用datetime.strptime和这些格式编码, 可以将字符串转换为日期：

```python
value = "2011-01-03"
datetime.strptime(value, "%Y-%m-%d")
datestrs = ["7/6/2011", "8/6/2011"]
[datetime.strptime(x, "%m/%d/%Y") for x in datestrs]

# pandas通常用于处理日期数组，不管这些日期是DataFrame的轴索引还是列。
datestrs = ["2011-07-06 12:00:00", "2011-08-06 00:00:00"]
pd.to_datetime(datestrs)

# pandas.to_datetime方法还可以处理应当作为缺失值的值（None、空字符串等)
idx = pd.to_datetime(datestrs + [None])
idx
idx[2]
pd.isna(idx)
```





### 时间序列基础知识
pandas最基本的时间序列类型就是以时间戳为索引的Series，时间戳通常以Python字符串或datatime对象表示：

```python
dates = [datetime(2011, 1, 2), datetime(2011, 1, 5),
         datetime(2011, 1, 7), datetime(2011, 1, 8),
         datetime(2011, 1, 10), datetime(2011, 1, 12)]
ts = pd.Series(np.random.standard_normal(6), index=dates)
ts

# 在pandas内部, python的datetime对象 被放进DatetimeIndex里
ts.index
```

与其他Series一样，不同索引的时间序列之间的算术运算会自动按日期对齐：

```python
ts + ts[::2] # ts[::2]是每隔两个值取一个值
ts + ts.iloc[::2]

ts.index.dtype  # pandas用NumPy的datetime64数据类型，以纳秒精度存储时间戳：
stamp = ts.index[0]  # DatetimeIndex中的各个标量值是pandas的Timestamp对象：
stamp  #Timestamp('2011-01-02 00:00:00')
```

只要有需要，pandas.TimeStamp可以随时自动转换为datetime对象。但是，pandas.TimeStamp不能进行逆运算，因为它存储的是纳秒精度的数据，而datetime只能存储到微秒级别。此外，pandas.TimeStamp还可以存储频率信息（如果有的话），并可以执行时区转换以及其他操作



#### 索引、选取、子集构造
当根据标签进行索引和选取数据时，时间序列和其他的Series很像：

```python
stamp = ts.index[0]
stamp
ts.loc[stamp]
# 为了方便，也可以传入一个可以解释为日期的字符串：
ts.loc["2011-01-10"]

# 对于较长的时间序列，只需传入“年”或“年月”即可轻松选取数据的切片 date_range之后详解
longer_ts = pd.Series(np.random.standard_normal(1000),
                      index=pd.date_range("2000-01-01", periods=1000))
longer_ts
longer_ts["2001"] #  字符串"2001"被解释为年，并选取出该时间范围的数据
longer_ts["2001-05"] # 如果指定月份，也可以选取数据：

# datetime对象也可以进行切片：
ts[datetime(2011, 1, 7):]
ts[datetime(2011, 1, 7):datetime(2011, 1, 10)]

# 由于大部分时间序列数据都是按照时间先后排序的，因此也可以用不存在于该时间序列中的时间戳对其进行范围查询：
ts
ts["2011-01-06":"2011-01-11"]

# 还有一个等价的实例方法truncate，也可以截取两个日期之间的Series：
ts.truncate(after="2011-01-09")
ts.truncate(before="2011-01-06", after="2011-01-11")

# 所有这些操作对DataFrame也有效，并对DataFrame的行进行索引：
dates = pd.date_range("2000-01-01", periods=100, freq="W-WED")
long_df = pd.DataFrame(np.random.standard_normal((100, 4)),
                       index=dates,
                       columns=["Colorado", "Texas",
                                "New York", "Ohio"])
long_df.loc["2001-05"]
```



#### 带有重复索引的时间序列
在某些应用场景中，可能会存在多个观测数据落在同一个时间点上的情况。下面就是一个例子：

```python
dates = pd.DatetimeIndex(["2000-01-01", "2000-01-02", "2000-01-02",
                          "2000-01-02", "2000-01-03"])
dup_ts = pd.Series(np.arange(5), index=dates)
dup_ts

dup_ts.index.is_unique

# 对这个时间序列进行索引，取决于时间戳是否重复，要么生成标量值，要么生成切片：
dup_ts["2000-01-03"]  # 不重复
dup_ts["2000-01-02"]  # 重复
```

假设你想对具有重复时间戳的数据进行聚合，一个办法是使用groupby，并传入level=0（存在的唯一层级）：

```python
grouped = dup_ts.groupby(level=0)
grouped.mean()
grouped.count()
```



#### dt属性

 - `dt` 属性会显示一个 `DatetimeProperties` 对象，其中包含用于处理日期时间的属性/方法。它类似于用于字符串方法的 `str` 属性。
- `DatetimeProperties` 对象具有 `day`、`month` 和 `year` 等属性，用于显示 **Series** 中每个日期的信息。
- `day_name` 方法返回星期几。
- `is_month_end` 和 `is_quarter_start` 等属性返回布尔值 **Series**



```python
bunch_of_dates = pd.Series(pd.date_range(start="2000-01-01", end="2020-12-31", freq="24D 3h"))
bunch_of_dates

bunch_of_dates.dt.day   
bunch_of_dates.dt.month
bunch_of_dates.dt.year
bunch_of_dates.dt.hour
bunch_of_dates.dt.day_of_year  # 返回当前日期是这一年中的第几天(0-366)
bunch_of_dates.dt.day_name()  # 星期几
#
bunch_of_dates.dt.is_month_end
bunch_of_dates.dt.is_month_start
#

bunch_of_dates.loc[bunch_of_dates.dt.is_quarter_start]  # 默认 Q_DEC,即以12月为一年的最后一个季度，1-3第一季度，4-6第二季度，7-9第三季度，10-12第四季度
bunch_of_dates.loc[bunch_of_dates.dt.is_month_end]
```



### 日期的范围，频率以及移位
pandas中的原生时间序列一般被认为是不规则的，也就是说，它们没有固定的频率。对于大部分应用程序而言，这是无所谓的。但是，经常需要以某种相对固定的频率进行分析，比如每日、每月、每15分钟等（但这样可能会在时间序列中导入缺失值）。幸好，pandas有一整套标准时间序列频率以及用于重采样、频率推断、生成固定频率日期范围的工具。例如，通过调用resample方法，可以将样本时间序列转换为具有固定日频率的序列：

```python
ts
resampler = ts.resample("D")
resampler

# 字符串"D"被解释为每日的频率
```

#### 生成日期范围
pandas.date_range还可以用于根据指定频率生成特定长度的DatetimeIndex

```python
# pd.date_range(开始日期的字符串，结束日期的字符串)  -> DatetimeIndex

index = pd.date_range("2012-04-01", "2012-06-01")
index
```

默认情况下，pandas.date_range会生成每日的时间戳。如果只传入起始日期或结束日期，还必须传入要生成的周期个数：

​	\- **periods = 数字**  周期，表示时间范围总共有多少个，配合start则生成start后面的时间索引，配合end生成end前面的时间索引，默认时间间隔是一天

```python
# pd.date_range(start="2012-04-01", periods=20)
pd.date_range(end="2012-06-01", periods=20)  # periods=n
# DatetimeIndex(['2012-05-13', '2012-05-14', '2012-05-15', '2012-05-16',
#                '2012-05-17', '2012-05-18', '2012-05-19', '2012-05-20',
#                '2012-05-21', '2012-05-22', '2012-05-23', '2012-05-24',
#                '2012-05-25', '2012-05-26', '2012-05-27', '2012-05-28',
#                '2012-05-29', '2012-05-30', '2012-05-31', '2012-06-01'],
#               dtype='datetime64[ns]', freq='D')
```

起始日期和结束日期定义了日期索引的严格边界。例如，如果你想生成一个由每月最后一个工作日组成的日期索引，可以传入频率"BME"（表示business end of month），这样就只会包括时间区间内符合频率要求的日期：



```python
#  每个月的最后一个工作日  freq="BME"

#   # date_range: 开始，结束，周期数（periods), freq(频率，隔多久生成一个日期）
pd.date_range("2000-01-01", "2000-12-01", freq="BME")  # BME(business, month, end)

pd.date_range("2025-01-01", "2025-07-01", freq="WOM-3SUN")  # 按每个月第三个星期日的频率 从今年1月1号 到7月1号 生成日期索引
```

| 别名     | 偏移量类型  | 说明         |
| -------- | ----------- | ------------ |
| D        | Day         | 日历的每天   |
| B        | BusinessDay | 工作日的每天 |
| h        | Hour        | 每时         |
| T 或 min | Minute      | 每分         |
| L 或 ms  | Milli       | 每毫秒       |

总共的偏移量类型：

| 别名                                                         | 偏移量类型            | 说明                                                         |
| ------------------------------------------------------------ | --------------------- | ------------------------------------------------------------ |
| `U` (舍弃)/ `us`                                             | micro                 | 每微秒                                                       |
| `L`(舍弃)/`ms`                                               |                       | 每毫秒                                                       |
| `N`(舍弃)/`ns`                                               |                       | 每纳秒                                                       |
| `S`(舍弃)/`s`                                                | second                | 每秒                                                         |
| `T`(舍弃)/`min`                                              | minute                | 每分钟                                                       |
| `H`(舍弃)/`h`                                                | hour                  | 每小时                                                       |
| `BH`(舍弃)/`bh`                                              | businesshour          | 每个工作日的工作时间，朝九晚五，为17:00时自动跳转到下一个工作日的9:00 |
| `D`                                                          | day                   | 每天（公历日）                                               |
| `C`/`B`                                                      | business              | 工作日的每天                                                 |
| `W [-MON,TUE,WED,THU,FRI,SAT,SUN]`                           | week                  | 每周，也可以指定每周的星期几形成固定偏移量，当只输入`W`时，其指代的是默认的`W-SUN` |
| `M`(舍弃)/`ME`                                               | monthend              | 每个月最后一个公历日                                         |
| `MS`                                                         | monthstart            | 每个月第一个公历日                                           |
| `SM`(舍弃)/`SME`                                             |                       | 每月月中和月末                                               |
| `SMS`                                                        |                       | 每月月初和月中                                               |
| `BM`或`CBM`(舍弃)/`BME`或`CBME`                              | businessmonthend      | 每月最后一个工作日                                           |
| `BMS`或`CBMS`                                                | businessmonthstart    | 每月第一个工作日                                             |
| `Q`(舍弃)/`QE [-JAN, FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV,DEC]` | quarter               | 每季度最后一个月的最后一个公历日，QE默认是QE-DEC             |
| `BQ`(舍弃)/`BQE [-JAN, FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV,DEC]` | bussinessquarterend   | 每季度最后一个月的最后一个工作日，BQE默认是BQE-DEC           |
| `QS[-JAN, FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV,DEC]`      | quarterstart          | 每季度最后一个月的第一个公历日，QS默认是QS-JAN               |
| `BQS[-JAN, FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV,DEC]`     | bussinessquarterstart | 每季度最后一个月的第一个工作日，BQS默认是BQS-JAN             |
| `Y`或`A`(舍弃)/`YE[-JAN, FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV,DEC]` | year                  | 每年指定月份的最后一个日历日，YE默认是YE-DEC                 |
| `BA`或`BY`(舍弃)/`BYE[-JAN, FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV,DEC]` | bussinessyear         | 每年指定月份的最后一个工作日                                 |
| `AS`(舍弃)/`YS[-JAN, FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV,DEC]` | yearstart             | 每年指定月份的第一个日历日                                   |
| `BAS`(舍弃)/`BYS[-JAN, FEB,MAR,APR,MAY,JUN,JUL,AUG,SEP,OCT,NOV,DEC]` |                       | 每年指定月份的第一个工作日                                   |
| `WOM[-数字n MON,TUE,WED,THU,FRI,SAT,SUN]`                    |                       | 每个月第n周的星期几                                          |



统一时间戳normalize = True

```python
# pandas.date_range默认会保留起始时间戳和结束时间戳的时间信息:
pd.date_range("2012-05-02 12:56:31", periods=5)

# 有时，虽然起始日期和结束日期带有时间信息，但你希望生成一组标准化到晚间零点的时间戳，normalize选项可以实现该功能：
pd.date_range("2012-05-02 12:56:31", periods=5, normalize=True)
```

```python
# 随堂练习，生成1949年10月1号 到 今年10月1号的daterange, 提示：用pd.DateOffset精准偏移一年; 或者AS-OCT把每年定到10月开头
# pd.date_range("1949-10-01", "2025-10-01", freq="YS-OCT")

# freq 想传一个 一年的间隔（偏移）   pd.DateOffset()
pd.date_range("1949-10-01", "2025-10-01", freq=pd.DateOffset(years=1))

```

#### 频率和日期偏移量

pandas中的频率是由基础频率和倍数组成的。基础频率通常以一个字符串别名表示，比如"M"表示每月，"H"表示每小时。对于每个基础频率，都有一个称为日期偏移量的对象与之对应。例如，每小时的频率可以用Hour类表示：

```python
# 日期偏移量对象 Pandas  pandas.tseries.offsets

from pandas.tseries.offsets import Hour, Minute, BusinessDay
hour = Hour()
# pd.date_range("1949-10-01", "2025-10-01", freq=pd.DateOffset(years=1)) + hour

# 传入一个整数即可定义偏移量的倍数
four_hours = Hour(4)  # "4h", Hour 是pandas.tseries.offsets 里面的一个类
four_hours

# pd.date_range("1949-10-01" "2025-10-01", freq=pd.DateOffset(years=1)) + four_hours


work_day = BusinessDay(1)
# pd.date_range("1949-10-01" "2025-10-01", freq=pd.DateOffset(years=1)) + work_day
# 对于大多数应用，无须显式创建这样的对象，只需使用如"H"或"4H"这样的字符串别名。在基础频率前面加上一个整数即可创建倍数：
# pd.date_range("2000-01-01", "2000-01-03 23:59", freq="4h")
pd.date_range("2000-01-01", "2000-01-03 23:59", freq=four_hours) + work_day


# 大部分偏移量对象都可通过加法进行连接：
Hour(2) + Minute(30)

# 同理，也可以传入频率字符串，比如"2h30min"，它可以被高效地解析为等效的表达式：
pd.date_range("2000-01-01", periods=10, freq="2h30min")
```

有些频率所描述的时间点并不是均匀分隔的。例如，"ME"（每月最后一个日历日）和"BME"（每月最后一个工作日）就取决于每月的天数，对于后者，还要考虑月末是不是周末。我将其称为锚定偏移量。

```python
# “月中某星期”(Week Of Month)是一种非常实用的频率类，它以WOM开头，使你能获得诸如“每月第3个星期五”之类的日期：
monthly_dates = pd.date_range("2012-01-01", "2012-09-01", freq="WOM-3FRI")
monthly_dates
list(monthly_dates)
```



#### 对超前和滞后数据进行移位
移位(shifting)是指沿着时间轴将数据前移或后移。Series和DataFrame都有一个shift方法，用于执行单纯的前移或后移操作，并保持索引不变：

```python
np.random.seed(100)
ts = pd.Series(np.random.standard_normal(4),
               index=pd.date_range("2000-01-01", periods=4, freq="ME"))
print(ts)
ts.shift(2)  # series.shift(数字)  这里相当于把数据往前推了2个单位 即数据在2000-03-31开始，到2000-04-30结束，虽然会舍弃两个值但是不会产生新的时间索引
ts.shift(-2)
# # 当这样进行移位时，就会在时间序列的前面或后面产生缺失数据
#
# # shift通常用于计算一个时间序列或多个时间序列（如DataFrame的列）中的连续百分比变化。可以这样表达：
ts.shift(1)
1- ts / ts.shift(1)


# 由于单纯的移位操作不会修改索引，因此部分数据会被丢弃。如果频率已知，则可以将其传给shift以便实现对时间戳进行移位，而不是对数据进行简单位移：

#   为了让索引整体位移， 数据不变
ts.shift(2, freq="ME")

# # 这里还可以传入其他频率，于是你就能非常灵活地对数据进行超前处理和滞后处理了：
ts.shift(3, freq="D")
ts.shift(1, freq="90min")

# 这里的min代表的是分钟。注意，参数freq表明移位是针对时间戳的，但没有修改底层的数据频率（如果存在的话)
```



#### 通过偏移量对日期进行移位
pandas的日期偏移量还可以用在datetime或Timestamp对象上:



```python
from pandas.tseries.offsets import Day, MonthEnd,BusinessMonthEnd
now = datetime(2011, 11, 17)
now + 3 * Day(), type(now + 3 * Day())

# 如果加的是锚定偏移量（比如MonthEnd），第一次增量会将原日期“向前滚动”到符合频率规则的下一个日期：


now + MonthEnd()  # -> 11.30
now + MonthEnd() + MonthEnd()
now + 2*MonthEnd()
#
# # 通过锚定偏移量的rollforward和rollback方法，可显式地将日期向前或向后“滚动”:
#  偏移量对象.rollforward(日期对象） -> 可显式地将日期向前或向后“滚动”: 生成一个新的日期对象 (pd.TimeStamp)
offset = MonthEnd()

now = datetime.now()
offset.rollforward(now)   # 前滚就是去到未来
offset.rollback(now)    # 后滚就是去到过去

offset2 = BusinessMonthEnd()
offset.rollforward(now)
```

```python
# 日期偏移量还有一个巧妙的用法，即结合groupby使用这两个“滚动”方法：
# groupby第一个参数可以是函数，函数作用：索引标签——>返回值，根据返回值对数据分组
ts = pd.Series(np.random.standard_normal(20),
               index=pd.date_range("2000-01-15", periods=20, freq="4D"))
ts


# for day in ts.index:
#     print(MonthEnd().rollforward(day))


ts.groupby(MonthEnd().rollforward).mean()


# for day in ts.index:
#     print(MonthEnd().rollforward(day))
```



### 周期及其算术运算
周期表示的是时间段，比如数日、数月、数季、数年等。pandas.Period类所表示的就是这种数据类型，它需要用到字符串或整数，以及频率表

```python
p = pd.Period("2011", freq="Y-DEC")  # Y-DEC → “年度频率，年底在 12 月” —> 一个 Period = 整年度。
p

# 在这个例子中，Period对象表示的是从2011年1月1日到2011年12月31日之间的整段时间。只需对周期对象加上或减去一个整数，就可以移动其周期：
p+5
p-2
#
# 如果两个周期对象拥有相同的频率，则二者的差就是它们之间日期偏移量的单位数量：
pd.Period("2014", freq="Y-DEC") - p  # pd.Period - pd.Period  -> offset对象
#
# period_range函数可用于创建规则的周期范围:
periods = pd.period_range("2000-01-01", "2000-06-30", freq="M")
periods
# periods[0]  # Period('2000-01', 'M')
#
# PeriodIndex类保存了一组周期序列，它可以在任何pandas数据结构中用作轴索引：
pd.Series(np.random.standard_normal(6), index=periods)
# #
# # 如果你有一个字符串数组，也可以使用PeriodIndex类（所有值均为周期对象）：
values = ["2001Q3", "2002Q2", "2003Q1"]
index = pd.PeriodIndex(values, freq="Q-DEC")
index
```

#### 周期的频率转换
周期和PeriodIndex对象都可以通过其asfreq方法转换为别的频率。举个例子，假设我们有一个年度周期，希望将其转换为当年年初或年末的一个月度周期，可以如下实现：

```python
p = pd.Period("2011", freq="Y-DEC")
p
p.asfreq("M", how="start")  #  另一个频率的周期
p.asfreq("M", how="end")
p.asfreq("M")  # 默认按how="end"处理
```



可以将Period('2011'，'Y-DEC')看作一个时间段中的游标，该时间段被划分为多个月度周期。图11-1对此进行了说明。对于不是以十二月作为结束月的财政年度，相应的月度的子周期是不同的：

![年周期图片](./images/pandas_period_year.png)

```python
p = pd.Period("2011", freq="Y-JUN")  # 以6月份结尾的 2011年 这个周期    2010.7 -- 2011.6
p
p.asfreq("M", how="start")
p.asfreq("M", how="end")
#
# 在将高频率转换为低频率时，根据父周期的归属情况，pandas确定了周期。例如，在Y-JUN频率中，月份Aug-2011实际上是属于周期2012的：
p = pd.Period("Aug-2011", "M")
p
p.asfreq("Y-JUN")  # 2010.7-2011.6 （2011年）； 2011.7 -- 2012.6（2012年）
```

```python
# 完整的PeriodIndex或时间序列也可以用相同的语法进行转换：
periods = pd.period_range("2006", "2009", freq="Y-DEC")
ts = pd.Series(np.random.standard_normal(len(periods)), index=periods)
ts

# series.asfreq(频率字符串, how="start"/"end") -> 新的series，series的索引基于原来的周期索引 去做了 频率变化
ts.asfreq("M", how="start")
```

#### 季度周期频率
季度数据在会计、金融等领域很常见。许多季度数据都会涉及“财年末”的概念，通常是一年12个月中某月的最后一个日历日或工作日。就这一点来说，周期2012Q4根据财年末的不同会有不同的含义。pandas支持12种可能的季度频率，即Q-JAN到Q-DEC：

```python
p = pd.Period("2012Q4", freq="Q-JAN")
p

#  2012以1月结尾的话： 2011.2 -- 2012.1

# # 在以1月结束的财年中，2012Q4   是从2011年11月到2012年1月。为了验证，可以将其转换为  日型频率：
p.asfreq("D", how="start")  # 2012Q4的 第一天 （按天的周期）
p.asfreq("D", how="end")
```

![不同的季度频率](./images/pandas_quarter_frequency.png)

```python
# pandas.period_range可用于生成季度 的周期
periods = pd.period_range("2011Q3", "2012Q4", freq="Q-JAN")
ts = pd.Series(np.arange(len(periods)), index=periods)
ts

ts.asfreq("M", how="start")
```

#### 时间戳和周期的相互转换
通过使用to_period方法，可以将由时间戳索引的Series和DataFrame对象转换为以周期作为索引：

```python
dates = pd.date_range("2000-01-01", periods=3, freq="MS")
ts = pd.Series(np.random.standard_normal(3), index=dates)
ts


# series变量.to_period()
pts = ts.to_period()
pts
pts.index  # PeriodIndex
ts.index # DatetimeIndex
```

由于周期指的是非重叠时间段，因此对于给定的频率，一个时间戳只能属于一个周期。新PeriodIndex的频率默认是从时间戳推断而来的，你也可以指定任何支持的频率 。结果中允许存在重复周期：

```python
dates = pd.date_range("2000-01-29", periods=6)
ts2 = pd.Series(np.random.standard_normal(6), index=dates)
ts2

ts2.to_period("M")
```

```python
# 要转换回时间戳，使用to_timestamp即可，返回的是DatetimeIndex：
pts = ts2.to_period("M")
ts2
pts
pts.to_timestamp(how="end")  # series.to_timestamp(how="start"/"end")
```



#### 通过数组创建PeriodIndex

固定频率的数据集通常会将时间信息分开存放在多个列中。例如，在下面这个宏观经济数据集中，年度数据集和季度数据集就分别存放在不同的列中：

```python
data = pd.read_csv("examples/macrodata.csv")
data.head(5)
data["year"]
data["quarter"]
```

```python
# 通过将这些数组以及一个频率传入pd.PeriodIndex.from_fields，就可以将它们合并成DataFrame的索引：
index = pd.PeriodIndex.from_fields(year=data["year"], quarter=data["quarter"],
                       freq="Q-DEC")  # 基于两列 ->  周期索引
index
data.index = index
data
data["infl"]
```

### 重采样及频率转换
重采样指的是将时间序列从一个频率转换到另一个频率的处理过程。将高频率数据连接到低频率称为降采样，而将低频率数据转换到高频率则称为升采样。并不是所有的重采样都能被划分到这两大类中。例如，将W-WED（每周三）转换为W-FRI既不是降采样也不是升采样。

pandas对象都带有一个resample方法，它是各种频率转换工作的主力函数。resample有一个类似于groupby的API，调用resample可以对数据进行分组，然后调用聚合函数：

```python
dates = pd.date_range("2000-01-01", periods=100)
ts = pd.Series(np.random.standard_normal(len(dates)), index=dates)
ts
ts.resample("ME").mean()  # ME Month-End
ts.resample("ME").mean().to_period('M')  # 转成周期索引
```

#### 降采样
降采样是将数据聚合到规则的低频率。待聚合的数据不必拥有固定的频率，期望的频率会自动定义聚合的分箱边界，这些分箱用于将时间序列拆分为多个片段。例如，要转换到月度频率（"M"或"BM"），数据需要被划分到多个单月时间段中。

各时间段都是半开放的。一个数据点只能属于一个时间段，所有时间段的并集必须能组成整个时间范围。在用resample对数据进行降采样时，需要考虑两件事：各区间哪边是闭合的。如何对各个聚合分箱打标签，是用区间的开头还是末尾。为了说明，我们来看一些频率为一分钟的数据

```python
dates = pd.date_range("2000-01-01", periods=12, freq="min")
ts = pd.Series(np.arange(len(dates)), index=dates)
ts
```

```python
# 假设你想通过各组求和的方式将这些数据聚合到“五分钟”的数据块或柱状图的柱中：
ts.resample("5min").sum()

# 传入的频率将会以“五分钟”的增量定义分箱边界。
# 对于这个频率，分箱是默认包含左边界的，因此00:00到00:05的区间是包含00:00的
```

```python
# 最终的时间序列是以各分箱左边界的时间戳进行标记的
ts.resample("5min", closed="right").sum()

# 传入label="right"，即可用分箱的右边界对其进行标记：
ts.resample("5min", closed="right", label="right").sum()
```

![closed,label重采样示意图](./images/pandas_resample_closed_label.png)

```python
# 最后，你可能希望对结果索引做一些位移，比如从右边界减去一秒，使其更容易分清该时间戳到底表示的是哪个区间。只需对结果索引添加一个偏移量即可实现：

from pandas.tseries.frequencies import to_offset
result = ts.resample("5min", closed="right", label="right").sum()
result.i
```

```python
# 在金融领域，一种常用的时间序列聚合方式是计算各个桶的4个值，即第一个值（open，开盘）、最后一个值（close，收盘）、最大值（high，最高）以及最小值（low，最低）。通过ohlc聚合函数，即可得到一个含有这4种聚合值的DataFrame，整个过程很高效，只需经过一次函数调用：
ts = pd.Series(np.random.permutation(np.arange(len(dates))), index=dates)
ts.resample("5min").ohlc()
```

#### 升采样和插值
升采样是将数据从低频率转换为高频率，不需要做聚合。来看一个带有每周数据的DataFrame：

```python
frame = pd.DataFrame(np.random.standard_normal((2, 4)),
                     index=pd.date_range("2000-01-01", periods=2,
                                         freq="W-WED"),
                     columns=["Colorado", "Texas", "New York", "Ohio"])
frame

# 每组只有一个值，这样就会在间隙导入缺失值。我们使用asfreq方法将其转换为高频率数据，不需要做聚合操作：
df_daily = frame.resample("D").asfreq()
df_daily
```

```python
# 假设你想用前面的每周数值来填充非星期三的日期。ffill方法填充 重采样：
frame.resample("D").ffill()

# 同样，这里也可以只填充指定的周期数，以限制观测值的持续范围：
frame.resample("D").ffill(limit=2)

# 注意，新的日期索引不必与旧的索引重叠
frame.resample("W-THU").ffill()
```

### 移动窗口函数
在移动窗口进行统计，也是一类常见于时间序列的数组变换。这对于圆滑噪声数据或不连续数据很有帮助

```python
# 加载一些时间序列数据，并将其重采样为工作日频率
close_px_all = pd.read_csv("examples/stock_px.csv", parse_dates=True, index_col=0)
close_px_all
close_px_all.index
close_px = close_px_all[["AAPL", "MSFT", "XOM"]]
close_px = close_px.resample("B").ffill()
```

```python
import matplotlib.pyplot as plt
# rolling运算符，通过一个窗口大小，可以在Series或DataFrame上调用它：
# series.rolling(滚动窗口的大小)  # 滚动窗口
close_px["AAPL"].rolling(250)
close_px["AAPL"].rolling(250).mean()
# close_px["AAPL"].rolling(250).mean().loc[close_px.index[0] + pd.offsets.BusinessDay(249)]  # 窗口大小250，要有250个工作日，才有窗口的平均值

# 苹果公司股价的250日均线
close_px["AAPL"].plot()
close_px["AAPL"].rolling(250).mean().plot()
#
# # 表达式rolling(250)与groupby很像，但它不是进行分组，而是创建一个可以按照250日分组的移动窗口对象
#
plt.show()
```

默认情况下，滚动函数需要窗口中所有的值为非NA值，可以修改此行为以解决缺失数据的问题。特别是，实际上在时间序列开始处，数据量就低于window周期数：

```python
# 移动窗口标准差
#  series.rolling(窗口大小, min_periods=最小可以计算的窗口大小)
close_px["AAPL"].pct_change()  # 计算数据相对于上一个时间点的百分比变化
std250 = close_px["AAPL"].pct_change().rolling(250, min_periods=10).std()
std250.iloc[5:12]  # min_periods=10, 位置到10  直接开始计算标准差了，虽然窗口大小是250

#
# # 苹果公司250日每日收益标准差
std250.plot()
plt.show()
```

```python
# 对DataFrame调用移动窗口函数会将转换应用到所有列上
# close_px.rolling(60).mean().plot(logy=True)

# data_frame.rolling(窗口大小)
close_px.rolling(60).mean().plot(logy=True)
plt.show()
```

```python
# 有些统计运算（如相关系数和协方差）需要在两个时间序列上执行。例如，金融分析师通常对某只股票与某个基准指数（如标准普尔500指数）的相关系数感兴趣。要进行说明，先计算所有我们感兴趣的时间序列的百分比变化：
spx_px = close_px_all["SPX"]
spx_rets = spx_px.pct_change()
spx_rets
returns =close_px.pct_change()
returns

# 调用rolling之后，corr聚合函数开始计算spx_rets的滚动相关系数
#   窗口内125个数据，和标准普尔指数算相关性，逐窗滑动，做关联分析时会索引对齐
corr = returns["AAPL"].rolling(125, min_periods=100).corr(spx_rets)
corr
corr.plot()

plt.show()
```

```python
# 如果每只股票都是DataFrame中的一列，我们只需对DataFrame和传入的Series spx_rets调用rolling，就可以一次性计算出所有的滚动相关系数
corr = returns.rolling(125, min_periods=100).corr(spx_rets)
corr
corr.plot()

plt.show()
```

