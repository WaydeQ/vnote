# ubuntu16.04安装docker

#### 1.1、移除已经安装过的docker（如果曾经安装过的话）

```
apt-get remove docker docker-engine docker.io
```

##### 1.2、更新包索引

```
apt-get update
```

#### 1.3、安装前置环境

```
apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        software-properties-common
```

#### 1.4、添加Docker的官方GPG key

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

#### 1.5、验证该key是否安装成功，或者使用sudo apt-key fingerprint查看所有的key

```
apt-key fingerprint
```

#### 1.6、amd64位机器上使用如下命令来建立稳定的库，可以使用echo $(lsb_release -cs)查看本机ubuntu的版本号。

```
add-apt-repository \
     "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) \
     stable"
```

##### 本质上是对/etc/apt/source.list进行修改在最后加入我们编辑的内容以及系统自动生成的描述内容。

#### 1.7、再次更新包索引

```
apt-get update
```

### 二、安装docker

#### 2.1、选择自己想要安装的版本

```
apt-cache madison docker-ce
```

#### 2.2、安装自己想要的版本

```
apt-get install docker-ce=[版本号]
```

##### 或者直接安装最新的版本:

```
apt-get install docker-ce
```

#### 2.3、检查自己安装的版本，并且运行hello-word实例

```
docker version
docker info
docker --version
```

#####  运行实例：

```
docker run hello-world
```

### 三、更换国内源，配置镜像加速

#### 3.1、 注册阿里云，填充相应区域：

```
echo "DOCKER_OPTS=\"--registry-mirror=https://自己的域名.mirror.aliyuncs.com\"" | sudo tee -a /etc/default/docker
    本质是修改/etc/default/docker，使用tee -a命令在文本之后追加我们编辑的内容，实现加速的目的。
```

#### 3.2、重启docker

```
service docker restart
```







