# java的gradle工程通过jenkins发布到kubernetes

## 准备

* 安装docker和kubernetes集群
* gogs部署

## 启动一个jenkins

通过docker方式启动jenkins

```sh
docker run --rm -d -u root -p 28080:8080 -v /data1:/data1 -v /data2/jenkins:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock -v "$HOME":/home jenkinsci/blueocean
```

之后可以登录http://localhost:28080配置jenkins，初始密码可通去/data2/jenkins_home去找，也可以通过docker logs查看该docker的后台日志，具体安装部署配置，可查看相关jenkins部署的相关文章

## 部署一个docker registry(TLS)

## 制作一个gralde的docker用于编译(含docker)

jenkins的pipeline是可以通过docker提供编译部署环境的，所以我们需要gradle的docker来进行编译打包，针对我们的需求，我们需要一个docker包含jdk8+gradle4然后进行编译，打包，并根据dockerfile将docker上传到docker registry中, 目前我想到的解决方案有如下几种：

* 方式1： 一条流水线两个stage，一个使用gradle:jdk8的docker进行编译打包，然后在下一个stage中不是用docker，使用jenkins所在docker中的docker环境上传。这里遇到的问题就是两个stage之间使用的源码工程不同，前一个打包出来的war，在下个stage里不存在，没有搞清具体原因。
* 方式2： 在一个stage里使用gradle:jdk8里gradle进行编译打包，然后通过gradle的docker插件，发布到registy上，不过这样就要求gradle:jdk8的docker里有docker支撑能力，那么就需要再次构建一个新的镜像，镜像包含docker-ce。
* 方式3： 与方式2类似，不同是不使用gradle的插件，而是直接使用命令行

根据上面的问题，我们需要构建一个包含gradle jdk8 docker的镜像，思路也简单在gradle:jdk8基础上增加docker支持，形成新的镜像。

开始的时候我向在镜像中完全重新安装一个docker，但其实可是使用host的docker，于是就有了如下的构建方法

* 首先创建一个Dockerfile，PS：这里只是用了docker的必要库libltdl7

```dockerfile
FROM gradle:jdk8
MAINTAINER marvinren <102325566@qq.com>
USER root

RUN apt-get update

RUN apt-get install libltdl7 -y
```

* 然后创建新的镜像,我只是建立在本地了，当然也可以上传到

```sh
docker build -t gradleanddocker:v1 .
```

* 然后启动东时候需要增加几个配置

```sh
docker run -it
-v /root/.gradle:/root/.gradle
-v /var/run/docker.sock:/var/run/docker.sock
-v /usr/bin/docker:/usr/bin/docker
gradleanddocker:v1
```

最后这个启动的参数可以写道jenkinsfile的配置里

## 部署一个nexus
## 编写Jenkinsfile

### 发布到k8s环境

```sh
kubectl run --image=hub.test.ai.com:5050/aialm-report-back --port=8080 aialm-report-back
kubectl expose deployment aialm-report-back --port=18080 --target-port=8080
kubectl run --image=hub.test.asiainfo.com:5050/aialm-report-front:v1 aialm-report-front
kubectl run --image=hub.test.asiainfo.com:5050/aialm-report-schedule:3 aialm-report-schedule
kubectl expose deployment aialm-report-front --type=NodePort --target-port=80
kubectl expose deployment aialm-report-schedule --port=8088 --target-port=48808

```


```sh
kubectl set image deployment/aialm-report-front aialm-report-front=hub.test.asiainfo.com:5050/aialm-report-front:25
```

## 编写Dockerfile
## 单元测试
发布流水线，单元测试应该是第一环节，在程序打包之前进行单元测试，如果编译失败或者单元测试不通过，那么此次CI的流水线就应该失败，通知开发人员进行修复。这里需要强调的是单元测试需要先编译，编译错误也会在这个时候出现。
对于gradle的程序，需要执行`gradle test`来执行单元测试，具体的单元测试的框架/方式，需要由开发来完成

## 自动测试
## liquibase
## 发布测试环境


## 一些技巧

### 使用环境变量
可是用`printenv`打印所有可用的环境变量