# git

### win10Git安装与配置
- 先去翻墙到：https://code.google.com/p/msysgit/downloads/list   下载git；
- 安装时候全部默认安装----->next;
- 打开开始界面的Git CMD 输入git或者 git --version 看是否安装成功：

![git1](_v_images/_git1_1528686273_31941.png)

- 设置昵称与email，在CMD中依次输入：

```
git config --global user.name "wuqi"
git config --global user.email "wuqi@linekong.com"
git config --global push.default simple
```

5、查看配置：
```
git config -l
```

- 克隆仓库到本地
```
git remote add origin git@github.com:WaydeQ/vnote.git^
git clone git@172.16.3.11:om/om-public.git
```

###
- 添加github地址
```
git  add .
git commit -am 'first commit'
git remote add origin git@github.com:runtuwuqi/vnote.git
git push -u  origin master
git commit -am 'add'
push
```

- 修改github地址
```
git remote set-url origin git@github.com:WaydeQ/vnote.git
git  add .
git commit -am 'add'
git push
```

- 强制覆盖本地文件
```
git fetch --all
git reset --hard origin/master
git pull
```