# graphtree
### graphtree介绍
-由于zabbix的图像显示一块不太友好，图像没法集中显示，而我们对这一功能的需求又比较高，所以就出现了
zatree,graphtree,这些插件。

#### graphtrees的功能：
```
一. 集中展示所有分组设备
二. 集中展示一个分组图像
三. 集中展示一个设备图像
四. 展示设备下的Application
五. 展示每个Application下的图像
六. 展示每个Application下的日志
七. 对原生无图的监控项进行绘图
```

#### graphtrees的安装方法
- 先下载graphtrees插件，可以在官网上下载，注意版本不要搞错了。
```
https://github.com/OneOaaS/graphtrees/raw/master/graphtree3.2.x.patch
1. 下载好了将graphtree3.2.x.patch拷贝到zabbix的网页路径下
2. 更新补丁
yum install -y patch
3. 运行补丁
patch -Np0 <graphtree${ZBXVERSION}.patch
完了改一下归属就好了
chown -R nginx:nginx oneoaas/
4.创建templates_c文件夹，并赋予所有权限
# cd oneoaas
# mkdir templates_c
# chmod 777 templates_c
重启下nginx就好了。
打开graphtrees
```

