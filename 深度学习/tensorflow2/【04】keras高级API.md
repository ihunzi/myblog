# keras高级API

[TOC]

## 简介

在TensorFlow 中，也实现了一套Keras 协议，即`tf.keras`，它与TensorFlow 深度融合，且只能基于TensorFlow 后端运算，并对TensorFlow 的支持更完美。对于使用TensorFlow 的开发者来说，**tf.keras 可以理解为一个普通的子模块**，与其他子模块，如tf.math，tf.data 等并没有什么差别

导入keras 模型，（不能使用import keras，它导入的是标准的Keras 库）

```python
from tensorflow import keras
from tensorflow.keras import layers # 导入常见网络层类
```

## 网络层

在`tf.keras.layers` 命名空间(下文使用layers 指代tf.keras.layers)中提供了大量常见网络层的类，如全连接层、激活函数层、池化层、卷积层、循环神经网络层等。对于这些网络层类，只需要在创建时指定网络层的相关参数。

## 网络容器

`Sequential`

Keras 提供的网络容器`Sequential` 将多个网络层封装成一个大网络模型，只需要调用网络模型的实例一次即可完成数据从第一层到最末层的顺序传播运算。

例如：

```python
# 导入Sequential 容器
from tensorflow.keras import layers, Sequential

network = Sequential([ # 封装为一个网络
    layers.Dense(3, activation=None), # 全连接层，此处不使用激活函数
    layers.ReLU(),#激活函数层
    layers.Dense(2, activation=None), # 全连接层，此处不使用激活函数
    layers.ReLU() #激活函数层
])

x = tf.random.normal([4,3])
out = network(x) # 输入从第一层开始，逐层传播至输出层，并返回输出层的输出
```

Sequential 容器也可以通过add()方法继续追加新的网络层，实现动态创建网络的功能：

```python
layers_num = 2 # 堆叠2 次
network = Sequential([]) # 先创建空的网络容器

for _ in range(layers_num):
    network.add(layers.Dense(3)) # 添加全连接层
    network.add(layers.ReLU())# 添加激活函数层
    
network.build(input_shape=(4, 4)) # 创建网络参数
network.summary()

# 通过summary()函数可以方便打印出网络结构和参数量，打印结果如下：
Model: "sequential_2"
_________________________________________________________________
Layer (type) Output Shape Param #
=================================================================
dense_2 (Dense) multiple 15
_________________________________________________________________
re_lu_2 (ReLU) multiple 0
_________________________________________________________________
dense_3 (Dense) multiple 12
_________________________________________________________________
re_lu_3 (ReLU) multiple 0
=================================================================
Total params: 27
Trainable params: 27
Non-trainable params: 0
_________________________________________________________________

# Param#列为层的参数个数，
# Total params 项统计出了总的参数量
# Trainable params 为总的待优化参数量
# Non-trainable params 为总的不需要优化的参数量
```

Sequential 对象的trainable_variables 和variables 包含了所有层的待优化张量列表和全部张量列表，

```python
In [3]: # 打印网络的待优化参数名与shape
    for p in network.trainable_variables:
		print(p.name, p.shape) # 参数名和形状
        
Out[3]:
    dense_2/kernel:0 (4, 3)
    dense_2/bias:0 (3,)
    dense_3/kernel:0 (3, 3)
    dense_3/bias:0 (3,)
```

创建网络后，正常的流程是循环迭代数据集多个Epoch，每次按批产生训练数据、前向计算，然后通过损失函数计算误差值，并反向传播自动计算梯度、更新网络参数。

这一部分逻辑由于非常通用，在Keras 中提供了`compile()`和`fit()`函数方便实现上述逻辑

## 模型装配

### compile

首先通过compile 函数指定网络使用的

- 优化器对象
- 损失函数类型
- 评价指标

等设定，这一步称为装配。

例如

```python
# 导入优化器，损失函数模块
from tensorflow.keras import optimizers,losses

# 模型装配
# 采用Adam 优化器，学习率为0.01;采用交叉熵损失函数，包含Softmax
network.compile(optimizer=optimizers.Adam(lr=0.01),
                     loss=losses.CategoricalCrossentropy(from_logits=True),
                  metrics=['accuracy'] # 设置测试测量指标为准确率
)
```

## 模型训练

### fit

模型装配完成后，即可通过fit()函数送入待训练的数据集和验证用的数据集，这一步称为模型训练。

```python
# 指定训练集为train_db，验证集为val_db,训练5 个epochs，每2 个epoch 验证一次
# 返回训练轨迹信息保存在history 对象中
history = network.fit(train_db, epochs=5, validation_data=val_db,validation_freq=2)

# train_db 为tf.data.Dataset 对象，也可以传入Numpy Array 类型的数据；
# epochs 参数指定训练迭代的Epoch 数量；
# validation_data 参数指定用于验证(测试)的数据集
# validation_freq 验证的频率
```

fit 函数会返回训练过程的数据记录history，其中history.history 为字典对象，包含了训练过程中的loss、测量指标等记录项，我们可以直接查看这些训练数据。

```python
In [4]:
    history.history # 打印训练记录
Out[4]: # 历史训练准确率
    {'accuracy': [0.00011666667, 0.0, 0.0, 0.010666667, 0.02495],
     'loss': [2465719710540.5845, # 历史训练误差
              78167808898516.03,
              404488834518159.6,
              1049151145155144.4,
              1969370184858451.0],
     'val_accuracy': [0.0, 0.0], # 历史验证准确率
    # 历史验证误差
     'val_loss': [197178788071657.3, 1506234836955706.2]}
```

## 模型测试

### predict

通过 Model.predict(x)方法即可完成模型的预测

```python
# 加载一个batch 的测试数据
x,y = next(iter(db_test))
print('predict x:', x.shape) # 打印当前batch 的形状
pred = network.predict(x) # 模型预测，预测结果保存在pred 中
# 取出y概率最大的
y = tf.argmax(y, axis=1)
# 取出预测值最大的
pred = tf.argmax(pred, axis=1)
print(y, pred)
```

如果只是简单的测试模型的性能，可以通过Model.evaluate(db)循环测试完db 数据集上所有样本，并打印出性能指标。和fit中的validation_xxx效果类似。

```python
network.evaluate(db_test) # 模型测试，测试在db_test 上的性能表现
```

## 模型的保存与加载

### save/load weight

```python
# 保存模型
model.save_weights('./checkpoints/my_checkpoint')

# 加载模型
model = create_model()	# 按照原来的代码的网格结构创建好一模一样的网络模型
model.load_weights('./checkpoints/my_checkpoint')
```

例如

```python
# 保存模型
model.save_weights('./checkpoints/my_checkpoint')
del network # 删除网络对象

# 加载模型
# 重新创建相同的网络结构
model = Sequential([layers.Dense(256, activation='relu'),
                      layers.Dense(128, activation='relu'),
                      layers.Dense(64, activation='relu'),
                      layers.Dense(32, activation='relu'),
                      layers.Dense(10)])
model.compile(optimizer=optimizers.Adam(lr=0.01),
                loss=tf.losses.CategoricalCrossentropy(from_logits=True),
                metrics=['accuracy']
)
# 从参数文件中读取数据并写入当前网络
model.load_weights('./checkpoints/my_checkpoint')
```

这种保存与加载网络的方式最为轻量级，文件中保存的仅仅是张量参数的数值，并没有其它额外的结构参数。但是它需要使用相同的网络结构才能够正确恢复网络状态，因此一般在拥有网络源文件的情况下使用。

### save/load entire weight

通过Model.save(path)函数可以将模型的结构以及模型的参数保存到path 文件上，在不需要网络源文件的条件下，通过keras.models.load_model(path)即可恢复网络结构和网络参数。

```python
model.save('path')
model = keras.models.load_model('path')
```

例如

```python
model.save('model.h5')
del network # 删除网络对象

# 从文件恢复网络结构与网络参数
model = keras.models.load_model('model.h5')
```

### saved_model

通过 tf.saved_model.save (network, path)即可将模型以SavedModel 方式保存到path 目录中

用户无需关心文件的保存格式，只需要通过tf.saved_model.load 函数即可恢复出模型对象，我们在恢复出模型实例后，完成测试准确率的计算

```python
tf.saved_model.save(model, path)
model = tf.saved_model.load(path)
```

这种模型用于工业部署



## ★自定义网络

 ```python
 # 自定义网络层，代替标准的Dense
 class MyDense(layers.Layer):
     def __init__(self, input_dim, output_dim):
         super(MyDense, self).__init__()
 
         self.kernel = self.add_weight('w', [input_dim, output_dim])
         # self.bias = self.add_weight('b', [output_dim])
 
     def call(self, inputs, training=None):
         x = inputs @ self.kernel
         return x
 
 # 自定义网络
 class MyNetwork(keras.Model):
     def __init__(self):
         super(MyNetwork, self).__init__()
         self.fc1 = MyDense(32 * 32 * 3, 256)
         self.fc2 = MyDense(256, 128)
         self.fc3 = MyDense(128, 64)
         self.fc4 = MyDense(64, 32)
         self.fc5 = MyDense(32, 10)
 
 
     def call(self, inputs, training=None):
         """
 
         :param inputs:[b, 32, 32, 3]
         :param training:
         :return:
         """
         x = tf.reshape(inputs, [-1, 32*32*3])
         x = self.fc1(x)
         x = tf.nn.relu(x)
 
         x = self.fc2(x)
         x = tf.nn.relu(x)
 
         x = self.fc3(x)
         x = tf.nn.relu(x)
 
         x = self.fc4(x)
         x = tf.nn.relu(x)
 
         x = self.fc5(x)
 
         return x
 ```



