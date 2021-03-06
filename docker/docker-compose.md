# docker-compose
### 安装docker compose
```
docker compose可以方便我们快捷高效地管理容器的启动、停止以及重启等操作，和批量管理容器，它类似于linux下的shell脚本，基于yaml语法，在该文件里我们可以描述应用的架构，比如用什么镜像、数据卷、网络模式、监听端口等信息。我们可以在一个compose文件中定义一个多容器的应用（比如jumpserver），然后通过该compose来启动这个应用。
```

#### 安装compose的方法如下，下载docker-compose然后放到/usr/local/bin/目录下，再更改一下权限即可：
```
[root@server ~]# curl -L https://github.com/docker/compose/releases/download/1.17.0-rc1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
[root@server ~]# chmod 755 !$  # 指定权限
chmod 755 /usr/local/bin/docker-compose
[root@server ~]# 
docker compose的安装就这么简单，安装完后，我们来查看一下版本信息：

[root@server ~]# docker-compose version
docker-compose version 1.17.0-rc1, build a0f95af
docker-py version: 2.5.1
CPython version: 2.7.13
OpenSSL version: OpenSSL 1.0.1t  3 May 2016
[root@server ~]# 
```

- 说明：Compose区分Version 1和Version 2（Compose 1.6.0+，Docker Engine 1.10.0+）。Version 2支持更多的指令。Version 1没有声明版本默认是"version 1"。Version 1将来会被弃用。

### docker compose示例
- 以上我们已经安装好了docker compose ，下面用一个简单的示例演示一下如何使用docker compose，编辑.yml文件，内容如下：
```
[root@server ~]# vim docker-compose.yml
version: "2"  # 使用Version 2
services:  # 包含需要操作的容器
  app1: # 容器的名称
    image: centos_nginx  # 指定基于哪个镜像
    ports:  # 指定映射的端口
      - "8080:80"
    networks:  # 指定使用哪个网络模式
      - "net1"
    volumes:  # 指定挂载的的目录
      - /data/:/data
  app2:
    image: test_centos
    networks:
      - "net2"
    volumes:
      - /data/:/data1
    entrypoint: tail -f /etc/passwd  # 这一句是为了让这个容器不会被关闭
networks:
  net1:
    driver: bridge
  net2:
    driver: bridge
```

##### 编辑完之后就可以启动了：
```
[root@server ~]# docker-compose up -d  # -d是丢到后台运行
Creating network "root_net2" with driver "bridge"
Creating network "root_net1" with driver "bridge"
Creating root_app2_1 ... 
Creating root_app1_1 ... 
Creating root_app2_1
Creating root_app1_1 ... done
[root@server ~]#
```

###### 然后docker ps看一下是否已启动成功：

- 所以要加上 entrypoint: tail -f /etc/passwd 的原因就在这，这个语句的唯一作用就是防止容器被关闭，这是docker的一个特性，说实话我也不知道为嘛要加这样的一句话，但是不加的话末尾启动的那个容器就会被关闭，不过不一定非要写 tail -f /etc/passwd ，也可以是其他可正常执行的命令。

##### docker-compose --help 可以查看该命令的文档，下面介绍几个常用的命令参数：

- 停止已有的容器：
```
[root@server ~]# docker-compose stop
Stopping root_app1_1 ... done
Stopping root_app2_1 ... done
[root@server ~]#
```

- 启动已有的容器：
```
[root@server ~]# docker-compose start
Starting app2 ... done
Starting app1 ... done
[root@server ~]#
```

- 查看容器的状态：
```
[root@server ~]# docker-compose ps
   Name                  Command                State     Ports
---------------------------------------------------------------
root_app1_1   /bin/sh -c /usr/local/ngin ...   Exit 137        
root_app2_1   tail -f /etc/passwd              Exit 137        
[root@server ~]#
```

- 删除容器：
```
[root@server ~]# docker-compose rm -f
Going to remove root_app1_1, root_app2_1
Removing root_app1_1 ... done
Removing root_app2_1 ... done
[root@server ~]# 
```

- 停止并删除运行中的容器：
```
[root@server ~]# docker-compose down
Stopping root_app1_1 ... done
Stopping root_app2_1 ... done
Removing root_app1_1 ... done
Removing root_app2_1 ... done
Removing network root_net2
Removing network root_net1
[root@server ~]# docker-compose ps
Name   Command   State   Ports
------------------------------
[root@server ~]#
```