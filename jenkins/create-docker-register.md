# 服务器安装server

## 下载docker registry

```sh
docker pull
```

## 在服务端创建CA证书

执行命令生成证书

```sh
mkdir -p ~/docker/certs && openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout ~/docker/certs/domain.key \
  -x509 -days 365 -out ~/docker/certs/domain.crt
```

执行命令如下:

```sh
# renzq @ ubuntu in ~/workspace/deploy-tools/k8s/roles/docker-registry/files on git:master x [16:21:05] 
$ openssl req -newkey rsa:4096 -nodes -sha256 -keyout ./domain.key -x509 -days 3650 -out ./domain.crt             
Generating a 4096 bit RSA private key
............................................................++
..................................................++
writing new private key to './domain.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CN
State or Province Name (full name) [Some-State]:beijing
Locality Name (eg, city) []:beijing
Organization Name (eg, company) [Internet Widgits Pty Ltd]:ai
Organizational Unit Name (eg, section) []:ai
Common Name (e.g. server FQDN or YOUR name) []:asiainfo.com
Email Address []:renzq@asiainfo.com
```

PS: 这里要关注域名，客户机，需要的在/etc/hosts中添加域名

## 拷贝证书

将自签名的证书domain.crt放入系统的CA bundle文件中，使得操作系统信任我们的证书。

```sh
cp certs/domain.crt /etc/docker/certs.d/mydockerhub.com:5000/ca.crt
```

CentOS 6 / 7中bundle文件的位置在/etc/pki/tls/certs/ca-bundle.crt：

```sh
cat domain.crt >> /etc/pki/tls/certs/ca-bundle.crt
```

Ubuntu/Debian Bundle文件地址/etc/ssl/certs/ca-certificates.crt

```sh
cat domain.crt >> /etc/ssl/certs/ca-certificates.crt
```

# 测试

```sh
[root@t4 ~]# docker login hub.asiainfo.com:5050 -u admin -p 123456
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```