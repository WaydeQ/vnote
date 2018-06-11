# zabbix中文乱码解决
```
yum install dos2unix -y

#!/bin/bash
mailTmp=/tmp/mailTmp
echo "$3" >$mailTmp
to=$1
subject=$2
dos2unix -k $mailTmp
mail -s "$subject" "$to" < $mailTmp
```