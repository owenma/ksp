# 集群测试


测试 Redis 集群比较简单的办法就是使用 redis-rb-cluster 或者 redis-cli ， 接下来我们将使用 redis-cli 为例来进行演示：


    [root@mobancentos70 redis_cluster]# redis-cli -c -h 172.20.13.229 -p 7000
    172.20.13.229:7000> set foo bar
    -> Redirected to slot [12182] located at 172.20.13.229:7001
    OK
    172.20.13.229:7001> set hello world
    -> Redirected to slot [866] located at 172.20.13.229:7000
    OK
    172.20.13.229:7000> get foo
    -> Redirected to slot [12182] located at 172.20.13.229:7001
    "bar"
    172.20.13.229:7001> get hello
    -> Redirected to slot [866] located at 172.20.13.229:7000
    "world"
    172.20.13.229:7000>

redis-cli 对集群的支持是非常基本的， 所以它总是依靠 Redis 集群节点来将它转向（redirect）至正确的节点。一个真正的（serious）集群客户端应该做得比这更好： 它应该用缓存记录起哈希槽与节点地址之间的映射（map）， 从而直接将命令发送到正确的节点上面。这种映射只会在集群的配置出现某些修改时变化， 比如说， 在一次故障转移（failover）之后， 或者系统管理员通过添加节点或移除节点来修改了集群的布局（layout）之后， 诸如此类。