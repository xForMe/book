# python 

## mac安装

```shell
brew install python
```

查看版本

```
cd /usr/local/Cellar/python
```

配置环境变量

```shell
export PATH=${PATH}:/usr/local/Cellar/python/3.7.2/bin
alias python="/usr/local/Cellar/python/3.7.2/bin/python3"
alias pip="/usr/local/Cellar/python/3.7.2/bin/pip3"

```



## python 工程

python目录结构

```
Foo/
|-- bin/
|   |-- foo
|
|--conf/
|　　|--__init__.py
|　　|--settings.py
|
|-- foo/
|   |-- tests/
|   |   |-- __init__.py
|   |   |-- test_main.py
|   |
|   |-- __init__.py
|   |-- main.py
|
|-- docs/
|   |-- conf.py
|   |-- abc.rst
|-- makefile
|-- setup.py
|-- requirements.txt
|-- README
|-- MANIFEST.in
```

- `bin/`: 存放项目的一些可执行文件
- ```conf/```:存放项目配置文件
- `foo/`: 存放项目的所有源代码。(1) 源代码中的所有模块、包都应该放在此目录。不要置于顶层目录。(2) 其子目录`tests/`存放单元测试代码； (3) 程序的入口最好命名为`main.py`。
- `docs/`: 存放一些文档
- `setup.py`: 安装、部署、打包的脚本
- `requirements.txt`: 存放软件依赖的外部Python包列表
- `README`: 项目说明文件
- makefile: python编译
- MANIFEST.in: python[打包文件](https://docs.python.org/2/distutils/sourcedist.html)

