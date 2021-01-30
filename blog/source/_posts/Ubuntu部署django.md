---
title: Ubuntu部署django
date: 2020-05-06 08:51:46
tags: 服务器
---

## 写在前面

又是工具人的一天

# 环境配置

- ubuntu16.04
- python3.7.1
- nginx
- uwsgi

# python3.7.1 安装

- 安装环境依赖

```shell
sudo apt-get install zlib1g-dev libbz2-dev libssl-dev libncurses5-dev libsqlite3-dev 
libreadline-dev tk-dev libgdbm-dev libdb-dev libpcap-dev xz-utils libexpat1-dev 
liblzma-dev libffi-dev libc6-dev
```

- 下载python3.7.1 安装包

```shell
wget https://www.python.org/ftp/python/3.7.1/Python-3.7.1.tgz
```

- 解压

```shell
tar -zxvf Python-3.7.1
```

- 进入解压目录

```shell
cd Python-3.7.1/
```

- 创建安装目录

```shel
sudo mkdir -p /usr/local/python3
```

- 编译安装

```shell
./configure --prefix=/usr/local/python3  --enable-optimizations
make
sudo make install
```

- 删除原来的软连接

```shell
rm -rf /usr/bin/python3
rm -rf /usr/bin/pip3
```

- 建立新的软连接

```shell
#添加python3的软链接
ln -s /usr/local/python3/bin/python3.7 /usr/bin/python3
#添加 pip3 的软链接
ln -s /usr/local/python3/bin/pip3.7 /usr/bin/pip3
```

- 检查版本

```shell
python3 -V
pip3 -V
```

# 获取项目文件

- 新建 or git 得到项目文件

- 创建虚拟环境

```shell
pip3 install virtualenv
# 清华源安装方式  pip3 install virtualenv -i https://pypi.python.org/simple/
python3 -m venv env
# env为虚拟环境名字
```

- 激活环境

```shell
source env/bin/activate
# 退出虚拟环境  deactivate
```

- 安装对应的库

```shell
pip install -r requirements.txt
```

# uwsgi配置

- 安装uwsgi

```shell
pip install uwsgi
```

- 测试uwsgi

```shell
uwsgi --http :8000 --file web/wsgi.py --static-map=/static=static
```

访问`localhost:8000` 看能否访问

- 新建uwsgi文件

```shell
vim uwsgi.ini
```

```shell
[uwsgi]
master = true
processes = 1
threads = 2
chdir = /www/wwwroot/project_plane/web/ # mangge.py 目录
wsgi-file= /www/wwwroot/project_plane/web/web/wsgi.py # wsgi.py 路径
http = 0.0.0.0:8080 # 运行端口
chmod-socket = 660
vacuum = true
max-requests = 1000
```

- 运行uwsgi文件

```shell
uwsgi --ini uwsgi.ini
```

查看否有报错

如果有则可以访问 `localhost:8080`

到这里uwsgi就配置完成了

## nginx配置

安装宝塔后新建站点

添加反向代理

到这里 就算部署完成了