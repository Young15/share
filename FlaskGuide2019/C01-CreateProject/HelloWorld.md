### Flask的HelloWorld程序

### 1 目标

**掌握flask程序的编写方式**

### 2 思考

**回忆如何起步编写Django程序？**

![why](/images/what.jpeg)

### 3 Flask程序编写

创建helloworld.py文件

```python
# 导入Flask类
from flask import Flask

#Flask类接收一个参数__name__
app = Flask(__name__)

# 装饰器的作用是将路由映射到视图函数index
@app.route('/')
def index():
    return 'Hello World'

# Flask应用程序实例的run方法启动WEB服务器
if __name__ == '__main__':
    app.run()

```

#### 4 启动运行

* 手动运行 

  ```shell
  python helloworld.py
  ```

* pycharm 运行

  像正常运行普通python程序一样即可。

