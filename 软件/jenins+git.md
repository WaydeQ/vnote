# jenins+git

安装部署持续集成工具jenkins：
2.1：安装部分：
2.1.1：执行安装：
下载jdk-8u111-linux-x64.rpm

# rpm -ivh jdk-8u111-linux-x64.rpm

# vim /etc/profile

export JAVA_HOME=/usr/java/jdk1.8.0_111 
export PATH=$JAVA_HOME/bin:$PATH
安装jenkins
方法①yum方式安装

# cd /etc/yum.repos.d/
# wget http://pkg.jenkins.io/redhat/jenkins.repo
# rpm --import http://pkg.jenkins.io/redhat/jenkins.io.key
# yum install -y jenkins
# systemctl start jenkins
方法②通过war包的方式安装下载地址：http://updates.jenkins-ci.org/download/war/2.60.1/jenkins.war

unzip apache-tomcat-8.0.37.zip
mv apache-tomcat-8.0.37 /usr/local/
cd /usr/local
ln -s /usr/local/apache-tomcat-8.0.37 /usr/local/tomcat
useradd jenkins
# 将jenkins.war放到/usr/local/tomcat/webapps目录下，解压
unzip -d jenkins jenkins.war
chown -R jenkins.jenkins /usr/local/apache-tomcat-8.0.37
bin/startup.sh


打开web界面：第一次打开要输入随机生成的密码：
http://192.168.3.199:8080/jenkins

查看密码

$ cat /home/jenkins/.jenkins/secrets/initialAdminPassword
8743f91ff1474a85a0abcd841fc74eb6

![j1](_v_images/_j1_1532072600_4986.png)

输入密码点下确认之后选择插件，选择默认安装插件即可，此过程需要一段时间，而且根据网络不同有些插件安装不成功，但是之后可以自己再安装即可

![j2](_v_images/_j2_1532072616_17498.png)


报错：
An error occurred
An error occurred during installation: No such plugin: cloudbees-folder

![j3](_v_images/_j3_1532072521_1609.png)

解决办法：
下载cloudbees-folder.hpi放在目录/usr/local/tomcat/webapps/jenkins/WEB-INF/detached-plugins/下，重启tomcat即可

设置用户名密码

![j4](_v_images/_j4_1532072562_5547.png)

3.1配置邮件通知，系统管理–系统设置

添加管理员邮箱,添加邮件

![j5](_v_images/_j5_1532072658_26687.png)

点击保存或测试邮件，系统会给目标邮箱发送一封测试邮件：

![j6](_v_images/_j6_1532072690_30899.png)

安装gitlab插件,系统管理–管理插件–可选插件

![j7](_v_images/_j7_1532072722_13278.png)

插件安装界面，会额外安装一些依赖关系的插件，jenkins基于ruby开发，所以会有ruby环境：

![j8](_v_images/_j8_1532072754_23035.png)

在gitlab管理界面将用户添加到一个项目，下一步要用此用户拉取项目代码

三：使用jenkins：
3.1：添加一个认证用户，拉取git代码的时候使用：

![j9](_v_images/_j9_1532072785_18741.png)

创建任务 --> 构建一个自由风格的软件项目

![j10](_v_images/_j10_1532072813_23939.png)

![j11](_v_images/_j11_1532072835_9882.png)

项目描述信息：

![j12](_v_images/_j12_1532072858_30081.png)

配置git项目地址，我们先进行其他配置，等会再继续配置git相关的选项

![j13](_v_images/_j13_1532072909_5560.png)

系统管理 --> Global Tool Configuration

![j14](_v_images/_j14_1532072931_11265.png)

![j15](_v_images/_j15_1532072947_27470.png)

![j16](_v_images/_j16_1532072972_12829.png)

![j17](_v_images/_j17_1532072997_28161.png)

配置deploy-key
如下配置,jenkins服务器上root用户生成密钥对

```
[root@node1 ~]# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
bf:1e:4d:b3:0f:fb:8b:71:cd:ef:d3:70:69:a4:46:ff root@node1.chinasoft.com
The key's randomart image is:
+--[ RSA 2048]----+
| |
| |
| |
| . . |
| S + + .|
| . o =.*.|
| o * oo=|
| o B .E|
| .o o.+o+|
+-----------------+
[root@node1 ~]# cat .ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC3vhfbA4NdkP8g1WVyYkw93f1PZ04nWoyFnoerC9b40jIY5px+tkugdy/RZ3/bp4hMC5yNgV1S25Tm8RpzBJMp7pbJz8dO+LCKqUnXq9Eh0QhsZE0xlQN+J4awy9YIGiD2nFg7k/ZzeAtQRzNryrrPnKaWpXtg7tMGGTWBjeyLAVuqCOMU7euY94G26UmVfjDLIJkcJqB+8dwvodW3wpmUER32qVPKZLnSYSOCfoNed+P4Eujs5PBCLuzFvGyiDhY2Pwrk/4S11jWUa7TJItfoPXzeGc/ujaJi/o24dt8VXeFa/Rm4wywYLNW3TRjXy1mPpdVlob3701MMQ0bf3qPv root@node1.chinasoft.com
```

gitlab上配置web-demo项目设置private deploy key

![j18](_v_images/_j18_1532073066_28177.png)

把key复制进去，同时title起一个简单明了的名字

![j19](_v_images/_j19_1532073085_25757.png)

在jenkins服务器192.168.3.199上测试下是否能够正常获取代码

```
[root@node1 tmp]# git clone git@192.168.3.198:web/web-demo.git
Cloning into 'web-demo'...
The authenticity of host '192.168.3.198 (192.168.3.198)' can't be established.
ECDSA key fingerprint is 9d:60:c3:8e:49:72:e8:9c:c0:d6:c8:d2:f1:b6:3a:74.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.3.198' (ECDSA) to the list of known hosts.
remote: Counting objects: 79, done.
remote: Compressing objects: 100% (79/79), done.
remote: Total 79 (delta 17), reused 0 (delta 0)
Receiving objects: 100% (79/79), 1.18 MiB | 0 bytes/s, done.
Resolving deltas: 100% (17/17), done.
```

- jenkins拉取代码没问题，继续配置jenkins的认证，这里应该输入私钥

![j20](_v_images/_j20_1532073128_9969.png)

- 复制jenkins拉取git代码服务器root的私钥

```
[root@node1 ~]# cat .ssh/id_rsa
-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAt74X2wODXZD/INVlcmJMPd39T2dOJ1qMhZ6HqwvW+NIyGOac
frZLoHcv0Wd/26eITAucjYFdUtuU5vEacwSTKe6Wyc/HTviwiqlJ16vRIdEIbGRN
MZUDfieGsMvWCBog9pxYO5P2c3gLUEcza8q6z5ymlqV7YO7TBhk1gY3siwFbqgjj
FO3rmPeBtulJlX4wyyCZHCagfvHcL6HVt8KZlBEd9qlTymS50mEjgn6DXnfj+BLo
7OTwQi7sxbxsog4WNj8K5P+EtdY1lGu0ySLX6D183hnP7o2iYv6NuHbfFV3hWv0Z
uMMsGCzVt00Y18tZj6XVZaG9+9NTDENG396j7wIDAQABAoIBAEHMfCR9HJTsMMDk
SmDs3JqnHWhK+UzUe0/6VmEla0VNmI0cQFyMEYcUR3Z41uulEgURf22ZLv9WDPuq
yar4r26rtynsE1avbiEpwHzQVaMDhT2zqYUg9NA/fVdgl3PtT3KgyGQFd5MgIQUN
ileGkOF6GpoGBqnOvJkJfAS5+0RHi8EZ8RcufNExgan5QF42dtKNWOEdgZIp0+WY
jmI73YEpIadQzKxjL6PCOUmFAjfuNllw3It5QLePvYYlQeWKkl8QdROOADzOxgoj
6zCdzGo6ZqP4vK8gGioT7UybH1WJoN8of1ZBenKyT4+TagQYwqV0LXIpVBkHKgdD
5bxZIPkCgYEA4giWHweLYxmkZIXDx5VrOjIPmzTFqSfDMI9rDFQt57AbIr0hM4BW
6qmViWwuc9t28cLSXv+0FtycIfs4co8RvMinIMLykWddX8x3sniCr2+Pgn/G+RgQ
FK36wN0dVZwwr/6oPeIDCJlr4AqhDAJLJkcbRjc5i8kR9OPHIvkPJjsCgYEA0Bou
cmeOGYtrCW6t88xnmR26Sk8ybNWUdpju9SpEeCdz4jJnZC29AJ3qg8LilG0sDS/y
0cNT/iMP8x6FPeFqJnhI/l4C1H5WEqult/thMl1bZ+RFEO92EUcKEXZep0yu5Txr
6bHZzCnp9cYe56z9qsGk064LR2N9TR16xAzeud0CgYEArx0B2NZKeaNDBhHPxI8Q
/IwOJSs0O0Gv6a4iu/F3gviffUFTOIgkTjbFwCqMrnuHYfOSccDeb5vIZlcvuyj2
D/DP8gS2UknnQDGzRhQxAuOGJg8CQrcOtka3brWfZVTVL3Q2OYMg4Iej2HXDoP8h
42zRR4u/THmhLL1O6NxVjUMCgYBslKyVbuja9T61HjXPVmDbqIIyEZ6a4NnlA6jy
7Mnq85LofCosQB00vDDH7SCyF0B75P/KaSNjH8JLtSoKfpoXVn2nkz/gxcZ9WWhP
Ve+CCuoNjDJpQriVDT74m42Jt14uJ9eaiPy+JUR4w5YOhdmZhg5NkKs75XJUkUQk
FR8FVQKBgQDOWXJYF3VEYdCD1qIy/CsY3s4ARD9kery+5aOCu6TNd6auYEfqxsev
ie1jD+cvAgVR1aZ5aP3C8jEYhZa4xgn1+hxfRLWs79uMYnXXNGJ+FxKftDyhuV+m
efEQrUf8jh4MofW9Ee6Z7YjitIRY7SQ+kZl2Xms+QZlJotBzTG2DSA==
-----END RSA PRIVATE KEY-----
```

![j21](_v_images/_j21_1532073173_3961.png)

- 添加描述，点击OK   继续编辑配置

![j22](_v_images/_j22_1532073215_8968.png)

- 这样就不报错了，下面还可以指定从哪个分支拉取，默认从master拉取，有些公司使用release分支

![k23](_v_images/_k23_1532073253_11735.png)

- 源码浏览器，去gitlab上找到http的url：http://192.168.3.198/web/web-demo.git

- 找出gitlab的版本，输入url和版本，版本只支持2位，拉到最下面保存
```
[root@web01 ~]# rpm -qa|grep gitlab
gitlab-ce-8.10.5-ce.0.el7.x86_64
```

- 点击立即构建，查看控制台，可以看到构建的详细输出

![j24](_v_images/_j24_1532073319_30245.png)


![j25](_v_images/_j25_1532073341_30588.png)
