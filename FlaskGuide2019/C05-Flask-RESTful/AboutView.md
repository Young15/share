# 关于视图

## 1 为路由起名

通过endpoint参数为路由起名

```python
api.add_resource(HelloWorldResource, '/', endpoint='HelloWorld')
```



## 2 蓝图中使用

```python
from flask import Flask, Blueprint
from flask_restful import Api, Resource

app = Flask(__name__)

user_bp = Blueprint('user', __name__)

user_api = Api(user_bp)

class UserProfileResource(Resource):
    def get(self):
        return {'msg': 'get user profile'}

user_api.add_resource(UserProfileResource, '/users/profile')

app.register_blueprint(user_bp)
```



## 3 装饰器

使用`method_decorators`添加装饰器

* 为类视图中的所有方法添加装饰器


  ```python
  def decorator1(func):
      def wrapper(*args, **kwargs):
          print('decorator1')
          return func(*args, **kwargs)
      return wrapper
  
  
  def decorator2(func):
      def wrapper(*args, **kwargs):
          print('decorator2')
          return func(*args, **kwargs)
      return wrapper
  
  
  class DemoResource(Resource):
      method_decorators = [decorator1, decorator2]
  
      def get(self):
          return {'msg': 'get view'}
      
      def post(self):
          return {'msg': 'post view'}
  ```

* 为类视图中不同的方法添加不同的装饰器


  ```python
  class DemoResource(Resource):
      method_decorators = {
          'get': [decorator1, decorator2],
          'post': [decorator1]
      }
  
      # 使用了decorator1 decorator2两个装饰器
      def get(self):
          return {'msg': 'get view'}
  
      # 使用了decorator1 装饰器
      def post(self):
          return {'msg': 'post view'}
  
      # 未使用装饰器
      def put(self):
          return {'msg': 'put view'}
  ```

  

