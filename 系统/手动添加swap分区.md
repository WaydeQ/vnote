# 手动添加swap分区
### 创建要作为swap分区的文件
- 增加1GB大小的交换分区，则命令写法如下，其中的count等于想要的块的数量（bs*count=文件大小）。 
```
dd if=/dev/zero of=/opt/swapfile bs=1M count=10240
```

#### 格式化为交换分区文件
- 建立swap的文件系统
```
mkswap /opt/swapfile 
```

#### 启用交换分区文件
- 启用swap文件
```
swapon /opt/swapfile 
```

#### 使系统开机时自启用(加到fstab)
- 在文件/etc/rc.local中添加一行： 
```
echo "swapon /opt/swapfile" >>/etc/rc.local
```