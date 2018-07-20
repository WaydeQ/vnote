# win10安装配置oracle客户端

### 下载Oracle 11g客户端 570M
```
http://www.oracle.com/technetwork/database/enterprise-edition/downloads/112010-win64soft-094461.html
```

#### 安装 出现了:[INS-13001]环境不满足最低要求。安装包中添加以下内容
```
E:\360Downloads\client\stage\cvu\cvu_prereq.xml

<OPERATING_SYSTEM RELEASE="6.2">
           <VERSION VALUE="3"/>
           <ARCHITECTURE VALUE="32-bit"/>
           <NAME VALUE="Windows 10"/>
           <ENV_VAR_LIST>
               <ENV_VAR NAME="PATH" MAX_LENGTH="1023" />
           </ENV_VAR_LIST>
</OPERATING_SYSTEM>

```

#### 重新安装
```
选择“管理员”模式--》选中文和英文 --》选择安装基目录--》完成

tnsnames.ora文件复制到安装目录以下位置
D:\oracle11g\product\11.2.0\client_1\network\admin
```

#### 安装 PLSQL Developer 12 (64 bit)

##### 完成！