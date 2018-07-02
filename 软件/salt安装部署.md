# salt安装部署

### yum安装
```
yum install -y https://repo.saltstack.com/yum/redhat/salt-repo-latest-2.el7.noarch.rpm
yum install -y salt-master salt-minion
```
#### 配置
```


```


#### 启动
```
systemctl enable salt-master.service
systemctl start salt-master.service
```

#### 使用
```
salt -E 'ali-bj-e-erating*' state.sls cn.erating.base

```