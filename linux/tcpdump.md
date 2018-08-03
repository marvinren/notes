# tcpdump简明教程

本文参照(A tcpdump Tutorial and Primer with Examples)[https://danielmiessler.com/study/tcpdump/]
tcpdump可以通过在服务端运行实现对协议包的抓取，然后通过wireshar对http进行分析

## 使用tcpdump在服务抓包

```sh
tcpdump -tttt -s0 -X -vv tcp port 8080 -w captcha.cap
```

这里的参数是这样的

* -tttt 输出最大程度可读的时间戳
* -s0 指定每一个包捕获的长度，单位是byte，使用-s0可以捕获整个包的内容
* -X 以hex和ASCII两种形式显示包的内容
* -vv 显示更加多的包信息
* tcp 指我们只捕获tcp流量
* port 8080 指我们只捕获端口8080的流量
* -w captcha.cap 指定捕获的流量结果输出到captcha.cap文件，便于分析使用

上述命令会保持运行，并将结果输出到 captcha.cap 文件中，在这个过程中，所有访问 8080 端口的 TCP 流量都会被捕获。当请求结束之后，我们可以使用 Ctrl+C 中断该命令的执行，这时候在当前目录下就可以看到生成了一个名为 captcha.cap 的文件。

## 使用Wireshark分析

接下来我们从服务器上下载这个captcha.cap文件到自己电脑上，使用 Wireshark 打开
```
scp account@ip:/path/to/captcha.cap .
```