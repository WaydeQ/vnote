# usermod
- 添加用户到组
```
usermod -a -G  jboss_passport huxiaoming
```

- 将newuser2添加到组staff中：
```
usermod -G staff newuser2
```

- 修改newuser的用户名为newuser1：
```
usermod -l newuser1 newuser
```

- 锁定账号newuser1：
```
usermod -L newuser1
```

- 解除对newuser1的锁定：
```
usermod -U newuser1
```

###  ACL  设置权限
- 给tom赋予rw权限
```
setfacl -m u:tom:rw a 
```

-  查看文件夹/app的权限
```
getfacl /app
```

-  清除所有规则
```
setfacl -b jboss_passport
```