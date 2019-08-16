# 路由

```python
@app.route("/itcast")
def view_func():
    return "hello world"
```

## 1 查询路由信息

* 命令行方式

  ```shell
  flask routes
  ```

  ```shell
  Endpoint  Methods  Rule
  --------  -------  -----------------------
  index     GET      /
  static    GET      /static/<path:filename>
  ```

* 在程序中获取

  在应用中的url_map属性中保存着整个Flask应用的路由映射信息，可以通过读取这个属性获取路由信息

  ```python
  print(app.url_map)
  ```

  如果想在程序中遍历路由信息，可以采用如下方式

  ```python
  for rule in app.url_map.iter_rules():
      print('name={} path={}'.format(rule.endpoint, rule.rule))
  ```

#### 需求

通过访问`/`地址，以json的方式返回应用内的所有路由信息

#### 实现

```python
@app.route('/')
def route_map():
    """
    主视图，返回所有视图网址
    """
    rules_iterator = app.url_map.iter_rules()
    return json.dumps({rule.endpoint: rule.rule for rule in rules_iterator})
```

## 2 指定请求方式

在 Flask 中，定义路由其默认的请求方式为：

- GET
- OPTIONS(自带)
- HEAD(自带)

利用`methods`参数可以自己指定一个接口的请求方式

```python
@app.route("/itcast1", methods=["POST"])
def view_func_1():
    return "hello world 1"
  
@app.route("/itcast2", methods=["GET", "POST"])
def view_func_2():
    return "hello world 2"
```

