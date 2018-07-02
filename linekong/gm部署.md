# gm部署

##  jboss_gm

- 创建jboss_gm用户及环境

```
useradd -d /data/jboss_gm jboss_gm
```

- 拷贝程序包

```
rsync -azvH /usr/local/src/jboss/* .
```

- 解压软件包

```
tar -zxf jboss_gm.tar.gz
mv jboss_gm ../
```

- 配置gm 中间件配置

```
cd  /data/jboss_gm/jboss-4.0.2/server/default/deploy/
// vim gmApplicationDs.xml
<bean id="dataSource_198" class="org.springframework.jndi.JndiObjectFactoryBean">       // dataSource_根据gameid
<property name="jndiName" value="java:gmweb2_linekong_198"/>

// mysql-ds.xml
<datasources>
  <local-tx-datasource>
    <jndi-name>gmweb2_gm</jndi-name>                                                     // 修改数据库
    <connection-url>jdbc:mysql://10.148.0.2:3306/egamemaster</connection-url>
    <driver-class>com.mysql.jdbc.Driver</driver-class>
    <user-name>egamemaster</user-name>
    <password>lk8Lsntkbq</password>
    <exception-sorter-class-name>org.jboss.resource.adapter.jdbc.vendor.MySQLExceptionSorter</exception-sorter-class-name>
    <metadata>
       <type-mapping>mySQL</type-mapping>
    </metadata>
  </local-tx-datasource>

  <local-tx-datasource>
    <jndi-name>gmweb2_linekong_198</jndi-name>
    <connection-url>jdbc:mysql://10.148.0.2:3306/erating_lmsy</connection-url>            // 修改数据库
    <driver-class>com.mysql.jdbc.Driver</driver-class>
        <user-name>erating</user-name>
        <password>lk3sgmCyt</password>
    <exception-sorter-class-name>org.jboss.resource.adapter.jdbc.vendor.MySQLExceptionSorter</exception-sorter-class-name>
    <metadata>
       <type-mapping>mySQL</type-mapping>
    </metadata>
  </local-tx-datasource>
</datasources>

linekong-config.xml 配置文件的这个参数与数据库中的sys_game表中的OWNER_TYPE 字段需要一致
-----------------------------------------------------
<linekong>
<project name="egmweb2">
        <property name="activityCodeFilePath">/data/jboss_gm/jboss-4.0.2/activitycode</property>
        <property name="pwdPath">/data/jboss_gm/jboss-4.0.2/server/default/conf/gm/</property>
        <property name="gameKey">198=linekonghy</property>
        <property name="legalIP">79.110.87.85</property>
        <property name="ownerType">1</property>
        <property name="iosPushPath960">/home/pustking.p12</property>
        <property name="iosPushPwd960">lineking</property>
        <property name="chatViewerUrl">http://35.185.191.193:8089/eChatMonitor/</property>
        <property name="chatViewerKey">linekongline</property>
        <property name="phpkey">linekongkong</property>
        <property name="unChargeUrl198">http://35.185.191.193:8082/union_mid/unCharging.do</property>
        <property name="unChargeKey198">d99aacfe513393a48b0b14a11828d554</property>
        <property name="unChargeUnionCode198">lmsy_198</property>
        <property name="unChargeUrl199">http://35.185.191.193:8082/union_mid/unCharging.do</property>
        <property name="unChargeKey199">67ec026cc75e3db9dc8180e1bb1fa036</property>
        <property name="unChargeUnionCode199">lmsy_199</property>
        <property name="unChargeUrl200">http://35.185.191.193:8082/union_mid/unCharging.do</property>
        <property name="unChargeKey200">08b14732254d1e2187062a33dc828de5</property>
        <property name="unChargeUnionCode200">lmsy_200</property>
</project>

<project name="echatmonitor">
          <property name="DBName">echatmonitor.</property>
          <property name="key">linekongline</property>
          <property name="gmkey">linekongkong</property>
          <property name="url">http://35.185.191.193:8089/eCharging_mid/xmlRpcServerServlet</property>
          <!--<property name="zz_xmlrpcaddress">http://172.20.150.41:8888/etoolkitsweb/xmlRpcServer</property> -->
          <property name="zz_xmlrpcaddress">http://35.185.191.193:8089/etoolkitsweb/xmlRpcServer</property>
          <property name="ly_xmlrpcaddress">http://35.185.191.193:8089/etoolkitsweb/xmlRpcServer</property>
          <property name="gameType">198,199,200</property>
</project>
</linekong>
```

- 修改gm C++程序配置(查询游戏数据)

```
.bashrc  //追加如下内容
export NLS_LANG=American_America.utf8
export NLS_DATE_FORMAT="YYYY-MM-DD HH24:MI:SS"
export LD_LIBRARY_PATH=$HOME/gmserver/lib/
unset ORACLE_HOME
export TNS_ADMIN=$PWD 

source .bashrc

// 路径
cd /data/jboss_gm/gmserver/gmserver

// 变更配置

// erating_mysql_db.ini
host_198=10.148.0.2                                          // 数据库IP
port_198=3306
user_198=erating                                             // 之前创建的数据库以及授权的用户密码
pwd_198=lk3sgmCyt                                            // 之前创建的数据库以及授权的用户密码
db_198=erating_lmsy                                          // 之前创建的数据库以及授权的用户密码

// gmserver.ini
#gameid
game_id=198,199,200                                          // 修改gameid
host_name=10.148.0.2                                         // 数据库IP
db_port=3306
service_name=egamemaster                                     // 之前授权的，不用变动。
user_name=egamemaster                                        // 之前授权的，不用变动。
password=00360bfe3373768d3d78438268c01d                      // 之前授权的，不用变动。
#Post=0 or Get=1
comm_with_worker_type=0 # 注意方式，0是post,1是Get ,如果需要Get方式的话 会提出需求。
```
- 启动GMSvr2.50

```
./GMSvr2.50 `pwd`
```

- 创建 egamemaster 库

```
// 备份线上的数据库(如提示关闭gtid，语句添加：--set-gtid-purged=OFF)
mysqldump -uroot -p  --opt -q -R -h10.66.167.137 -P3306 --single-transaction egamemaster > egamemaster_20170524.sql

// 导入线上的gm库数据
create database egamemaster;
use egamemaster;
source egamemaster_20170810.sql
show tables; 看是否有表  
select * from sys_game;
update sys_game set GAME_ID=198 where GAME_ID=2225;                       // 修改一个不用的gameid为新项目的
update sys_game set game_name='lmsy';					                            // 修改游戏名为新项目的
delete from sys_game where GAME_ID=2124;				                          // 删除不用的gameid
select * from sys_operators where passport_name='linekong';
select * from sys_resources limit 1;
select * from  sys_game_resources  limit 1;
select * from sys_operators where passport_name='egmweb2';
select * from sys_operators where passport_name='linekong';
update sys_operators set game_id  = -1 where passport_name = 'egmweb2';   // 修改egmweb2账号权限为超级用户
```

- 启动程序

```
cd /data/jboss_gm/jboss-4.0.2/bin
./run.sh &
```
