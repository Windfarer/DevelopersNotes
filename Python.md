人类为什么要互相伤害，就不能统一一下版本
# 环境安装

## Python 2

### OS X
人生苦短，我用brew

```
brew install python
```

### CentOS
使用epel源
```
sudo yum install python27 python27-devel
```


### PIP
包管理工具必不可少

```
wget https://bootstrap.pypa.io/get-pip.py
sudo python get-pip.py
```

### 虚拟环境
```
pip install virtualenv
```


## Python 3

### OS X
```
brew install python3
```
### CentOS
使用ius源
```
sudo yum install python34u-devel
```

#### PIP
包管理工具必不可少

```
wget https://bootstrap.pypa.io/get-pip.py
sudo python3 get-pip.py
```

#### 虚拟环境
```
pip install pyvenv
```

# 日常开发
一定要建虚拟环境，否则Python的包会变得乱七八糟

启用虚拟环境后，即可正常使用```python```来启动创建时所指定版本的python，```pip```命令会装到这个与外界隔离的环境下，不会与外部环境或其他虚拟环境冲突
#### 创建虚拟环境
Python2

```
virtualenv -p python2.7 venv
```
或Python3

```
pyvenv venv
```
#### 启用虚拟环境
```
source venv/bin/activate
```
#### 退出虚拟环境
```
deactivate
```

#### 管理依赖
一般会把依赖的包和版本号写到```requirements.txt```文件里，然后在虚拟环境里一键安装

```
pip install -r requirements.txt
```
