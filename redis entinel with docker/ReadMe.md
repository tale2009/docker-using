### 配置docker网络环境
哨兵需要通过网络监听主从Redis的状态
```
docker: Error response from daemon: user specified IP address is supported on user defined networks only.
```
docker原有的网络配置是无法给容器固定IP所以需要自建自己的网络。
```
docker network create --subnet=172.18.0.0/16 mynetwork
```
### 组建Redis Sentinel
![](https://github.com/tale2009/docker-using/blob/main/redis%20sentinel%20with%20docker/image.png?raw=true)
<center>拓补图</center>

#### Redis主从复制
```
docker run --privileged=true --name master --network mynetwork --ip 172.18.0.3  -p 63791:6379  -v /Users/kirra/Desktop/project/java02/Week_12/homework1/sentinel/redis-master.conf:/usr/local/etc/redis/redis.conf -d  redis:3.2.9 redis-server /usr/local/etc/redis/redis.conf
docker run --privileged=true --name salve1 --network mynetwork --ip 172.18.0.4  -p 63792:6379  -v /Users/kirra/Desktop/project/java02/Week_12/homework1/sentinel/redis-slave.conf:/usr/local/etc/redis/redis.conf -d  redis:3.2.9 redis-server /usr/local/etc/redis/redis.conf
docker run --privileged=true --name salve2 --network mynetwork --ip 172.18.0.5  -p 63793:6379  -v /Users/kirra/Desktop/project/java02/Week_12/homework1/sentinel/redis-slave.conf:/usr/local/etc/redis/redis.conf -d  redis:3.2.9 redis-server /usr/local/etc/redis/redis.conf
```
#### 启动Sentinel集群
哨兵启动后会自动检查目标节点，并且如果正常启动发现有其他sentinel节点会自动感知对方。
```
docker run --privileged=true --name sentinel1 --network mynetwork --ip 172.18.0.6  -p 26370:26379  -v /Users/kirra/Desktop/project/java02/Week_12/homework1/sentinel/sentinel.conf:/usr/local/etc/redis/sentinel.conf -d  redis:3.2.9 redis-sentinel /usr/local/etc/redis/sentinel.conf
docker run --privileged=true --name sentinel2 --network mynetwork --ip 172.18.0.7  -p 26371:26379  -v /Users/kirra/Desktop/project/java02/Week_12/homework1/sentinel/sentinel1.conf:/usr/local/etc/redis/sentinel.conf -d  redis:3.2.9 redis-sentinel /usr/local/etc/redis/sentinel.conf
docker run --privileged=true --name sentinel3 --network mynetwork --ip 172.18.0.8  -p 26372:26379  -v /Users/kirra/Desktop/project/java02/Week_12/homework1/sentinel/sentinel2.conf:/usr/local/etc/redis/sentinel.conf -d  redis:3.2.9 redis-sentinel /usr/local/etc/redis/sentinel.conf
```
成功之后使用docker stop 命令去对master容器进行暂停，就能通过日志中看到集群自动修改目前master节点。