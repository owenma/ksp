# 集群安装

1）172.20.13.229(server)

登录Server：

    cd /usr/local/

    mkdir redis_cluster  //创建集群目录

    mkdir 7000 7001 7002  //分别代表三个节点    其对应端口 7000 7001 7002

    创建7000节点为例，

    cd ./7000

    cp /usr/local/redis/redis.conf  ./    //拷贝到当前7000目录

    vi redis.conf    //编辑配置  主要修改一下几个参数

 

    daemonize    yes                          //redis后台运行
    pidfile  /var/run/redis_7000.pid    //pidfile文件对应7000
    port  7000                                  //端口7000
    cluster-enabled  yes                    //开启集群  把注释#去掉
    cluster-config-file  nodes.conf      //集群的配置  配置文件首次启动自动生成
    cluster-node-timeout  5000      //请求超时  设置5秒够了
    appendonly  yes                        //aof日志开启  有需要就开启，它会每次写操作都记录一条日志

    配置好了，就相应地把这个修改后的配置文件拷贝到 7001  7002目录，注意要修改监听端口port 7001 7002.

    接下来，启动服务，进入节点目录

    依次执行  redis-server  redis.conf

    可以看到生成了appendonly.aof  nodes.conf
    
    ps -ef | grep redis 查看是否启动成功
    ```[root@mobancentos70 ~]# ps -ef|grep redis
    root     20099     1  0 7月29 ?       00:09:04 redis-server 172.20.13.229:7002 [cluster]
    root     20106     1  0 7月29 ?       00:09:09 redis-server 172.20.13.229:7000 [cluster]
    root     20121     1  0 7月29 ?       00:09:10 redis-server 172.20.13.229:7001 [cluster]```
    
    netstat -tnlp | grep redis 可以看到redis监听端口
   ```
   [root@mobancentos70 ~]# netstat -tnlp | grep redis
    tcp        0      0 172.20.13.229:17000     0.0.0.0:*        LISTEN      20106/redis-server
    tcp        0      0 172.20.13.229:17001     0.0.0.0:*        LISTEN      20121/redis-server
    tcp        0      0 172.20.13.229:17002     0.0.0.0:*        LISTEN      20099/redis-server
    tcp        0      0 172.20.13.229:7000      0.0.0.0:*        LISTEN      20106/redis-server
    tcp        0      0 172.20.13.229:7001      0.0.0.0:*        LISTEN      20121/redis-server
    tcp        0      0 172.20.13.229:7002      0.0.0.0:*        LISTEN      20099/redis-server
    ```
    
    我们除了看到 配置文件中设置的端口700*    还有700*+10000  （1700*）, 前者是客户端访问的， 后者是集群内部节点之间访问的.
    
  设置iptables开放上面所有端口
 ``` 
  /sbin/iptables -I INPUT -p tcp --dport 7000 -j ACCEPT
  /sbin/iptables -I INPUT -p tcp --dport 7001 -j ACCEPT
  /sbin/iptables -I INPUT -p tcp --dport 7002 -j ACCEPT
  /sbin/iptables -I INPUT -p tcp --dport 17000 -j ACCEPT
  /sbin/iptables -I INPUT -p tcp --dport 17001 -j ACCEPT
  /sbin/iptables -I INPUT -p tcp --dport 17002 -j ACCEPT
  ```
    
2）172.20.13.230(server)
   同上, 设置端口  7003  7004  7005