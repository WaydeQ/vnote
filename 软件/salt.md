# salt


### 测试命令 test=true
```
salt -G 'role:cn.erating.cluster131' state.sls cn.erating.dhss.erating test=true
```


### 字符问题
```
Unable to manage file: 'utf8' codec can't decode byte 0xd5 in position 9: invalid continuation byte
```
- 删除中文乱码，删除服务器上文件，重新推送
