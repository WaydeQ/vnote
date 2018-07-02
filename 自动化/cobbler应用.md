# cobbler应用
### cobbler接管dhcp，tftp

```
sed -i 's/manage_dhcp: 0/manage_dhcp: 1/g' /etc/cobbler/settings
sed -i 's/manage_tftpd: 0/manage_tftpd: 1/g' /etc/cobbler/settings
```

确保`manage_dhcp`, `manage_tftpd`参数为`1`

### 不要重复安装系统

```
sed -i 's/pxe_just_once: 0/pxe_just_once: 1/g' /etc/cobbler/settings
```

### 创建系统任务

```
cobbler system add --name=install_221 --profile=centos7-x86_64 --ip-address=172.16.1.221 --mac-address=84:2b:2b:5c:e1:57 --interface=eth0 --netboot-enabled=1
```

### 配置生效：注意每次修改完cobbler配置的时候使用cobbler sync
```
cobbler sync
```

### 删除任务

```
cobbler system remove --name=install_221
```