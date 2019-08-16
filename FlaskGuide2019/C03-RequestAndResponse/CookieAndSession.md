# Cookie与Session

## 1 Cookie

### 设置

```python
from flask import Flask, make_response

app = Flask(__name__)

@app.route('/cookie')
def set_cookie():
	resp = make_response('set cookie ok')
	resp.set_cookie('username', 'itcast')
	return resp
```

设置有效期

```python
@app.route('/cookie')
def set_cookie():
    response = make_response('hello world')
    response.set_cookie('username', 'itheima', max_age=3600)
    return response
```

### 读取

```python
from flask import request

@app.route('/get_cookie')
def get_cookie():
    resp = request.cookies.get('username')
    return resp
```

### 删除

```python
from flask import request

@app.route('/delete_cookie')
def delete_cookie():
    response = make_response('hello world')
    response.delete_cookie('username')
    return response
```

## 2 Session

需要先设置SECRET_KEY

```python
class DefaultConfig(object):
    SECRET_KEY = 'fih9fh9eh9gh2'
   
app.config.from_object(DefaultConfig)

或者直接设置
app.secret_key='xihwidfw9efw'
```

### 设置

```python
from flask import session

@app.route('/set_session')
def set_session():
    session['username'] = 'itcast'
    return 'set session ok'
```

### 读取

```python
@app.route('/get_session')
def get_session():
    username = session.get('username')
    return 'get session username {}'.format(username)
```

### 思考

flask将session数据保存到了哪里？