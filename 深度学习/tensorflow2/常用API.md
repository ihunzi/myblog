# tensorflow常用API
## 一、基本用法

### 1、导入

```python
import tensorflow as tf
```

### 2、查看版本

```python
print(tf.__version__)
```

### n、构建模型

- 构建模型

  **Flatten**

  - 该网络的第一层 tf.keras.layers.Flatten 将图像格式从二维数组（28 x 28 像素）转换成一维数组（28 x 28 = 784 像素）。将该层视为图像中未堆叠的像素行并将其排列起来。该层没有要学习的参数，它只会重新格式化数据。将数据展平

  **Dense**

  - 展平像素后，网络会包括两个 tf.keras.layers.Dense 层的序列。它们是密集连接或全连接神经层。第一个 Dense 层有 128 个节点（或神经元）。第二个（也是最后一个）层会返回一个长度为 10 的 logits 数组。每个节点都包含一个得分，用来表示当前图像属于 10 个类中的哪一类。

```python
model = keras.Sequential([
    keras.layers.Flatten(input_shape=(28, 28)),
    keras.layers.Dense(128, activation='relu'),
    keras.layers.Dense(10, activation="softmax"),
])
```

- 查看模型

```python
model.summary()
```

- 模型配置

  - 优化器(optimizer) - 决定模型如何根据其看到的数据和自身的损失函数进行更新。

  - 损失函数(loss) - 用于测量模型在训练期间的准确率。您会希望最小化此函数，以便将模型“引导”到正确的方向上。
  - 指标(metrics) - 用于监控训练和测试步骤。以下示例使用了准确率，即被正确分类的图像的比率。

```python
model.compile(
    optimizer='rmsprop', loss=None, metrics=None, loss_weights=None,
    weighted_metrics=None, run_eagerly=None, steps_per_execution=None, **kwargs
)
# e.g.
model.compile(optimizer='adam',
             loss='sparse_categorical_crossentropy',
             metrics=['accuracy'])
```

- 训练模型

```python
model.fit(
    x=None, y=None, batch_size=None, epochs=1, verbose='auto',
    callbacks=None, validation_split=0.0, validation_data=None, shuffle=True,
    class_weight=None, sample_weight=None, initial_epoch=0, steps_per_epoch=None,
    validation_steps=None, validation_batch_size=None, validation_freq=1,
    max_queue_size=10, workers=1, use_multiprocessing=False
)

# e.g.
model.fit(x_train, y_train, epochs=10)
```

- 模型评估

```python
model.evaluate(
    x=None, y=None, batch_size=None, verbose=1, sample_weight=None, steps=None,
    callbacks=None, max_queue_size=10, workers=1, use_multiprocessing=False,
    return_dict=False, **kwargs
)
# e.g.
model.evaluate(x_test,  y_test)
```

**会输出当前测试样本的loss和精度**

- 预测

```python
model.predict(
    x, batch_size=None, verbose=0, steps=None, callbacks=None, max_queue_size=10,
    workers=1, use_multiprocessing=False
)
# e.g.
model.predict([x])
# 如果是分类问题，输出是所有分类的概率值，只需找到最大的概率即可
```



### n、全连接层

最简单的只需要给一个参数units，即该层的输出维度（输入维度Dense会自动计算，所以不用给定）。

一般会给定两个参数，units（输出维度）和activation（激活函数）

隐藏层的units是人为设定的，不同的units都是可行的，至于哪一个是最优的，要根据经验和实验得到，而激活函数一般使用ReLu

输出层的units是实际要输出的维度，如果是一个分类任务，有10个标签，则units为10，激活函数使用softmax，表示0-1之间的概率

```python
tf.keras.layers.Dense(
    units, activation=None, use_bias=True,
    kernel_initializer='glorot_uniform',
    bias_initializer='zeros', kernel_regularizer=None,
    bias_regularizer=None, activity_regularizer=None, kernel_constraint=None,
    bias_constraint=None, **kwargs
)
```

### n、模型的保存与加载

#### ①save/load weight

```python
model.save_weights('weights.ckpt')
model.load_weights('weights.ckpt')
```

这种保存与加载网络的方式最为轻量级，文件中保存的仅仅是张量参数的数值，并没有其它额外的结构参数。但是它需要使用相同的网络结构才能够正确恢复网络状态，因此一般在拥有网络源文件的情况下使用。

#### ②save/load entire weight

通过Model.save(path)函数可以将模型的结构以及模型的参数保存到path 文件上，在不需要网络源文件的条件下，通过keras.models.load_model(path)即可恢复网络结构和网络参数。

```python
model.save('path')
model = keras.models.load_model('path')
```

#### ③saved_model

通过 tf.saved_model.save (network, path)即可将模型以SavedModel 方式保存到path 目录中

用户无需关心文件的保存格式，只需要通过tf.saved_model.load 函数即可恢复出模型对象，我们在恢复出模型实例后，完成测试准确率的计算

```python
tf.saved_model.save(model, path)
model = tf.saved_model.load(path)
```

这种模型用于工业部署





## 二、卷积神经网络



## 三、自然语言处理

### 1、分词器

- 导入

```python
from tensorflow.keras.preprocessing.text import Tokenizer
```

- 创建分词器

```python
tokenizer = Tokenizer(num_words=100)	# 创建100大小的词典
```

- 根据数据集分词
  - fit_on_texts将遍历整个文本并创建词典key:value，key为单词，value为单词的编码
  - 这个操作会去除标点符号，和对大小写不敏感

```python
sentences = [
    'I love my dog',
    'I love my cat',
    'You love my dog!',
    'Do you think my dog is amazing?'
]
tokenizer.fit_on_texts(sentences)
```

- 查看词典

```python
word_index = tokenizer.word_index
pring(word_index)
```

- 输出结果

```python
{'my': 1, 'love': 2, 'i': 3, 'dog': 4, 'cat': 5, 'you': 6, 'do': 7, 'think': 8, 'is': 9, 'amazing': 10}
```

#### 分词器拓展

- 查看编码结果

```python
tokenizer_string = tokenizer.encode(sentences[0])
```

- 查看解码结果

```python
tokenizer.decode(tokenizer_string)
```



### 2、序列化

#### 将上面的sentences序列化

```python
sequences = tokenizer.texts_to_sequences(sentences)
print(sequences)
```

- 输出

```python
[[3, 2, 1, 4], [3, 2, 1, 5], [6, 2, 1, 4], [7, 6, 8, 1, 4, 9, 10]]
```

#### 用新的句子看看序列化效果

```python
test_data = [
    'I really love my dog',
    'my dog loves my manatee'
]
test_seq = tokenizer.texts_to_sequences(test_data)
print(test_data)
```

- 输出

```python
[[3, 2, 1, 4], [1, 4, 1]]
```

<u>*如果词典里面没有句子中的词，则句子编码后会丢失部分词*</u>

#### 解决句子成分丢失

- 在创建分词器时，设置一个参数oov_token，后面的符号可以是任何不与单词冲突的符号

```python
tokenizer = Tokenizer(num_words=100, oov_token='<BLK>')	# 创建100大小的词典
```

- 输出结果

```python
{'<BLK>': 1, 'my': 2, 'love': 3, 'dog': 4, 'i': 5, 'you': 6, 'cat': 7, 'do': 8, 'think': 9, 'is': 10, 'amazing': 11}
[[5, 1, 3, 2, 4], [2, 4, 1, 2, 1]]
```

#### 句子补齐——pad_sequences

- 导入

```python
from tensorflow.keras.preprocessing.sequence import pad_sequences
```

- 句子补齐

```python
:sequences 要补齐的句子
:padding 在开头（pre）/末尾（post）补齐，默认为开头
:truncating 当句子超出长度时，从开头（pre）/末尾（post）去掉超出部分，默认为开头
:maxlen 句子长度
padded = pad_sequences(sequences, padding='post', truncating='post', maxlen=5)
print(padded)
```

- 输出

```python
原来
[[5, 3, 2, 4], [5, 3, 2, 7], [6, 3, 2, 4], [8, 6, 9, 2, 4, 10, 11]]
句子补齐后
[[5 3 2 4 0]
 [5 3 2 7 0]
 [6 3 2 4 0]
 [8 6 9 2 4]]
```

### 3、嵌入

- Embedding层

```python
这个函数的作用就是，把单词转换为词向量，用于神经网络计算
第一个参数是：单词总数量（词汇表大小）
第二个参数是：单词的词向量维度，比如上面的6表示‘you’，这里第二个参数设置为4的话，那么‘you’:6的词向量就是[x x x x],1*4的向量来表示
tf.keras.layers.Embedding(
    input_dim, output_dim, embeddings_initializer='uniform',
    embeddings_regularizer=None, activity_regularizer=None,
    embeddings_constraint=None, mask_zero=False, input_length=None, **kwargs
)
```

- 输出
  - 嵌入的输出是一个二维数组，行和列分别是句子的长度和向量的维度，所以Embedding之后要有Flatten层（或者GlobalAvgPool1D()）将结果展平

### 4、LSTM层

```python
tf.keras.layers.LSTM(
    units, activation='tanh', recurrent_activation='sigmoid',
    use_bias=True, kernel_initializer='glorot_uniform',
    recurrent_initializer='orthogonal',
    bias_initializer='zeros', unit_forget_bias=True,
    kernel_regularizer=None, recurrent_regularizer=None, bias_regularizer=None,
    activity_regularizer=None, kernel_constraint=None, recurrent_constraint=None,
    bias_constraint=None, dropout=0.0, recurrent_dropout=0.0,
    return_sequences=False, return_state=False, go_backwards=False, stateful=False,
    time_major=False, unroll=False, **kwargs
)
```

一般用到两个参数，units（输出维度）和return_sequences=True（当前一个LSTM层和后一个LSTM层衔接时，前一个LSTM层需设置return_sequences=True，这样可以确保上一个LSTM层的输出可与下一个LSTM层的输入相匹配），dropout（正则化——随机失活）

