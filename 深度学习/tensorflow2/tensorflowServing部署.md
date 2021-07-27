# 模型部署

## 保存模型

### SavedModel 格式保存

- 使用 `SavedModel` 格式进行模型保存

```
model.save('path_to_saved_model')
```

在当前文件夹里面会多出一个文件夹path_to_saved_model

![1](https://gitee.com/ihunzi/images/raw/master/blog/20210714185013.png)

```
.
├── assets
├── saved_model.pb
└── variables
    ├── variables.data-00000-of-00001
    └── variables.index
```

其中 `assets` 是一个可选的目录，用于存放模型运行所需的辅助文件，比如字典文件等。 `variables` 目录下存放的是模型权重的检查点文件，模型的所有权重信息均保存在该目录下。 `saved_model.pb` 文件中包含了模型的结构以及训练的配置信息如优化器，损失以及指标等信息。

## 启动服务

### 环境

- centos7.6或者ubuntu18.4
- docker20.10.7

### 准备

- docker拉取tensorflow serving

```
docker pull tensorflow/serving
```

- 基础上面的模型跟改一下目录结构

```
MODEL_NAME/
└── 1
    ├── assets
    ├── saved_model.pb
    └── variables
        ├── variables.data-00000-of-00001
        └── variables.index
MODEL_NAM是自定义的模型的名字
1 表示该模型的可用版本，这里只有一个版本，也可以存在多个版本。
版本目录下的文件即为 SavedModel 格式的文件
下面用xieshi这个名字来代替MODEL_NAME
```

- 1、新建一个文件夹来统一管理模型，在/usr/local目录下新建一个aimodels目录
- 2、把xieshi模型放到aimodels目录里面

![2](https://gitee.com/ihunzi/images/raw/master/blog/20210714185019.png)

### 启动服务

```
docker run -d -p 8501:8501 -v /usr/local/aimodels/xieshi:/models/xieshi -e MODEL_BASE_PATH=/models -e MODEL_NAME=xieshi tensorflow/serving

我们可以在容器启动时通过设置容器内的环境变量 MODEL_BASE_PATH 来指定模型的存储路径，该环境变量的默认值为 /models ，表示从容器内的 /models 路径加载模型。同样地，可以通过 MODEL_NAME 环境变量来指定要加载的模型名称，该环境变量的默认值为 model 。基于环境变量的 Serving 服务启动命令如下所示：

8051是tfs（tensorflow serving）默认的端口
挂载
/usr/local/aimodels/xieshi 主机模型目录
/models/xieshi 容器里面存放模型的目录
```

![3](https://gitee.com/ihunzi/images/raw/master/blog/20210714185022.png)

### 测试模型是否加载成功

- url里面多了v1是怎么回事，目前还不清楚，反正就需要加这个v1。否则出会出问题
- 出问题后使用 `docker logs containerid` 查看容器的日志以定位问题

```
输入命令
curl http://localhost:8501/v1/models/xieshi
返回
{
    "model_version_status": [
        {
            "version": "0",
            "state": "AVAILABLE",
            "status": {
                "error_code": "OK",
                "error_message": ""
            }
        }
    ]
}
```

![4](https://gitee.com/ihunzi/images/raw/master/blog/20210714185026.png)

### 输入参数预测结果

- 我这里是一个生成诗句的模型

```
curl -d '{"instances": [[1.0, 2.0, 5.0]]}' -X POST http://localhost:8501/v1/models/xieshi:predict
```

![5](https://gitee.com/ihunzi/images/raw/master/blog/20210714185029.png)

**如果返回错误使用 `docker logs containerid` 查看容器的日志以定位问题**

### python调用测试

```
import json
import requests

# 构造输入参数
payload = {
	"instances": [[1.0, 2.0, 5.0], ]
}
# 请求
pred = requests.post('http://localhost:8501/v1/models/model:predict', json=payload)
# 返回
_probas = json.loads(pred.content.decode('utf-8'))
# 取值
_probas = _probas['predictions']
 
print(_probas)
```

tensorflow serving需要json格式，按照上面那样就行了



## 参考资料

[TensorFlow 篇 | TensorFlow 2.x 模型 Serving 服务 (juejin.cn)](https://juejin.cn/post/6887763163865939975)

[TensorFlow 篇 | TensorFlow 2.x 基于 Keras 的模型保存及重建 (juejin.cn)](https://juejin.cn/post/6886633039876227079)

[【记录】记录第一次使用tensorflow serving全过程_maqian5的博客-CSDN博客](https://blog.csdn.net/maqian5/article/details/108404175)

[TensorFlow 篇 | TensorFlow 2.x 基于 Keras 的模型构建 (juejin.cn)](https://juejin.cn/post/6883776276813840398)

