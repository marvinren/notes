# Ubuntu下安装使用zsh

## 介绍

* shell
* zsh
* oh my shell

由于这个是笔记，介绍的内容就略去了，总之zsh很牛X，我最欣赏的就是自动补全

## 配置zsh与oh-my-zsh终极配置

### 安装zsh
直接从源里就可以安装，命令如下：
```
sudo apt-get instlal zsh
```

### 配置zsh
zsh的配置比较麻烦，从网上下载个现成的把，我准备收集几个

* 等我收集1
* 等我收集2

.zshrc拷贝到home目录下即可

### 取代bash
```
sudo usermod -s /bin/zsh username
```
如果要切换回去bash：
```
chsh -s /bin/bash
```

### 安装oh-my-zsh

* 直接从git下载安装包
```
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

* 备份zshrc并替换
```
cp ~/.zshrc ~/.zshrc.orig
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```


也可直接从脚本安装
```
cd oh-my-zsh/tools
./install.sh
```

### 设置主题
oh-my-zsh集成了大量的主题, 位于oh-my-zsh/theme

配置主题, 可以通过修改~/.zshrc中的环境变量ZSH_THEME来完成
```
ZSH_THEME="agnoster" # (this is one of the fancy ones)
```
如果你觉得主题太多你可以选择使用随机模式, 来由系统随机选择
```
ZSH_THEME="random" # (...please let it be pie... please be some pie..)
```
我这里推荐使用`ys`，相关主题介绍可以参看`https://github.com/robbyrussell/oh-my-zsh/wiki/themes`

## 更新 oh-myzsh

更新oh-my-zsh

修改`~/.zshrc
```
disable_update_prompt = true
```
禁用自动升级, 修改~/.zshrc
```
disable_auto_update = true
```
当然你也可以选择手动更新
```
upgrade_oh_my_zsh
```

## 卸载oh-my-zsh

如果你想卸载oh-my-zsh, 只需要执行uninstall_oh_my_zsh zsh， 从命令行运行. 这将删除本身和恢复你以前的bash或者zsh配置.
```
uninstall_oh_my_zsh zsh
```

