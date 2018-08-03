# 1 创建ssh秘钥
```
yum install epel-release -y
yum install sshpass -y
ssh-keygen -t rsa
```

# 2 批量复制秘钥并授权
```
ansible web -m shell -a 'mkdir ~/.ssh' -k
ansible web -m copy -a 'src=~/.ssh/id_rsa.pub dest=~/.ssh/authorized_keys mode=0600' -k
```

# 3 测试
```
ssh 10.0.0.20
ssh 10.0.0.21
```