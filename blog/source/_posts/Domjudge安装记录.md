---
title: Domjudge安装记录
date: 2020-12-01 20:51:46
tags: 服务器

---

本文参考于：https://github.com/cn-xcpc-tools/cn-xcpc-docs 

### 前期准备

- 腾讯云学生机
- Ubuntu Server 18.04.1 LTS 64位

### 安装依赖包和功能

```shell
sudo apt-get upgrade && sudo apt-get update
```

```shell
sudo apt install gcc g++ make zip unzip mariadb-server \
        apache2 php php-cli libapache2-mod-php php-zip \
        php-gd php-curl php-mysql php-json php-xml php-intl php-mbstring \
        acl bsdmainutils ntp phpmyadmin python-pygments \
        libcgroup-dev linuxdoc-tools linuxdoc-tools-text \
        groff texlive-latex-recommended texlive-latex-extra \
        texlive-fonts-recommended texlive-lang-european composer
```

安装时选择 `apache2`

```shell
sudo apt install libcurl4-gnutls-dev libjsoncpp-dev libmagic-dev
```

```shell
sudo phpenmod json
```

### 编译Domjudge

 [domjudge-7.1.1.tar.gz](\blog\upload\Domjudge\domjudge-7.1.1.tar.gz) 

```shell
cd Downloads
wget https://www.domjudge.org/releases/domjudge-7.1.1.tar.gz
```

```shell
tar -zxvf domjudge-7.1.1.tar.gz
```

```shell
cd domjudge-7.1.1
./configure --prefix=/opt/domjudge --with-baseurl=127.0.0.1
make domserver && sudo make install-domserver
make docs && sudo make install-docs
```

### 配置数据库

```
cd /opt/domjudge/domserver
sudo bin/dj_setup_database -u root install
```


### 配置 Web 服务器

```shell
cd /opt/domjudge/domserver
sudo ln -s /opt/domjudge/domserver/etc/apache.conf /etc/apache2/conf-available/domjudge.conf
sudo a2enmod rewrite
sudo a2enconf domjudge
sudo systemctl reload apache2
sudo chown www-data:www-data -R /opt/domjudge/domserver/webapp/var/*
```
现在你应该可以访问 `http://127.0.0.1/domjudge` 或者公网并使用用户名 `admin` 与 `/opt/domjudge/domserver/etc/initial_admin_password.secret` 内生成的密码登录 domjudge 后台了。

### 配置 MySQL

编辑 `/etc/mysql/conf.d/mysql.cnf`，追加以下内容：

```shell
[mysqld]
max_connections = 1000
max_allowed_packet = 16MB
innodb_log_file_size = 48MB
```
其中 `max_allowed_packet` 数值改成两倍于题目测试数据文件的大小，`innodb_log_file_size` 数值改成十倍于题目测试数据文件的大小。

```shell
sudo systemctl restart mysql
```
### 配置php

编辑 `/opt/domjudge/domserver/etc/apache.conf`，取消以下几行内容前的注释：

```shell
<IfModule mod_php7.c>
php_value max_file_uploads      101
php_value upload_max_filesize   128M
php_value post_max_size         128M
php_value memory_limit          512M
</IfModule>
```

编辑 `/etc/php/7.2/apache2/php.ini`，搜索 `date.timezone` 关键字，取消其行前注释，并将其值设为 `Asia/Shanghai`。搜索 `max_execution_time` 关键字，将其值由30改为300，防止生成队伍密码时 PHP 执行超时。

```shell
sudo systemctl restart apache2
```
### 配置 Apache

编辑 `/etc/apache2/apache2.conf`，搜索 `KeepAlive` 关键字，将其值设为 `Off`，并在其后新增一行内容：

```shell
MaxClients 1000
```
```shell
sudo systemctl restart apache2
```
## 搭建judgehost

## 准备工作

### 安装依赖包和功能

```shell
sudo apt-get upgrade && sudo apt-get update
sudo apt install make sudo debootstrap libcgroup-dev lsof zip unzip\
        php-cli php-curl php-json php-xml php-zip procps \
        gcc g++ openjdk-8-jre-headless \
        openjdk-8-jdk ghc fp-compiler \
        libcurl4-gnutls-dev libjsoncpp-dev libmagic-dev
```

### 编译 Domjudge

```shell
cd Downloads
wget https://www.domjudge.org/releases/domjudge-7.1.1.tar.gz
tar -zxvf domjudge-7.1.1.tar.gz
cd domjudge-7.1.1
./configure --prefix=/opt/domjudge --with-baseurl=127.0.0.1
make judgehost && sudo make install-judgehost
```

这会将 judgehost 安装在 `/opt/domjudge/judgehost` 里。

## 配置 judgehost

### 添加用户

```shell
useradd -d /nonexistent -U -M -s /bin/false domjudge-run
# 如果 judgehost 拥有多个 CPU 核心，你可以添加额外的用户来支持绑定
# 不同的 judgehost 进程到不同的 CPU 核心上，如下：
useradd -d /nonexistent -U -M -s /bin/false domjudge-run-0
useradd -d /nonexistent -U -M -s /bin/false domjudge-run-1
useradd -d /nonexistent -U -M -s /bin/false domjudge-run-2
useradd -d /nonexistent -U -M -s /bin/false domjudge-run-3
# ... 如果有更多的 CPU 核心，请自行添加更多的用户
```

### 配置 sudoers

将 /opt/domjudge/judgehost/etc/sudoers-domjudge 复制到 /etc/sudoers.d/ 目录下。

```shell
sudo cp /opt/domjudge/judgehost/etc/sudoers-domjudge /etc/sudoers.d/
```

### 修改 rest 密码

使用 vim 等文本编辑器编辑 /opt/domjudge/judgehost 目录下 etc/restapi.secret 这个文件。文件的格式为：

```shell
default http://example.edu/domjudge/api/  judgehosts  MzfJYWF5agSlUfmiGEy5mgkfqU
```
`注意不要有空行`

格式为 endpoint api_url username password ，endpoint 可以保持不变，api_url 根据 judgeserver 的地址进行修改，username 和 password 要与 domserver上的 etc/restapi.secret 保持一致。

### 构建 chroot 环境 

使用 vim 等文本编辑器编辑 ~/domjudge/judgehost/bin/dj_make_chroot 脚本，将 ubuntu 镜像改为国内源。（第 172 行）

```shell
# Ubuntu mirror, modify to match closest mirror
[ -z "$DEBMIRROR" ] && DEBMIRROR="http://mirrors.aliyun.com/ubuntu/"
```

修改之后保存并运行此脚本(bash 运行)。这一步会从源上下载必要的软件包，所以请耐心等待。

### 设置cgroup

使用 vim 等文本编辑器编辑 /etc/default/grub 这个文件，对其中的这一行做如下修改：

```shell
GRUB_CMDLINE_LINUX_DEFAULT="quiet cgroup_enable=memory swapaccount=1"
```

如果下面的步骤仍然报错 那就修改

```shell
GRUB_CMDLINE_LINUX="quiet cgroup_enable=memory swapaccount=1"
```

然后执行：

```shell
update-grub
```

之后**重启计算机**。

### 启动 judgehost

如果需要使用cgroup，则每次重启之后都要运行 `/opt/domjudge/judgehost/bin/create_cgroups`
`/opt/domjudge/judgehost/bin/judgedaemon` 即可启动，若提示 `error: Call to undefined function curl_init()`，则可以安装 php-curl 解决

如果想要断开连接并且还可以正常使用判题机，这样来

```shell
 bash /opt/domjudge/judgehost/bin/create_cgroups
 nohup /opt/domjudge/judgehost/bin/judgedaemon & 
```

退出连接的时候记得用exit，不要直接关闭 到这里，domjudge就可以正常投入使用了。

### 配置多 judgehost 的 systemd 及 rsyslog

使用 vim 等文本编辑器在 /lib/systemd/system 下新建一个文本文件叫做 create-cgroups.service，写入下列内容：

```shell
[Unit]
Description=Make sure cgroups exist for DOMjudge judgedaemon

[Service]
Type=oneshot
ExecStart=/opt/domjudge/judgehost/bin/create_cgroups
RemainAfterExit=true
```

在 /lib/systemd/system 下再新建一个文本文件叫做 [domjudge-judgehost@.service](mailto:domjudge-judgehost@.service)，写入下列内容： 注意 `User=<username>` 要用自己编译 judgehost 时的用户名，因为 /etc/sudoers.d/sudoers-domjudge 列表里是当时的用户

```shell
[Unit]
Description=DOMjudge JudgeDaemon
Requires=create-cgroups.service
After=create-cgroups.service
After=network.target

[Service]
Type=simple

ExecStart=/opt/domjudge/judgehost/bin/judgedaemon -n %i
User=<username>

Restart=always
RestartSec=3
PrivateTmp=yes

StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=judgehost-%i

[Install]
WantedBy=multi-user.target
```

在 /etc/rsyslog.d/ 下新建一个文本文件叫做 judgehost.conf，写入下列内容：

```shell
:programname, isequal, "judgehost-0" /var/log/judgehost/judgehost-0.log
:programname, isequal, "judgehost-1" /var/log/judgehost/judgehost-1.log
:programname, isequal, "judgehost-2" /var/log/judgehost/judgehost-2.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-3.log
```

重启日志服务，启动四个 judgehost：

```shell
sudo systemctl restart rsyslog
sudo systemctl start domjudge-judgehost@0
sudo systemctl start domjudge-judgehost@1
sudo systemctl start domjudge-judgehost@2
sudo systemctl start domjudge-judgehost@3
```

judgedaemon的日志会保存在 /var/log/judgehost 下

一些杂项

```shell script
./configure --prefix=/opt/domjudge --with-baseurl=127.0.0.1

wget https://dup4.top/attachments/domjudge-7.1.1.tar.gz

default	http://localhost/domjudge/api	judgehost	123456

sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-0
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-1
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-2
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-3
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-4
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-5
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-6
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-7
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-8
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-9
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-10
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-11
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-12
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-13
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-14
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-15
sudo useradd -d /nonexistent -U -M -s /bin/false domjudge-run-16

sudo vim /lib/systemd/system/domjudge-judgehost@.service

sudo vim /etc/rsyslog.d/judgehost.conf

:programname, isequal, "judgehost-0" /var/log/judgehost/judgehost-0.log
:programname, isequal, "judgehost-1" /var/log/judgehost/judgehost-1.log
:programname, isequal, "judgehost-2" /var/log/judgehost/judgehost-2.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-3.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-4.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-5.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-6.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-7.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-8.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-9.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-10.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-11.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-12.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-13.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-14.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-15.log
:programname, isequal, "judgehost-3" /var/log/judgehost/judgehost-16.log

#重启judge
sudo systemctl restart rsyslog
sudo systemctl start domjudge-judgehost@0
sudo systemctl start domjudge-judgehost@1
sudo systemctl start domjudge-judgehost@2
sudo systemctl start domjudge-judgehost@3
sudo systemctl start domjudge-judgehost@4
sudo systemctl start domjudge-judgehost@5
sudo systemctl start domjudge-judgehost@6
sudo systemctl start domjudge-judgehost@7
sudo systemctl start domjudge-judgehost@8
sudo systemctl start domjudge-judgehost@9
sudo systemctl start domjudge-judgehost@10
sudo systemctl start domjudge-judgehost@11
sudo systemctl start domjudge-judgehost@12
sudo systemctl start domjudge-judgehost@13
sudo systemctl start domjudge-judgehost@14
sudo systemctl start domjudge-judgehost@15
sudo systemctl start domjudge-judgehost@16

#设置开机启动
sudo systemctl enable domjudge-judgehost@0
sudo systemctl enable domjudge-judgehost@1
sudo systemctl enable domjudge-judgehost@2
sudo systemctl enable domjudge-judgehost@3
sudo systemctl enable domjudge-judgehost@4
sudo systemctl enable domjudge-judgehost@5
sudo systemctl enable domjudge-judgehost@6
sudo systemctl enable domjudge-judgehost@7
sudo systemctl enable domjudge-judgehost@8
sudo systemctl enable domjudge-judgehost@9
sudo systemctl enable domjudge-judgehost@10
sudo systemctl enable domjudge-judgehost@11
sudo systemctl enable domjudge-judgehost@12
sudo systemctl enable domjudge-judgehost@13
sudo systemctl enable domjudge-judgehost@14
sudo systemctl enable domjudge-judgehost@15
sudo systemctl enable domjudge-judgehost@16

```