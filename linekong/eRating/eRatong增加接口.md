# eRatong增加接口
- 查找应用
```
grep  auth_svr_logic app.conf
```
- 添加接口
```
<cmd tag="CMD_USER_BIRTHDAY_QUERY">10003014</cmd>
```
- 重启服务
```
./erating_logic -ar cqzj2 auth_svr_logic
./erating_logic -ar cqzj2 auth_svr_logic_01
```