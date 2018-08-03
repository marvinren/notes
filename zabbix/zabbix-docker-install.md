# zabbix的安装，通过docker方式

## 1. 安装数据库mysql

```mysql
docker run --name zabbix-mysql-server --hostname zabbix-mysql-server \
-e MYSQL_ROOT_PASSWORD="123456" \
-e MYSQL_USER="zabbix" \
-e MYSQL_PASSWORD="123456" \
-e MYSQL_DATABASE="zabbix" \
-p 3306:3306  \
-d mysql:5.7 \
--character-set-server=utf8 --collation-server=utf8_bin
```

## 2. 创建zabbix-server

```sh
docker run  --name zabbix-server-mysql --hostname zabbix-server-mysql \
--link zabbix-mysql-server:mysql \
-e DB_SERVER_HOST="mysql" \
-e MYSQL_USER="zabbix" \
-e MYSQL_DATABASE="zabbix" \
-e MYSQL_PASSWORD="123456" \
-v /etc/localtime:/etc/localtime:ro \
-v /data/docker/zabbix/alertscripts:/usr/lib/zabbix/alertscripts \
-v /data/docker/zabbix/externalscripts:/usr/lib/zabbix/externalscripts \
-p 10051:10051 \
-d \
zabbix/zabbix-server-mysql
```

## 3.最后安装web-nginx

```sh
最后安装zabbix-web-nginx
docker run --name zabbix-web-nginx-mysql --hostname zabbix-web-nginx-mysql \
--link zabbix-mysql-server:mysql \
--link zabbix-server-mysql:zabbix-server \
-e DB_SERVER_HOST="mysql" \
-e MYSQL_USER="zabbix" \
-e MYSQL_PASSWORD="123456" \
-e MYSQL_DATABASE="zabbix" \
-e ZBX_SERVER_HOST="zabbix-server" \
-e PHP_TZ="Asia/Shanghai" \
-p 8000:80 \
-p 8443:443 \
-d \
zabbix/zabbix-web-nginx-mysql
```

登录访问测试
```sh
http://ip:8000
username: Admin
password: zabbix
```

## 4.安装docker-zabbix-agent

```sh
docker run --name zabbix-agent --link zabbix-server-mysql:zabbix-server -d zabbix/zabbix-agent:latest
```

然后在zabbix里添加Host，注意IP，需要使用docker网络里的IP，hostname要跟agent里的配置文件的Hostname一致


## 5. 配置主机

### 基础环境准备

安装zabbix的yum源，这里有必要提一点，阿里的yum源已经提供了zabbix3.0，不过我这里配置了好像是zabbix2.0的，可以直接从源上下载

```sh
rpm -ihv http://mirrors.aliyun.com/zabbix/zabbix/3.0/rhel/6/x86_64/zabbix-release-3.0-1.el6.noarch.rpm
```

### zabbix_agent安装配置

```sh
yum install zabbix-agent -y
vim /etc/zabbix/zabbix_agentd.conf
Server=server端ip地址                      #用于被动模式，数据获取
ServerActive=server端ip地址                #用于主动模式，数据提交
Hostname=<本机的名字>

systemctl start zabbix-agent.service
systemctl enable zabbix-agent.service
```

这里的主动模式还有被动模式的出发点都是agent端，所以agent主动向server端发送数据就成为主动模式，而等待server端主动获取数据则称为被动模式。

agent端安装比较简单.至此已经安装完毕，下来就是在server端添加agent端.

zabbix客户端自动安装脚本，考虑脚本的通用性，设置的手工输入zabbixserverIP这个变量。下面是一个网上的自动安装的脚本

```sh
#!/bin/bash
#suto install zabbix_agentd
#author :swh
echo  "Now  this shell will install zabbix_agentd autoly:please wait"
yum install net-snmp-devel libxml2-devel libcurl-devel  -y
echo "add zabbix group and user:"
groupadd zabbix
useradd   -r zabbix  -g  zabbix  -s /sbin/nologin
echo "download package -make and make install "
cd  /usr/local/src
wget -c  "http://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/3.0.3/zabbix-3.0.3.tar.gz"
tar -xzvf zabbix-3.0.3.tar.gz
cd zabbix-3.0.3
./configure --prefix=/usr/local/zabbix-3.0.3/ --enable-agent
make
make install
ret=$?     
if [ $? -eq 0 ]
  then     
        read  -p "please input zabbix_serverIP:"  zabbix_serverIP
        sed -i 's/Server=127.0.0.1/Server='$zabbix_serverIP'/' /usr/local/zabbix-3.0.3/etc/zabbix_agentd.conf
        sed -i 's/ServerActive=127.0.0.1/ServerActive='$zabbix_serverIP'/' /usr/local/zabbix-3.0.3/etc/zabbix_agentd.conf
        sed -i 's/Hostname=Zabbix server/Hostname='$HOSTNAME'/' /usr/local/zabbix-3.0.3/etc/zabbix_agentd.conf
        echo "zabbix install success,you need set hostname: $HOSTNAME"
         
else
        echo "install failed,please check"
fi 
/usr/local/zabbix-3.0.3/sbin/zabbix_agentd
if [ $? -eq 0 ]
  then
        echo "set zabbix_agentd start with system"
        echo "/usr/local/zabbix-3.0.3/sbin/zabbix_agentd start" >> /etc/rc.d/rc.local
else
        echo "start error,please check"
fi
```