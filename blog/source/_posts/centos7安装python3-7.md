---
title: centos7安装python3.7
date: 2020-02-28 16:30:08
tags: [系统]
description: centos7安装python3.7
---

#### 写在前面

由于centos7没有自带的python3,所以需要进行编译安装

## 安装编译 以及 相关的工具

```shell
yum -y groupinstall "Development tools"

yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel

yum install libffi-devel -y
```

## 下载安装包解压

```shell
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tar.xz
tar -xvJf  Python-3.7.0.tar.xz
```

## 编译安装

```shell
mkdir /usr/local/python3 #创建编译安装目录
cd Python-3.7.0
./configure --prefix=/usr/local/python3
make && make install
```

## 创建软连接

```shell
ln -s /usr/local/python3/bin/python3 /usr/local/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/local/bin/pip3
```

## 验证是否成功

```shell
python3 -V
pip3 -V
```