# eRating相关信息查看

#### 查看erating进程
```
./erating_logic st
```
#### jboss_charge 充值中间件端口
```
grep port /data/jboss_charge/jboss-4.0.2/server/default/deploy/jbossweb-tomcat55.sar/server.xml
```
#### unionCode && chargekey ：
```
cat /data/jboss_charge/jboss-4.0.2/server/default/deploy/linekong-config.xml
<project name="lmsy_2295">
<property name="key">aa7694c45a42db03c6d933b3bd26b703</property>
```
#### platform 端口
```
netstat -nltp |grep platform
或：
grep port /home/erating/platform_svr_mysql/platform_svr.conf
```
#### GMSvr 端口
```
netstat -nltp |grep GMSvr2.50
或：
grep web_port /data/jboss_gm/gmserver/gmserver/gmserver.ini
```
#### gm端口
```
grep port= /data/jboss_gm/jboss-4.0.2/server/default/deploy/jbossweb-tomcat55.sar/server.xml
     <Connector port="8099" address="${jboss.bind.address}"

```
#### jboss_charge 端口：
```
grep "Connector port" /data/jboss_charge/jboss-4.0.2/server/default/deploy/jbossweb-tomcat55.sar/server.xml
      <Connector port="8082" address="${jboss.bind.address}"
#netstat 确认
netstat -nltp |grep 8082

```
#### jboss_passport 端口
```
grep "Connector port" /data/jboss_passport/jboss-4.0.2/server/default/deploy/jbossweb-tomcat55.sar/server.xml
netstat -nltp |grep
```

### 最后发出erating信息
（以下信息按照实际配置修改）
```
1. gm 登陆地址：
http://103.53.171.213:8080/etoolkitsweb

2. gm tool 网关url配置：
103.53.171.213:16625

3. 充值中间件信息
ip：103.53.171.213
端口：8082

gameid:2295
unionCode：lmsy_2295
key : aa7694c45a42db03c6d933b3bd26b703

4. erating信息
ip：103.53.171.213
port：5001
platform: 5008

5.gateway信息
导出erating数据库sys_gateway表数据
```

