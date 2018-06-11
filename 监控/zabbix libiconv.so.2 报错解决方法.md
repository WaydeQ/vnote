# zabbix libiconv.so.2 报错解决方法
/usr/local/zabbix/sbin/zabbix_agentd: error while loading shared libraries: libiconv.so.2:
cannot open shared object file: No such file or directory
#####  解决方法:
- 在/etc/ld.so.conf中加一行/usr/local/lib，运行ldconfig,这样再启动就没有问题了
```
echo "/usr/local/lib" >>/etc/ld.so.conf
ldconfig
```