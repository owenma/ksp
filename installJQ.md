# 创建集群

    官方提供了一个工具：redis-trib.rb  （/usr/local/redis/src/redis-trib.rb） 
要运行这个我们首先要安装一下 Ruby：

   
    yum -y install ruby ruby-devel rubygems rpm-build

  再用 gem 这个命令来安装 redis接口：

     gem install redis    //等一会儿
 
   确认所有的节点都启动后，接下来使用参数create 创建：
 
```
/usr/local/redis/src/redis-trib.rb  create  --replicas  1  172.20.13.229:7000  172.20.13.229:7001  172.20.13.230:7003  172.20.13.230:7004  172.20.13.230:7005  172.20.13.229:7002
```
    
 
解释下， --replicas  1  表示 自动为每一个master节点分配一个slave节点    上面有6个节点，程序会按照一定规则生成 3个master（主）3个slave(从)

    前面已经提醒过的 防火墙一定要开放监听的端口，否则会创建失败。
    
    运行中，提示Can I set the above configuration? (type 'yes' to accept): yes    //输入yes
    
    创建过程中的显示：
   ```
   [root@mobancentos70 redis]# /usr/local/redis/src/redis-trib.rb  create  --replicas  1  172.20.13.229:7000  172.20.13.229:7001  172.20.13.230:7003  172.20.13.230:7004  172.20.13.230:7005  172.20.13.229:7002
>>> Creating cluster
>>> Performing hash slots allocation on 6 nodes...
Using 3 masters:
172.20.13.229:7000
172.20.13.230:7003
172.20.13.229:7001
Adding replica 172.20.13.230:7004 to 172.20.13.229:7000
Adding replica 172.20.13.229:7002 to 172.20.13.230:7003
Adding replica 172.20.13.230:7005 to 172.20.13.229:7001
M: 239e1e08b782005f7c9f73f64446159124e0f73a 172.20.13.229:7000
   slots:0-5460 (5461 slots) master
M: bc55587ed472eca63b901576abadc17b6b98b3dd 172.20.13.229:7001
   slots:10923-16383 (5461 slots) master
M: 5e2c320238a41ee28f82d835d3fc96d6a0e15ce9 172.20.13.230:7003
   slots:5461-10922 (5462 slots) master
S: a0cef1fa6ac79cad9a69791d3f4dea9bd8ea0d92 172.20.13.230:7004
   replicates 239e1e08b782005f7c9f73f64446159124e0f73a
S: c8e54ee24b91b71ae698af0f961eeac99139e502 172.20.13.230:7005
   replicates bc55587ed472eca63b901576abadc17b6b98b3dd
S: 7414943a4513eee521fc0b7e37985f7d07fb5a4e 172.20.13.229:7002
   replicates 5e2c320238a41ee28f82d835d3fc96d6a0e15ce9
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join...
>>> Performing Cluster Check (using node 172.20.13.229:7000)
M: 239e1e08b782005f7c9f73f64446159124e0f73a 172.20.13.229:7000
   slots:0-5460 (5461 slots) master
M: bc55587ed472eca63b901576abadc17b6b98b3dd 172.20.13.229:7001
   slots:10923-16383 (5461 slots) master
M: 5e2c320238a41ee28f82d835d3fc96d6a0e15ce9 172.20.13.230:7003
   slots:5461-10922 (5462 slots) master
M: a0cef1fa6ac79cad9a69791d3f4dea9bd8ea0d92 172.20.13.230:7004
   slots: (0 slots) master
   replicates 239e1e08b782005f7c9f73f64446159124e0f73a
M: c8e54ee24b91b71ae698af0f961eeac99139e502 172.20.13.230:7005
   slots: (0 slots) master
   replicates bc55587ed472eca63b901576abadc17b6b98b3dd
M: 7414943a4513eee521fc0b7e37985f7d07fb5a4e 172.20.13.229:7002
   slots: (0 slots) master
   replicates 5e2c320238a41ee28f82d835d3fc96d6a0e15ce9
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

检查集群状态：

/usr/local/redis/src/redis-trib.rb check 172.20.13.229:7000

```...
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.```
