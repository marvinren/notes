# 使用jenkins-cli连接

jenkins CLI是一个命令行工具，对于我这种命令行控来说，比较喜欢用，所以就记录下来了。

* 首先，要启动jenkins的服务
* 然后配置jenkins里的Configure Global Security，允许匿名访问
* 打开http://localhost:8080/cli下载jar包
* java -jar jenkins-cli.jar -s http://localhost:8080 help

如上就完成了访问

## 常用命令

```sh
# 查看帮助
java -jar jenkins-cli.jar -s http://10.12.2.147:8080 -auth admin:admin help

# 查看jobs
java -jar jenkins-cli.jar -s http://10.12.2.147:8080 -auth admin:admin list-jobs

# 运行job
java -jar jenkins-cli.jar -s http://10.12.2.147:8080 -auth admin:admin build vue-admin-ui/master

# 查看console
java -jar jenkins-cli.jar -s http://10.12.2.147:8080 -auth admin:admin console vue-admin-ui/master

# 重启
java -jar jenkins-cli.jar -s http://10.12.2.147:8080 -auth admin:admin restart
```