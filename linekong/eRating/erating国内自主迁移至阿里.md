# erating国内自主迁移至阿里
### ali
```
uname=amc
cd /data
useradd $uname -d /data/$uname
cd $uname
mkdir eRating
cd eRating
rsync -avz --exclude={"8231","8232","update"} root@172.16.1.223:/home/rating/erating_amc/eRating/ ./ 

#
rsync -avz root@172.16.1.223:/home/rating/erating_amc/eRating/8231/amc/game.conf ./

./erating_logic -m -p 8041 -s amc
cd 8041
./erating_logic add_auth union

```

### 国内集群
```
vimdiff 8231/amc/game.conf 8232/amc/game.conf
vimdiff 8231/amc/app.conf 8232/amc/app.conf

find . -name 'app.conf' |xargs grep auth_name | awk '{print $2}'| sort|uniq
```

