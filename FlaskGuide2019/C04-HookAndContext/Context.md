# 上下文

上下文：即语境，语意，在程序中可以理解为在代码执行到某一时刻时，根据之前代码所做的操作以及下文即将要执行的逻辑，可以决定在当前时刻下可以使用到的变量，或者可以完成的事情。

Flask中有两种上下文，请求上下文和应用上下文

Flask中上下文对象：相当于一个容器，保存了 Flask 程序运行过程中的一些信息。

## 1 请求上下文(request context)

思考：在视图函数中，如何取到当前请求的相关数据？比如：请求地址，请求方式，cookie等等

在 flask 中，可以直接在视图函数中使用 **request** 这个对象进行获取相关数据，而 **request** 就是请求上下文的对象，保存了当前本次请求的相关数据，请求上下文对象有：request、session

- request 
  - 封装了HTTP请求的内容，针对的是http请求。举例：user = request.args.get('user')，获取的是get请求的参数。
- session
  - 用来记录请求会话中的信息，针对的是用户信息。举例：session['name'] = user.id，可以记录用户信息。还可以通过session.get('name')获取用户信息。



## 2 应用上下文(application context)

它的字面意思是 应用上下文，但它不是一直存在的，它只是request context 中的一个对 app 的代理(人)，所谓local proxy。它的作用主要是帮助 request 获取当前的应用，它是伴 request 而生，随 request 而灭的。

应用上下文对象有：current_app，g

### current_app

应用程序上下文,用于存储应用程序中的变量，可以通过current_app.name打印当前app的名称，也可以在current_app中存储一些变量，例如：

- 应用的启动脚本是哪个文件，启动时指定了哪些参数
- 加载了哪些配置文件，导入了哪些配置
- 连了哪个数据库
- 有哪些public的工具类、常量
- 应用跑再哪个机器上，IP多少，内存多大

#### 示例

创建`current_app_demo.py`

```python
from flask import Flask, current_app

app1 = Flask(__name__)
app2 = Flask(__name__)

# 以redis客户端对象为例
# 用字符串表示创建的redis客户端
# 为了方便在各个视图中使用，将创建的redis客户端对象保存到flask app中，
# 后续可以在视图中使用current_app.redis_cli获取
app1.redis_cli = 'app1 redis client'
app2.redis_cli = 'app2 redis client'

@app1.route('/route11')
def route11():
    return current_app.redis_cli

@app1.route('/route12')
def route12():
    return current_app.redis_cli
  
@app2.route('/route21')
def route21():
    return current_app.redis_cli
  
@app2.route('/route22')
def route22():
    return current_app.redis_cli
```

运行

```shell
export FLASK_APP=current_app_demo:app1
flask run
```

* 访问`/route11`  显示`app1 redis client`
* 访问`/route12`  显示`app1 redis client`

```shell
export FLASK_APP=current_app_demo:app2
flask run
```

* 访问`/route21`  显示`app2 redis client`
* 访问`/route22`  显示`app2 redis client`

#### 作用

**`current_app` 就是当前运行的flask app，在代码不方便直接操作flask的app对象时，可以操作`current_app`就等价于操作flask app对象**

### g对象

g 作为 flask 程序全局的一个临时变量，充当中间媒介的作用，我们可以通过它在一次请求调用的多个函数间传递一些数据。每次请求都会重设这个变量。

#### 示例

```python
from flask import Flask, g

app = Flask(__name__)

def db_query():
    user_id = g.user_id
    user_name = g.user_name
    print('user_id={} user_name={}'.format(user_id, user_name))

@app.route('/')
def get_user_profile():
    g.user_id = 123
    g.user_name = 'itcast'
    db_query()
    return 'hello world'
```

### g对象与请求钩子的综合案例

#### 需求

* 构建认证机制
* 对于特定视图可以提供强制要求用户登录的限制
* 对于所有视图，无论是否强制要求用户登录，都可以在视图中尝试获取用户认证后的身份信息

#### 实现

```python
from flask import Flask, abort, g

app = Flask(__name__)

@app.before_request
def authentication():
    """
    利用before_request请求钩子，在进入所有视图前先尝试判断用户身份
    :return:
    """
    # TODO 此处利用鉴权机制（如cookie、session、jwt等）鉴别用户身份信息
    # if 已登录用户，用户有身份信息
    g.user_id = 123
    # else 未登录用户，用户无身份信息
    # g.user_id = None

def login_required(func):
    def wrapper(*args, **kwargs):
        if g.user_id is not None:
            return func(*args, **kwargs)
        else:
            abort(401)

    return wrapper

@app.route('/')
def index():
    return 'home page user_id={}'.format(g.user_id)

@app.route('/profile')
@login_required
def get_user_profile():
    return 'user profile page user_id={}'.format(g.user_id)
```

## 3 app_context 与 request_context

#### 思考

在Flask程序未运行的情况下，调试代码时需要使用`current_app`、`g`、`request`这些对象，会不会有问题？该如何使用？

#### app_context

`app_context`为我们提供了应用上下文环境，允许我们在外部使用应用上下文`current_app`、`g`

可以通过`with`语句进行使用

```python
>>> from flask import Flask
>>> app = Flask('')
>>> app.redis_cli = 'redis client'
>>> 
>>> from flask import current_app
>>> current_app.redis_cli   # 错误，没有上下文环境
报错
>>> with app.app_context():  # 借助with语句使用app_context创建应用上下文
...     print(current_app.redis_cli)
...
redis client
```

#### request_context

`request_context`为我们提供了请求上下文环境，允许我们在外部使用请求上下文`request`、`session`

可以通过with语句进行使用

```python
>>> from flask import Flask
>>> app = Flask('')
>>> request.args  # 错误，没有上下文环境
报错
>>> environ = {'wsgi.version':(1,0), 'wsgi.input': '', 'REQUEST_METHOD': 'GET', 'PATH_INFO': '/', 'SERVER_NAME': 'itcast server', 'wsgi.url_scheme': 'http', 'SERVER_PORT': '80'}  # 模拟解析客户端请求之后的wsgi字典数据
>>> with app.request_context(environ):  # 借助with语句使用request_context创建请求上下文
...     print(request.path)
...   
/
```



