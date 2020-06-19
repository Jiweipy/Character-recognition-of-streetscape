# 远程使用Jupyter Notebook

## 服务器端配置：

### 1 进入到虚拟环境

> $ conda activate torch-gpu

### 2 生成配置文件

> $ jupyter notebook --generate-config

上述代码会在~/.jupyter下生成一个jupyter_notebook_config.py文件。

###  3 修改配置文件

> $ vi ~/.jupyter/jupyter_notebook_config.py
>

添加一下代码：

```python
 c.NotebookApp.ip='*'		#表示同一网络的主机都可访问
 c.NotebookApp.password = u'sha密文'
 c.NotebookApp.open_browser = False
 c.NotebookApp.port =8888 #随便指定一个端口(这个需要看是不是冲突)
```

### 4 生成sha密文

> $ ipython

```python
 In [1]: from notebook.auth import passwd
 In [2]: passwd()
 Enter password:
 Verify password:
 Out[2]:'sha1:xxxxxxxxx:xxxxxxxxxxxxxxxxxxxxx'
```

### 5 把密文填充到配置文件里

> $ vi ~/.jupyter/jupyter_notebook_config.py

修改以下代码

```python
 c.NotebookApp.ip='*'		#表示同一网络的主机都可访问
 c.NotebookApp.password = u'sha1:xxxxxxxxx:xxxxxxxxxxxxxxxxxxxxx'
 c.NotebookApp.open_browser = False
 c.NotebookApp.port =8888 #随便指定一个端口(这个需要看是不是冲突)
```

### 6 运行 jupyter notebook

> $ jupyter notebook

## 客户端配置：

### 1 在浏览器中打开网址

### 2 输入上述步骤四设置的密码

### 3 运行


