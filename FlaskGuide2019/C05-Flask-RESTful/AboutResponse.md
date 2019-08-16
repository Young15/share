# 关于响应处理

## 1 序列化数据

Flask-RESTful 提供了marshal工具，用来帮助我们将数据序列化为特定格式的字典数据，以便作为视图的返回值。

```python
from flask_restful import Resource, fields, marshal_with

resource_fields = {
    'name': fields.String,
    'address': fields.String,
    'user_id': fields.Integer
}

class Todo(Resource):
    @marshal_with(resource_fields, envelope='resource')
    def get(self, **kwargs):
        return db_get_todo() 
```

也可以不使用装饰器的方式

```python
class Todo(Resource):
    def get(self, **kwargs):
        data = db_get_todo()
        return marshal(data, resource_fields)
```

#### 示例

```python
# 用来模拟要返回的数据对象的类
class User(object):
    def __init__(self, user_id, name, age):
        self.user_id = user_id
        self.name = name
        self.age = age

resoure_fields = {
        'user_id': fields.Integer,
        'name': fields.String
    }

class Demo1Resource(Resource):
    @marshal_with(resoure_fields, envelope='data1')
    def get(self):
        user = User(1, 'itcast', 12)
        return user
      
class Demo2Resource(Resource):
    def get(self):
        user = User(1, 'itcast', 12)
        return marshal(user, resoure_fields, envelope='data2')
```



## 2 定制返回的JSON格式

### 需求

想要接口返回的JSON数据具有如下统一的格式

```json
{"message": "描述信息", "data": {要返回的具体数据}}
```

在接口处理正常的情况下， message返回ok即可，但是若想每个接口正确返回时省略message字段

```python
class DemoResource(Resource):
    def get(self):
        return {'user_id':1, 'name': 'itcast'}
```

对于诸如此类的接口，能否在某处统一格式化成上述需求格式？

```json
{"message": "OK", "data": {'user_id':1, 'name': 'itcast'}}
```

### 解决

**Flask-RESTful的Api对象提供了一个`representation`的装饰器，允许定制返回数据的呈现格式**

```python
api = Api(app)

@api.representation('application/json')
def handle_json(data, code, headers):
    # TODO 此处添加自定义处理
    return resp
```

Flask-RESTful原始对于json的格式处理方式如下：

代码出处：`flask_restful.representations.json`

```python
from flask import make_response, current_app
from flask_restful.utils import PY3
from json import dumps


def output_json(data, code, headers=None):
    """Makes a Flask response with a JSON encoded body"""

    settings = current_app.config.get('RESTFUL_JSON', {})

    # If we're in debug mode, and the indent is not set, we set it to a
    # reasonable value here.  Note that this won't override any existing value
    # that was set.  We also set the "sort_keys" value.
    if current_app.debug:
        settings.setdefault('indent', 4)
        settings.setdefault('sort_keys', not PY3)

    # always end the json dumps with a new line
    # see https://github.com/mitsuhiko/flask/pull/1262
    dumped = dumps(data, **settings) + "\n"

    resp = make_response(dumped, code)
    resp.headers.extend(headers or {})
    return resp
```

为满足需求，做如下改动即可

```python
@api.representation('application/json')
def output_json(data, code, headers=None):
    """Makes a Flask response with a JSON encoded body"""

    # 此处为自己添加***************
    if 'message' not in data:
        data = {
            'message': 'OK',
            'data': data
        }
    # **************************
    
    settings = current_app.config.get('RESTFUL_JSON', {})

    # If we're in debug mode, and the indent is not set, we set it to a
    # reasonable value here.  Note that this won't override any existing value
    # that was set.  We also set the "sort_keys" value.
    if current_app.debug:
        settings.setdefault('indent', 4)
        settings.setdefault('sort_keys', not PY3)

    # always end the json dumps with a new line
    # see https://github.com/mitsuhiko/flask/pull/1262
    dumped = dumps(data, **settings) + "\n"

    resp = make_response(dumped, code)
    resp.headers.extend(headers or {})
    return resp
```



