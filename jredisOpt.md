# jredis操作集群

Maven依赖:
```
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.7.2</version>
    <type>jar</type>
    <scope>compile</scope>
</dependency>
```

测试1：
 ```
 public static void main(String[] args) {
        String key = "foo";
        // 这东西 可以直接看到key 的分片数，就能知道放哪个 节点
        System.out.println(JedisClusterCRC16.getSlot(key));
        Set<HostAndPort> jedisClusterNodes = new HashSet<HostAndPort>();
        jedisClusterNodes.add(new HostAndPort("172.20.13.229", 7000));
        jedisClusterNodes.add(new HostAndPort("172.20.13.229", 7001));
        jedisClusterNodes.add(new HostAndPort("172.20.13.230", 7003));
        // 3个master 节点
        JedisCluster jc = new JedisCluster(jedisClusterNodes);
        System.out.println(jc.get(key));
        jc.setnx(key, "bar");
        String value = jc.get(key);
        System.out.println(value);
    }
  console：  
    12182
    bar
    bar
    ```
 测试2：
```
public static void main(String[] args) {
       Set<HostAndPort> jedisClusterNodes = new HashSet<HostAndPort>();
        jedisClusterNodes.add(new HostAndPort("172.20.13.229", 7000));
        jedisClusterNodes.add(new HostAndPort("172.20.13.229", 7001));
        jedisClusterNodes.add(new HostAndPort("172.20.13.230", 7003));
        JedisCluster jc = new JedisCluster(jedisClusterNodes);
        for (int i = 1; i <= 10000; i++) {
            long start = System.currentTimeMillis();
            jc.set("k:" + i, "v" + i);
            System.out.print("set " + i +"th value in " + (System.currentTimeMillis() - start) + " ms");
            start = System.currentTimeMillis();
            jc.get("k:" + i);
            System.out.println(", get " + i +"th value in " + (System.currentTimeMillis() - start)
            + " ms");
        }
 }
 console:
  set 9976th value in 1 ms, get 9976th value in 1 ms
  set 9977th value in 1 ms, get 9977th value in 1 ms
  set 9978th value in 0 ms, get 9978th value in 1 ms
  set 9979th value in 1 ms, get 9979th value in 1 ms
  set 9980th value in 1 ms, get 9980th value in 1 ms
  set 9981th value in 1 ms, get 9981th value in 0 ms
  set 9982th value in 1 ms, get 9982th value in 1 ms
  set 9983th value in 1 ms, get 9983th value in 0 ms
  set 9984th value in 1 ms, get 9984th value in 1 ms
  set 9985th value in 1 ms, get 9985th value in 0 ms
  set 9986th value in 1 ms, get 9986th value in 1 ms
  set 9987th value in 1 ms, get 9987th value in 0 ms
  set 9988th value in 1 ms, get 9988th value in 1 ms
  set 9989th value in 1 ms, get 9989th value in 1 ms
  set 9990th value in 1 ms, get 9990th value in 1 ms
  set 9991th value in 1 ms, get 9991th value in 1 ms
  set 9992th value in 1 ms, get 9992th value in 1 ms
  set 9993th value in 0 ms, get 9993th value in 1 ms
  set 9994th value in 1 ms, get 9994th value in 1 ms
  set 9995th value in 0 ms, get 9995th value in 1 ms
  set 9996th value in 2 ms, get 9996th value in 0 ms
  set 9997th value in 1 ms, get 9997th value in 1 ms
  set 9998th value in 1 ms, get 9998th value in 1 ms
  set 9999th value in 1 ms, get 9999th value in 1 ms
  set 10000th value in 0 ms, get 10000th value in 1 ms
    ```