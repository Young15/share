# 处理响应

## 需求

如何在不同的场景里返回不同的响应信息？

## 1 返回模板

使用`render_template`方法渲染模板并返回

例如，新建一个模板index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
我的模板html内容
<br/>{{ my_str }}
<br/>{{ my_int }}
</body>
</html>
```

后端视图

```python
from flask import render_template

@app.route('/')
def index():
    mstr = 'Hello 黑马程序员'
    mint = 10
    return render_template('index.html', my_str=mstr, my_int=mint)
```

## 2 重定向

```python
from flask import redirect

@app.route('/demo2')
def demo2():
    return redirect('http://www.itheima.com')
```

## 3 返回JSON

```python
from flask import jsonify

@app.route('/demo3')
def demo3():
    json_dict = {
        "user_id": 10,
        "user_name": "laowang"
    }
    return jsonify(json_dict)
```

## 4 自定义状态码和响应头

#### 1） 元祖方式

可以返回一个元组，这样的元组必须是 **(response, status, headers)** 的形式，且至少包含一个元素。 status 值会覆盖状态代码， headers 可以是一个列表或字典，作为额外的消息标头值。

```python
@app.route('/demo4')
def demo4():
    # return '状态码为 666', 666
    # return '状态码为 666', 666, [('Itcast', 'Python')]
    return '状态码为 666', 666, {'Itcast': 'Python'}
```

#### 2)  make_response方式

```python
@app.route('/demo5')
def demo5():
    resp = make_response('make response测试')
		resp.headers[“Itcast”] = “Python”
		resp.status = “404 not found”
    return resp
```

