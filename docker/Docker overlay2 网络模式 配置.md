# Docker overlay2 网络模式 配置
#### overlay和overlay2的区别

##### 本质区别是镜像层之间共享数据的方法不同
- overlay共享数据方式是通过硬连接
- 而overlay2是通过每层的 lower文件
```
cd /etc/docker/
daemon.json
{
"storage-driver": "overlay2",
"storage-opts": [
"overlay2.override_kernel_check=true"
]
}

docker info |grep -i storage
```

#### 升级内核
```
1.导入 elrepo 的key
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org

2. 安装 elrepo 源
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm

3. 在yum的ELRepo源中，mainline 为最新版本的内核
安装 ml 的内核
yum --enablerepo=elrepo-kernel install  kernel-ml-devel kernel-ml -y

4. 修改内核启动顺序，默认启动的顺序应该为1,升级以后内核是往前面插入，为0
grub2-set-default 0

5. 重启系统
reboot

6. 查看 内核版本
uname -r
```