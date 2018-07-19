# gitbook使用入门

<!-- toc -->

## Gitbook Editor
最简单的使用方式就是用gitbook的编辑器，没有什么难度，但是我还是喜欢用命令行的方式。[下载位置](https://legacy.gitbook.com/editor)
另外：Gitbook editor可以支持与github同步

## Gitbook安装
1. 首先要安装node
2. 可以更新源`npm config set registry http://registry.npm.taobao.org`
3. gitbook -v

## Gitbook简单使用

可以使用[<gitbook简明教程>](http://www.chengweiyang.cn/gitbook/installation/README.html) 

```
gitbook init //初始化目录文件
gitbook help //列出gitbook所有的命令
gitbook --help //输出gitbook-cli的帮助信息
gitbook build //生成静态网页
gitbook serve //生成静态网页并运行服务器
gitbook build --gitbook=2.0.1 //生成时指定gitbook的版本, 本地没有会先下载
gitbook ls //列出本地所有的gitbook版本
gitbook ls-remote //列出远程可用的gitbook版本
gitbook fetch 标签/版本号 //安装对应的gitbook版本
gitbook update //更新到gitbook的最新版本
gitbook uninstall 2.0.1 //卸载对应的gitbook版本
gitbook build --log=debug //指定log的级别
gitbook builid --debug //输出错误信息
```

## Gitbook安装使用

1. book.json文件配置
```
{
    "plugins": ["atoc"],
    "pluginsConfig": {
        "atoc": {
            "addClass": true,
            "className": "atoc"
        }
    }
}
```
2. `gitbook install ./`安装插件
3. 在md文件里，`<!-- toc -->`