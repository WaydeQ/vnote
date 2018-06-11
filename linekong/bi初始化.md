# bi初始化

### Mysql_ebi.sql

1.修改初始化脚本Mysql_ebi.sql参数：
```
替换	gameid，游戏名称
例：
234为老gameid 替换为新gameid 236
```

2.修改ebi数据库名称erating数据库对应的名称
```
查找ebi.erating关键字，替换成对应的数据库名
例：
ebi_dlzg
erating_dlzg
替换成
ebi_bc
erating_bc
```

3.初始化汇率表sys_exchange_rate，该汇率一定是当时最新的汇率（百度查）
```
insert into sys_exchange_rate values(234, 'USD',6.3598,NULL  ,1 ); 
币种USD 汇率6.3598
```

4.初始化sys_charge_rate 充值比率和消耗比率（需要跟运营协调好）
```
insert into sys_charge_rate values(234,1,100,0,'USD');
100 是通过99除0.99获取
```

5.初始化充值档位表sys_fixed_charge_rate，填充数据档位数据
```
(99,0.99,234,55),
(499,4.99,234,55),
(1499,14.99,234,55),
(1999,19.99,234,55),
(2999,29.99,234,55),
(4999,49.99,234,55),
(9999,99.99,234,55),
```

6.初始化等级表sys_level_interval，填充等级数据
```
insert into `sys_level_interval` 
values (0, 1, 234);

insert into `sys_level_interval` 
values (2, 4, 234);
```


### ebi.sql
```
双渠道 后面添加ios或android
series_name 系列名要 统一

insert into    EREPORT2.sys_game_config_channel (game_id,game_name,QUERY_TYPE,query_name,EXCHANGE_RATE,EXCHANGE_SCALE,EXCHANGE_DESC,GAME_TYPE,CHANNEL_TYPE,GAME_STATE,CHANNEL_NAME,QUERY_STATE)
values (234,'地牢之光北美自主ios','49','地牢之光系列','6.3598','1','1美元=6.3598人民币元','3','3','1','北美自主ios','1');
insert into ereport2.SYS_MOBILE_GAME_SERIES_CONFIG(series_id,series_name,icon_url,Series_Alias) values(49,'地牢之光','1','dlzg');
```

#### 替换
```
替换gameid，game_id = 206  -- 206 为国内对应自主游戏的game_id（查询更换）
天天爱萌宠腾讯 中文名称替换

充值渠道ID：
55=iOS,Google=56
```

### ereport2.sql
```
替换 erating_** 和自主库名
select * from erating_ttamc.vw_sys_product@xiyouji_new.lk where game_id = 206; 
```
- query_type 值查询
```
sys_game_config  query_type  37  查自主库 （已存在使用老的）

登录用户密码：
EREPORT2/vdQodSivaaL4b1G
查询命令：
select * from sys_game_config where game_name like '%TF%';
select distinct query_type from sys_game_config where query_type <1000;
select distinct t.query_type  from sys_game_config t where t.query_type <1000 order by t.query_type desc;
```

- 修改名称
```
values (2377, '天天爱萌宠腾讯', 3, 37 , '天天爱萌宠系列', 1, 1, '',1);

sys_game_config_channel 查自主库
values (2377,'天天爱萌宠腾讯','37','天天爱萌宠系列','1','1','海外默认1','3','2','1','腾讯','1');
```

#####  多gameid 只加一条
```
insert into ereport2.SYS_MOBILE_GAME_SERIES_CONFIG(series_id,series_name,icon_url,Series_Alias) values(49,'地牢之光','1','dlzg');
```

- ereport.sql 游戏类型修改
```
 --series_name 游戏系列名称
 --game_type   游戏类型 1-自研手游 2-代理手游 3-端游 4-页游 5-h5
 --union_type  运营类型 1-国内自营  2-安卓联运国内 3-ios联运国内 4-海外分成 5 -韩国自营 6-韩国安卓联运 7-韩国ios联运 8-台湾自营 9-台湾安卓联运 10 -台湾ios联运
sys_query_union_config
 insert into sys_query_union_config(game_id,series_name,game_type,union_type) values(2377,'天天爱萌宠',2,2);
```

### ereport_esuite_add.sql

### 新游戏 不执行   老bi 脚本
```
修改gameid

V_VARS T_VARRAY:=T_VARRAY(12,100,739);

确认
copyReport(945,,1134);
```

### 最后bi抽取配置
```
172.16.1.172
su - jboss
/home/jboss/work/RemoteGameETL/config
修改applicationContext.xml
每个gameid都要加一段配置

测试mysql是否能登录
mysql -uebi -p -h49.51.36.13 -P1626

/home/jboss/work/RemoteGameETL/shell2
验证配置
java -jar $REMOTE_ETLHOME/newRemoteGameETL.jar -t statVipUseraTask -g 2377
没有数据库连接报错就ok


添加新gameid
sed -i 's/2372377/237,2377/g' *.sh

```