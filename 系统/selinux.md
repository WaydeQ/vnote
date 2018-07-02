# selinux
### 查看状态
```
getenforce
sestatus -v
```

###  临时关闭,设置SELinux成为permissive模式
```
setenforce 0 
```

### 关闭selinux
```
sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config
设置后需要重启才能生效
sestatus
```