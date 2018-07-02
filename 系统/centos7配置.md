# centos7配置
#### 关闭防火墙
```
systemctl stop firewalld.service
```
#### 禁止firewall开机启动
```
systemctl disable firewalld.service
```
#### 查看默认防火墙状态
```
firewall-cmd --state
systemctl list-unit-files|grep firewalld.service
```
#### centos7.0 没有netstat 和 ifconfig命令问题
```
yum install net-tools -y
```
#### 使用 systemctl list-unit-files 可以查看启动项
```
systemctl list-unit-files | grep enable
```
#### 修改主机名
```
hostnamectl set-hostname ***
```
#### 修改时区
```
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

timedatectl set-timezone Asia/Shanghai
```