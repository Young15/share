# 环境安装

## 1. 复习虚拟环境和pip的命令

```shell
# 虚拟环境
mkvirtualenv  # 创建虚拟环境
rmvirtualenv  # 删除虚拟环境
workon  # 进入虚拟环境、查看所有虚拟环境
deactivate  # 退出虚拟环境

# pip
pip install  # 安装依赖包
pip uninstall  # 卸载依赖包
pip list  # 查看已安装的依赖包
pip freeze  # 冻结当前环境的依赖包
```

## 2. 创建虚拟环境

```shell
mkvirtualenv flask -p python3
```

注意需要联网

## 3. 安装Flask

使用flask 1.0.2版本，注意需要联网

```shell
pip install flask
```
