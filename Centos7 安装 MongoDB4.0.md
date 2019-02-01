# Centos7 安装 MongoDB4.0

## 简介

MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。他支持的数据结构非常松散，是类似json的bson格式，因此可以存储比较复杂的数据类型。Mongo最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

MongoDB 是一个基于分布式文件存储的数据库。由 C++ 语言编写。旨在为 WEB 应用提供可扩展的高性能数据存储解决方案。

MongoDB 是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。 

## 源码方式安装

### 安装包下载

[MongoDB官网下载地址](https://www.mongodb.com/download-center/community)，打开地址后选择Community Server,然后选择Linux下载对应的版本安装包即可，但是下拉列表中有很多Linux安装包，Centos选择带RHEL的安装包，这里选择**RHEl 7 Linux 64-bit x64**版本的安装包即可，下载后上传到对应Linux服务器。 

### MongoDB安装

在安装时均使用root用户，如果非root用户则在命令前加sudo命令，用来以root身份运行 

#### 解压文件

```
tar -zxvf mongodb-linux-x86_64-rhel70-4.0.5.tgz -C /usr/local
cd /usr/local
mv mongodb-linux-x86_64-rhel70-4.0.5 mongodb
```



#### 添加环境变量

在/etc/profile文件末尾插入如下内容

```
######mongodb
export MONGODB_HOME=/usr/local/mongodb  
export PATH=$PATH:$MONGODB_HOME/bin
```

修改保存后要重启系统配置，执行命令如下 

```
source /etc/profile
```

#### 创建mongodb数据文件和日志文件的存放位置

```
cd /usr/local/mongodb
mkdir -p data/db logs
chmod -R 777 data/db
touch logs/mongodb.log
```

#### 创建启动文件

`vi mongodb.conf `

```
dbpath=/usr/local/mongodb/data/db  #数据文件存放目录
logpath=/usr/local/mongodb/logs/mongodb.log #日志存放目录
port=27017 #端口
fork=true #以守护程序的方式启用，即在后台运行
logappend=true
maxConns=5000
storageEngine = wiredTiger
bind_ip = 0.0.0.0
```

### 启动数据库

```
# 启动数据库
mongod --config /usr/local/mongodb/mongodb.conf
# 访问数据库
mongo
```

## yum方式安装

### 配置MongoDB的yum源

`vim /etc/yum.repos.d/mongodb-org-4.0.repo `

```
[mongodb-org-4.0]  
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
```

### 安装MongoDB

安装命令

```
yum -y install mongodb-org
```

查看mongo安装位置 

```
whereis mongod
```

查看修改配置文件

```
vim /etc/mongod.conf
```

### 启动MongoDB

```
systemctl start mongod.service
```

设置开机启动

```
systemctl enable mongod.service
```

启动mongo shell

```
mongo
```

查看数据库

```
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
```

### 设置mongodb远程访问

编辑mongod.conf注释bindIp,并重启mongodb

`vim /etc/mongod.conf`

![](https://images2015.cnblogs.com/blog/1152574/201706/1152574-20170601161847868-46882348.png)

重启mongodb

```
systemctl restart mongod.service
```

