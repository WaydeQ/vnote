# root启动erating解决办法
ipcs |grep 9991
0x99910000 1507339    root      666        135728    1                      
0x99910000 393222    root      660        1

### 删除共享内存
ipcrm -M 0x99910000

ipcs 

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch    status      
0x6c030019 0          zabbix    600        657056    5                      
0x99990000 917505    erating    666        135728    9                      
0x99990001 950274    erating    666        4268128    2                      
0x99990002 983043    erating    666        4268128    2                      
0x99990003 1015812    erating    666        4268128    2                      
0x99990004 1048581    erating    666        4268128    2                      
0x99990005 1081350    erating    666        4268128    2                      
0x99990006 1114119    erating    666        4268128    2                      
0x99990007 1146888    erating    666        4268128    2                      
0x99990008 1179657    erating    666        4268128    2                      
0x00000000 1507339    root      666        135728    1          dest        

------ Semaphore Arrays --------
key        semid      owner      perms      nsems    
0x00000000 0          root      600        1        
0x00000000 32769      root      600        1        
0x7a030019 65538      zabbix    600        13        
0x00000000 163843    nobody    600        1        
0x00000000 196612    nobody    600        1        
0x99990000 327685    erating    660        1        
0x99910000 393222    root      660        1        

### 删除信号量
ipcrm -S 0x99910000