# 起步

**Flask-RESTful是用于快速构建REST API的Flask扩展。**

## 1 安装

```shell
pip install flask-restful
```

## 2 Hello World

```python
from flask import Flask
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)

class HelloWorldResource(Resource):
    def get(self):
        return {'hello': 'world'}

		def post(self):
        return {'msg': 'post hello world'}
      
api.add_resource(HelloWorldResource, '/')

# 此处启动对于1.0之后的Flask可有可无
if __name__ == '__main__':
    app.run(debug=True)
```

