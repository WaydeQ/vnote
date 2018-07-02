# iptables
### 查看规则
```
iptables -L -n
```
### 封IP
```
iptables -I INPUT -s 58.210.4.242 -j DROP 
```
#   解封IP
```
iptables -D INPUT -s 58.210.4.242 -j DROP
```
### 允许IP访问（指定位置4）
```
iptables -I INPUT -s 103.69.194.121 -p tcp --dport 873 -j ACCEPT

iptables -I INPUT 4 -s 113.208.129.8 -j ACCEPT
```

### 开放多端口：
```
iptables -I INPUT 3 -s 45.249.247.229 -p tcp -m multiport --dports 2122,3306 -j ACCEPT
```
### 删除（指定位置4）：
```
iptables -D INPUT 4
```

###DNAT 网络地址转换 (添加 删除)
```
iptables -t nat -A PREROUTING -d 118.26.72.133 -i eth0 -p tcp -j DNAT --to-destination 10.0.0.121

iptables -t nat -D  PREROUTING -d 118.26.72.133 -i eth0 -p tcp -j DNAT --to-destination 10.0.18.114
```

### 其他
```
iptables -A INPUT -s 192.168.1.0/255.255.255.0 -j ACCEPT
iptables -I INPUT -s 192.168.1.248 -j DROP

iptables -I INPUT -p tcp -m tcp --dport 80 -j ACCEPT
iptables -I INPUT -p tcp -m tcp --dport 50718 -j ACCEPT
```
### 保存配置
```
iptables-save >/etc/sysconfig/iptables
```