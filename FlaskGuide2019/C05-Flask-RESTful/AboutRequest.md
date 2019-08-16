# 关于请求处理

Flask-RESTful 提供了`RequestParser`类，用来帮助我们检验和转换请求数据。

```python
from flask_restful import reqparse

parser = reqparse.RequestParser()
parser.add_argument('rate', type=int, help='Rate cannot be converted', location='args')
parser.add_argument('name')
args = parser.parse_args()
```

#### 使用步骤：

1. 创建`RequestParser`对象

2. 向`RequestParser`对象中添加需要检验或转换的参数声明

3. 使用`parse_args()`方法启动检验处理

4. 检验之后从检验结果中获取参数时可按照字典操作或对象属性操作

   ```python
   args.rate
   或
   args['rate']
   ```

   

## 参数说明

### 1 required

描述请求是否一定要携带对应参数，**默认值为False**

* True  强制要求携带

  若未携带，则校验失败，向客户端返回错误信息，状态码400

* False 不强制要求携带

  若不强制携带，在客户端请求未携带参数时，取出值为None

```python
class DemoResource(Resource):
    def get(self):
        rp = RequestParser()
        rp.add_argument('a', required=False)
        args = rp.parse_args()
        return {'msg': 'data={}'.format(args.a)}
```

### 2 help

参数检验错误时返回的错误描述信息

```python
rp.add_argument('a', required=True, help='missing a param')
```

### 3 action

描述对于请求参数中出现多个同名参数时的处理方式

* `action='store'`  保留出现的第一个， 默认
* `action='append'` 以列表追加保存所有同名参数的值

```python
rp.add_argument('a', required=True, help='missing a param', action='append')
```

### 4 type

描述参数应该匹配的类型，可以使用python的标准数据类型string、int，也可使用Flask-RESTful提供的检验方法，还可以自己定义

* 标准类型

  ```python
  rp.add_argument('a', type=int, required=True, help='missing a param', action='append')
  ```

* Flask-RESTful提供

  检验类型方法在`flask_restful.inputs`模块中

  * `url`

  * `regex(指定正则表达式)`  

    ```python
    from flask_restful import inputs
    rp.add_argument('a', type=inputs.regex(r'^\d{2}&'))
    ```

  * `natural`  自然数0、1、2、3...

  * `positive`  正整数 1、2、3...

  * `int_range(low ,high)`  整数范围

    ```python
    rp.add_argument('a', type=inputs.int_range(1, 10))
    ```

  * `boolean`

* 自定义

  ```python
  def mobile(mobile_str):
      """
      检验手机号格式
      :param mobile_str: str 被检验字符串
      :return: mobile_str
      """
      if re.match(r'^1[3-9]\d{9}$', mobile_str):
          return mobile_str
      else:
          raise ValueError('{} is not a valid mobile'.format(mobile_str))
          
  rp.add_argument('a', type=mobile)
  ```

### 5 location

描述参数应该在请求数据中出现的位置

```python
# Look only in the POST body
parser.add_argument('name', type=int, location='form')

# Look only in the querystring
parser.add_argument('PageSize', type=int, location='args')

# From the request headers
parser.add_argument('User-Agent', location='headers')

# From http cookies
parser.add_argument('session_id', location='cookies')

# From json
parser.add_argument('user_id', location='json')

# From file uploads
parser.add_argument('picture', location='files')
```

也可指明多个位置

```python
parser.add_argument('text', location=['headers', 'json'])
```











#### 