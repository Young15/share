# 开发服务器启动方式

在1.0版本之后，Flask调整了开发服务器的启动方式，由代码编写`app.run()`语句调整为命令`flask run`启动。

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return 'Hello World'
  
# 程序中不用再写app.run()
```

## 1 终端启动

```shell
$ export FLASK_APP=helloworld
$ flask run
 * Running on http://127.0.0.1:5000/
```

### 说明

- 环境变量 FLASK_APP 指明flask的启动实例

- `flask run -h 0.0.0.0 -p 8000` 绑定地址 端口

- `flask run --help`获取帮助

- 生产模式与开发模式的控制

  通过`FLASK_ENV`环境变量指明

  * `export FLASK_ENV=production` 运行在生产模式，未指明则默认为此方式
  * `export FLASK_ENV=development`运行在开发模式

### 扩展

```shell
$ export FLASK_APP=helloworld
$ python -m flask run
 * Running on http://127.0.0.1:5000/
```

## 2 Pycharm启动

设置环境变量

![设置环境变量](/images/pycharm运行环境变量.png)

![](/images/pycharm启动flask1.png)

#### 旧版本Pycharm设置

![](/images/Pycharm启动flask2.png)