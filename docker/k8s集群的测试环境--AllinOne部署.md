# k8s集群的测试环境--AllinOne部署
### 快速指南
- 以下为基于Ubuntu 16.04/CentOS 7.4 快速体验k8s集群的测试、开发环境--AllinOne部署，觉得比官方的minikube方便、简单很多。

#### 1.基础系统配置
```
推荐内存2G/硬盘20G以上
最小化安装Ubuntu 16.04 server或者CentOS 7 Minimal
配置基础网络、更新源、SSH登陆等
```
#### 2.安装依赖工具

#####  Ubuntu 16.04 请执行以下脚本:

- 文档中脚本默认均以root用户执行
```
apt-get update && apt-get upgrade -y && apt-get dist-upgrade -y
```
- 删除不要的默认安装
```
apt-get purge ufw lxd lxd-client lxcfs lxc-common
```
- 安装依赖工具
```
apt-get install python2.7 git python-pip
```
- Ubuntu16.04可能需要配置以下软连接
```
ln -s /usr/bin/python2.7 /usr/bin/python
```

##### CentOS 7 请执行以下脚本：

- 文档中脚本默认均以root用户执行
- 安装 epel 源并更新
```
yum install epel-release -y
yum update
```
- 删除不要的默认安装
```
yum erase firewalld firewalld-filesystem python-firewall -y
```
- 安装依赖工具
```
yum install git python python-pip -y
```

#### 3.ansible安装及准备

- 安装ansible (国内如果安装太慢可以直接用pip阿里云加速)
```
#pip install pip --upgrade
#pip install ansible
pip install pip --upgrade -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
pip install --no-cache-dir ansible -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
```
- 配置ansible ssh密钥登陆
```
ssh-keygen -t rsa -b 2048 回车 回车 回车
ssh-copy-id $IP #$IP为本虚机地址，按照提示输入yes 和root密码
```

#### 4.安装kubernetes集群
```
git clone https://github.com/gjmzj/kubeasz.git
mv kubeasz /etc/ansible
```

- 下载已打包好的binaries，并且解压缩到/etc/ansible/bin目录
- 国内请从分享的百度云链接下载 https://pan.baidu.com/s/1c4RFaA
- 如果你有合适网络环境也可以按照/down/download.sh自行从官网下载各种tar包到 ./down目录，并执行download.sh

```
tar zxvf k8s.191.tar.gz
mv bin/* /etc/ansible/bin
```
- 配置ansible的hosts文件
```
cd /etc/ansible
cp example/hosts.allinone.example hosts
```

- 然后根据实际情况修改此hosts文件，所有节点都是本虚机IP

#### 采用一步安装或者分步安装
- 一步安装
```
ansible-playbook 90.setup.yml
报错：
ERROR! Unexpected Exception, this is probably a bug: (cryptography 1.3.1 (/usr/lib64/python2.7/site-packages), Requirement.parse('cryptography>=1.5'))

pip install --upgrade cryptography
```