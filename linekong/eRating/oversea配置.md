# oversea配置
# oversea联运充值安装配置

### 配置jdk

```
mkdir /usr/java
cd /usr/java
tar zxf jdk-7u80-linux-x64.tar.gz
```
- .bashrc 添加以下内容

```
export JAVA_HOME=/usr/java/jdk1.7.0_80
export JRE_HOME=$JAVA_HOME/jre 
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH 
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH

source .bashrc
```
### 初始化数据库
- 上传redmine上ftp的sql文件
- 例：DB操作 FTP:/oVersea/20170929/DB
- 执行顺序：oversea_charge.sql >> oversea_charge_config.sql >> overseaChargeConfig上线初始化数据.sql >> 脚本1.sql
- 登录mysql 安顺序 source（注：如有报错，查找报错表，重新执行未添加的sql语句）

```
/usr/local/mysql/bin/mysql -uroot -p -h10.66.117.144 -P3306
```

- 添加mysql查询用户

```
grant SELECT on oversea_charge.* to 'panpan'@'10.105.157.56' identified by 'panpan';
grant SELECT on oversea_charge_config.* to 'panpan'@'10.105.157.56' identified by 'panpan';
flush privileges;
```

- 创建用户

```
useradd -d /data/union_charege union_charege
useradd -d /data/union_charege_config union_charege_config
```

- 上传tomcat

```
tar xf apache-tomcat-8.5.11.tgz -C /data/union_charege
tar xf apache-tomcat-8.5.11.tgz -C /data/union_charege_config
```

### 配置union_charege
- 配置环境变量

```
vim .bash_profile 
LK_OVERSEA_CHARGE=/data/union_charege/apache-tomcat-8.5.11/app_conf
export LK_OVERSEA_CHARGE
```

- 配置tomcat

```
cd /data/union_charege/apache-tomcat-8.5.11
mkdir app_conf app_log
cd app_conf
```
- rz 上传lk_oversea_charege配置文件目录下 log4j.properties spring-mybatis.xml
- 修改log存放位置

```
vim log4j.properties

log4j.appender.R.File=/data/union_charege/apache-tomcat-8.5.11/app_logs/server.log

//确认HH为大写
log4j.appender.stdout.layout.ConversionPattern=[%d{dd/MM/yy HH:mm:ss:sss z}] %t %5p %c{2}: %m%n
log4j.appender.R.layout.ConversionPattern=[%d{dd/MM/yy HH:mm:ss:sss z}] %t %5p %c{2}: %m%n
```

- 配置修改：mysqlIP,连接库,用户,密码

```
vim spring-mybatis.xml

 <bean id="charge" class="com.mchange.v2.c3p0.ComboPooledDataSource"  destroy-method="close">
                <property name="jdbcUrl" value="jdbc:mysql://10.66.117.144:3306/oversea_charge"/>
                <property name="user" value="oversea_charge" />
                <property name="password" value="9bO59bFewwYiEKR1" />

        <bean id="chargeConfig" class="com.mchange.v2.c3p0.ComboPooledDataSource"  destroy-method="close">
                <property name="jdbcUrl" value="jdbc:mysql://10.66.117.144:3306/oversea_charge_config"/>
                <property name="user" value="oversea_config" />
                <property name="password" value="9bO59bFewwYsdser" />

                <property name="jdbcUrl" value="jdbc:mysql://10.66.117.144:3306/erating_lmsy"/>
                <property name="user" value="erating" />
                <property name="password" value="erating" />

```

- 修改端口：8010 8413 默认用nio，修改HTTP连接端口8009防止端口冲突或注释掉

```
vim conf/server.xml
<Server port="-1" shutdown="SHUTDOWN">

    <!--
    <Connector port="8010" protocol="org.apache.coyote.http11.Http11AprProtocol"
              connectionTimeout="20000"
              redirectPort="8413" />
    -->
    <!-- A "Connector" using the shared thread pool-->
    <Connector executor="tomcatThreadPool"
              port="8010" protocol="HTTP/1.1"
              connectionTimeout="20000"
              redirectPort="8413" />


    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <Connector port="8019" protocol="AJP/1.3" redirectPort="8413" />
```

- 上传war包

```
cd webapps
rz lk-oversea-charge.war
```

- 启动服务(确认update_tomcat.sh程序路径正确)

```
cd /data/union_charege/
./update_tomcat.sh start
```

### 配置union_charege_config

- 配置环境变量

```
su - union_charege_config
vim .bash_profile 
OVERSEA_CHARGE=/data/union_charege_config/apache-tomcat-8.5.11/app_conf
export OVERSEA_CHARGE
```

- 配置tomcat

```
cd /data/union_charege_config/apache-tomcat-8.5.11
mkdir app_conf app_log
cd app_conf
```

- rz 上传overseaChargeConfig配置文件目录下 log4j.properties spring-mybatis.xml
- 修改log存放位置（确认HH为大写）

```
vim log4j.properties
log4j.appender.R.File=/data/union_charege_config/apache-tomcat-8.5.11/app_logs/server.log
```

- 配置修改ds1、ds2：mysqlIP，用户，密码

```
vim 
         <!-- 配置项目数据库连接池配置 -->
       <bean id="ds1" class="com.mchange.v2.c3p0.ComboPooledDataSource"  destroy-method="close">  
                <property name="driverClass" value="com.mysql.jdbc.Driver"/>  
                <property name="jdbcUrl" value="jdbc:mysql://10.66.117.144:3306/oversea_charge_config"/>  
                <property name="user" value="oversea_config" />  
                <property name="password" value="9bO59bFewwYsdser" />  

           <!--充值项目数据库连接池-->
       <bean id="ds2" class="com.mchange.v2.c3p0.ComboPooledDataSource"  destroy-method="close">  
                <property name="driverClass" value="com.mysql.jdbc.Driver"/>  
                <property name="jdbcUrl" value="jdbc:mysql://10.66.117.144:3306/oversea_charge"/>  
                <property name="user" value="oversea_charge" />  
                <property name="password" value="9bO59bFewwYiEKR1" />  
```

- 修改端口：8020 8423 默认用nio，修改HTTP连接端口8009防止端口冲突或注释掉

```
vim conf/server.xml
<Server port="-1" shutdown="SHUTDOWN">

    <!--
    <Connector port="8020" protocol="org.apache.coyote.http11.Http11AprProtocol"
              connectionTimeout="20000"
              redirectPort="8423" />
    -->
    <!-- A "Connector" using the shared thread pool-->
    <Connector executor="tomcatThreadPool"
              port="8020" protocol="HTTP/1.1"
              connectionTimeout="20000"
              redirectPort="8423" />

    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <Connector port="8029" protocol="AJP/1.3" redirectPort="8423" />
```

- 上传war包

```
cd webapps
rz overseaChargeConfig.war
```
- 启动服务(确认update_tomcat.sh程序路径正确)

```
cd /data/union_charege_config/
./update_tomcat.sh start
```

- 添加开机启动

```
vim /etc/rc.local

#union_charege
su - union_charege -c "cd /data/union_charege/ && ./update_tomcat.sh start"

#union_charege_config
su - union_charege_config -c "cd /data/union_charege_config/ && ./update_tomcat.sh start"
```

### 添加日志查看用户

```
useradd watcher
passwd watcher
linekongpfwork

chmod 705 /data/union_charege /data/union_charege_config
chmod 700 /data/union_charege_config/update_tomcat.sh /data/union_charege/update_tomcat.sh

cd /data/union_charege_config/apache-tomcat-8.5.11
chown union_charege_config.watcher app_logs logs -R
chmod g+s app_logs log

cd /data/union_charege/apache-tomcat-8.5.11
chown union_charege.watcher app_logs logs -R
chmod g+s app_logs logs
```