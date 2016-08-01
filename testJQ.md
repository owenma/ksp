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
