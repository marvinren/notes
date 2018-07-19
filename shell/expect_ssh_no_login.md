# 使用expect实现ssh免登录
## 安装expect
```
sudo apt-get install expect
```

## 编写脚本
```
#!/usr/bin/expect -f
set ip [lindex $argv 0]
spawn ssh -i ./zhiming.pem zhiming@10.0.0.51 -p 50005
expect "*zhiming*"
send "8C4TTa7k4vhMphc1\r"
expect "*Opt or IP*"
send "$ip\r"
interact
```
直接直接执行即可