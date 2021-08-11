# tensorflow2进阶

[TOC]

## 分割与合并

### 合并

`concat`

通过`tf.concat(tensors, axis)`函数拼接张量，其中参数tensors 保存了所有需要合并的张量List，axis 参数指定需要合并的维度索引。

设一个tensor为：a = [6, 35, 8]，具体含义是：6个班级，每个班级35个学生， 8门学科

* **此时需要A收集4个班的数据，B收集2个班的数据，并将A和B的数据合并起来交给C。**

```python
a = tf.ones([4, 35, 8])
b = tf.ones([2, 35, 8])
c = tf.concat([a, b], axis=0)

c.shape
TensorShape([6, 35, 8])
```

* **此时需要A收集4个班每个班32个同学的数据，B收集4个班每个班3个同学的数据，并将A和B的数据合并起来交给C。**

```python
a = tf.ones([4, 32, 8])
b = tf.ones([4, 3, 8])
c = tf.concat([a, b], axis=1)

c.shape
TensorShape([4, 35, 8])
```

*要合并的维度可以不相等，其他维度都要相等*

### 堆叠

`stack`

假设学校A[4, 35, 8]，学校B[4, 35, 8]；把两个学校的数据合并到一起变成[2, 4, 35, 8]

```python
a = tf.ones([4, 35, 8])
b = tf.ones([4, 35, 8])

c = tf.stack([a, b], axis=0)
d = tf.stack([a, b], axis=3)

c.shape
TensorShape([2, 4, 35, 8])
d.shape
TensorShape([4, 35, 8, 2])
```

*所有的维度都要完全相等*

### 切割

* `unstack`

以上面c = [2, 4, 35, 8]为例

```python
aa, bb = tf.unstack(c, axis=0)
aa.shape, bb.shape
TensorShape([4, 35, 8]), TensorShape([4, 35, 8])

res = tf.unstack(c, axis=3)
res[0].shape, res[1].shape...res[7].shape
TensorShape([2, 4, 35])...
```

要分割的轴上是多少，就分割成多少个tensor。比如上面的axis=3为8，就分割成一个长度为8的List，每个list的shape如上

* `split`

`tf.split(x, num_or_size_splits, axis)`参数意义如下

![image-20210806153325555](https://gitee.com/ihunzi/images/raw/master/blog/image-20210806153325555.png)

------

## 数据统计

### 范数

`tf.norm(x, ord, axis)`

其中参数ord 指定为1、2 时计算L1、L2 范数，指定为np.inf 时计算∞ −范数。

### 最值

- `tf.reduce_max(x, axis)`

- `tf.reduce_min(x, axis)`

### 均值

`tf.reduce_mean(x, axis)`

### 和

`tf.reduce_sum(x, axis)`	求解张量在axis 轴上所有特征的和



**当不指定axis 参数时，tf.reduce_*函数会求解出全局元素的最大、最小、均值、和等数据**

### 最值索引

通过 `tf.argmax(x, axis)`和`tf.argmin(x, axis)`可以求解在axis 轴上，x 的最大值、最小值所在的**索引号**

```python
a.shape = [4, 10]

tf.argmax(a).shape = [10]
tf.argmax(a) = array([3, 3, 3, 3, 3, 3, 3, 2, 1, 0])

tf.argmin(a, axis=1).shape = [4]
tf.argmin(a) = array([9, 8, 7, 6])

# 结合下面的矩阵好理解
```

$$
\begin{bmatrix}
0&1&2&3&4&5&6&7&8&9\\
1&2&3&4&5&6&7&8&9&0\\
2&3&4&5&6&7&8&9&0&1\\
3&4&5&6&7&8&9&0&1&2\\
\end{bmatrix}
$$

### 比较

`tf.equal(a, b)`

```python
# 假设
a = tf.constant([1, 2, 3, 3, 5])
b = tf.range(5)		# b = [1, 2, 3, 4, 5]

# 比较
tf.equal(a, b)
<tf.Tensor: shape=(5,), dtype=bool, numpy=array([False, False, False, True, False])>
# 返回
[False, False, False, True, False] 相当于 [0, 0, 0, 1, 0]
```

用来做精确度计算

假设样本预测结果a=$\begin{bmatrix}
0.1&0.2&0.7\\
0.9&0.05&0.05\\
\end{bmatrix}$，则`pred = argmax(a, axis=1)`的结果为[2， 0]，样本实际值y为[2, 1]

```python
# 比较预测值和实际值的误差
tf.equal(pred, y)
# 得到 [True, False]即[1, 0]
# 将比较结果的数据相加
correct = tf.reduce_sum(tf.cast(tf.equal(pred, y), dtype=tf.int32))
# 得到 correct = 1
# 然后除以样本数量2
correct / 2 = 0.5
# 所以精确度为0.5
```

### 去除重复

`tf.unique`



------

## 张量排序

### 随机打散

`tf.random.shuffle`

### Sort/argsort

`a = tf.random.shuffle(tf.range(5))`	=>  tf.Tensor([4 2 1 3 0], shape=(5,), dtype=int32)

- `tf.sort`	返回排序后的结果

```python
tf.sort(a, direction='DESCENDING')
<tf.Tensor: shape=(5,), dtype=int32, numpy=array([4, 3, 2, 1, 0])>
```

- `tf.argsort`	返回排序的索引，比如这个索引0对应a中的值4；索引3对应a中的值3；索引1对应a中的值2...

```python
tf.argsort(a, direction='DESCENDING')
<tf.Tensor: shape=(5,), dtype=int32, numpy=array([0, 3, 1, 2, 4])>
```

*这里都是默认对左后一个轴进行排序*

### Top_k

返回前**k**个排序结果

`res = tf.matg.top_k(a, k)`，a是待排序的张量，k是前k个结果

`res.indices`返回前k个的索引

`res.values`返回前k个的值

## 填充与复制

### 填充

`tf.pad(a, padding)` a为待填充的tensor，参数paddings 是包含了多个**[** *Left Padding*, *Right Padding* **]**的嵌套方案List。

如[[0,0], [2,1], [1,2]]表示第一个维度不填充，第二个维度左边(起始处)填充两个单元，右边(结束处)填充一个单元，第三个维度左边填充一个单元，右边填充两个单元。

![image-20210806171616867](https://gitee.com/ihunzi/images/raw/master/blog/image-20210806171616867.png)

### 复制

`tf.tile(a, copy_list)`	a为待操作张量，copy_list为每个维度需要复制的次数的List

比如

`tf.tile(a, [1, 2])`

把a在第一个维度保持不变，第二个维度变成2倍

![image-20210806173003189](https://gitee.com/ihunzi/images/raw/master/blog/image-20210806173003189.png)

------

## 张量限幅

### $[a, +\infty)$​​​

`tf.maximum(x, a)`

### $(-\infty, a]$​​​

`tf.minimum(x, a)`

### $[a, b]$​​

`tf.clip_by_value(x, a, b)`



------

## 高级操作

- `tf.where(tensor)`

where是一个查询元素为true的方法

![image-20210807101417030](https://gitee.com/ihunzi/images/raw/master/blog/image-20210807101417030.png)

![image-20210807101635394](https://gitee.com/ihunzi/images/raw/master/blog/image-20210807101635394.png)

- `scatter_nd(indices, updates, shape)`

![image-20210807101932728](https://gitee.com/ihunzi/images/raw/master/blog/image-20210807101932728.png)

![image-20210807102145578](https://gitee.com/ihunzi/images/raw/master/blog/image-20210807102145578.png)

- `meshgrid`

通过 tf.meshgrid 函数可以方便地生成二维网格的采样点坐标，方便可视化等应用场合



------

## 经典数据集加载

`keras.datasets`

通过 `datasets.xxx.load_data()`函数即可实现经典数据集的自动加载，其中xxx 代表具体的数据集名称，如“CIFAR10”、“MNIST”。TensorFlow 会默认将数据缓存在用户目录下的.keras/datasets 文件夹

```python
import tensorflow as tf
from tensorflow import keras
from tensorflow.keras import datasets # 导入经典数据集加载模块

# 加载MNIST 数据集
(x, y), (x_test, y_test) = datasets.mnist.load_data()
print('x:', x.shape, 'y:', y.shape, 'x test:', x_test.shape, 'y test:',y_test)

# 返回数组的形状
Out [66]: x: (60000, 28, 28) y: (60000,) x test: (10000, 28, 28) y test: [7 2 1 ... 4 5 6]
```

`Dataset.from_tensor_slices`

数据加载进入内存后，需要转换成Dataset 对象，才能利用TensorFlow 提供的各种便捷功能，比如：随机打散（`Dataset.shuffle`）、批训练（`Dataset.batch`）等。通过`Dataset.from_tensor_slices` 可以将训练部分的数据图片x 和标签y 都转换成Dataset 对象

```python
train_db = tf.data.Dataset.from_tensor_slices((x, y)) # 构建Dataset 对象
```

将数据转换成Dataset 对象后，一般需要再添加一系列的数据集标准处理步骤，如随机打散、预处理、按批装载等。

### 随机打散

通过 `Dataset.shuffle(buffer_size)`工具可以设置Dataset 对象随机打散数据之间的顺序，防止每次训练时数据按固定顺序产生，从而使得模型尝试“记忆”住标签信息，代码实现如下：

```py
train_db = train_db.shuffle(10000) # 随机打散样本，不会打乱样本与标签映射关系
```

其中，buffer_size 参数指定缓冲池的大小，一般设置为一个较大的常数即可

### 批训练

为了利用显卡的并行计算能力，一般在网络的计算过程中会同时计算多个样本，我们把这种训练方式叫做批训练，其中一个批中样本的数量叫做`Batch Size`。为了一次能够从Dataset 中产生Batch Size 数量的样本，需要设置Dataset 为批训练方式，实现如下：

```python
train_db = train_db.batch(128) # 设置批训练，batch size 为128
```

其中128 为Batch Size 参数，即一次并行计算128 个样本的数据。Batch Size 一般根据用户的GPU 显存资源来设置，当显存不足时，可以适量减少Batch Size 来减少算法的显存使用量。

### 预处理

从 keras.datasets 中加载的数据集的格式大部分情况都不能直接满足模型的输入要求，因此需要根据**用户的逻辑自行实现**预处理步骤。Dataset 对象通过提供`map(func)`工具函数，可以非常方便地调用用户自定义的预处理逻辑，它实现在func 函数里。例如，下方代码调用名为preprocess 的函数完成每个样本的预处理：

```python
# 预处理函数实现在preprocess 函数中，传入函数名即可
train_db = train_db.map(preprocess)
```

考虑 MNIST 手写数字图片，从keras.datasets 中经.batch()后加载的图片x shape 为[𝑏, 28,28]，像素使用0~255 的整型表示；标签shape 为[𝑏]，即采样数字编码方式。实际的神经网络输入，一般需要将图片数据标准化到[0,1]或[−1,1]等0 附近区间，同时根据网络的设置，需要将shape 为[28,28]的输入视图调整为合法的格式；对于标签信息，可以选择在预处理时进行One-hot 编码，也可以在计算误差时进行One-hot 编码。

我们将MNIST 图片数据映射到𝑥 ∈ [0,1]区间，视图调整为[𝑏, 28 ∗ 28]；对于标签数据，我们选择在预处理函数里面进行One-hot 编码。preprocess 函数实现如下：

```python
def preprocess(x, y): # 自定义的预处理函数
    # 调用此函数时会自动传入x,y 对象，shape 为[b, 28, 28], [b]
    # 标准化到0~1
    x = tf.cast(x, dtype=tf.float32) / 255.
    x = tf.reshape(x, [-1, 28*28]) # 打平
    y = tf.cast(y, dtype=tf.int32) # 转成整型张量
    y = tf.one_hot(y, depth=10) # one-hot 编码
    # 返回的x,y 将替换传入的x,y 参数，从而实现数据的预处理功能
    return x,y
```

### 循环训练

对于 Dataset 对象，在使用时可以通过

```python
for step, (x,y) in enumerate(train_db): # 迭代数据集对象，带step 参数
```

或者

```python
for x,y in train_db: # 迭代数据集对象
```

方式进行迭代，每次返回的x 和y 对象即为批量样本和标签。当对train_db 的所有样本完成一次迭代后，for 循环终止退出。这样**完成一个Batch 的数据训练，叫做一个Step**；通过**多个step 来完成整个训练集的一次迭代，叫做一个Epoch**。在实际训练时，通常需要对数据集**迭代多个Epoch 才能取得较好地训练效果**。例如，固定训练20 个Epoch，实现如下：

```python
for epoch in range(20): # 训练Epoch 数
    for step, (x,y) in enumerate(train_db): # 迭代Step 数
        # training...
```

此外，也可以通过设置Dataset 对象，使得数据集对象内部遍历多次才会退出，实现如下:

```python
train_db = train_db.repeat(20) # 数据集迭代20 遍才终止
```

上述代码使得`for x,y in train_db` 循环迭代20 个epoch 才会退出。不管使用上述哪种方式，都能取得一样的效果。

