使用redis-master.conf redis-slave.conf启动docker 即可主从复制
```
docker run --privileged=true --name master --network mynetwork --ip 172.18.0.3  -p 63791:6379  -v /Users/kirra/Desktop/project/docker-using/redis\ master\ salve\ with\ docker/redis-master.conf:/usr/local/etc/redis/redis.conf -d  redis:3.2.9 redis-server /usr/local/etc/redis/redis.conf
docker run --privileged=true --name salve1 --network mynetwork --ip 172.18.0.4 -p 63792:6379   -v /Users/kirra/Desktop/project/docker-using/redis\ master\ salve\ with\ docker/redis-slave.conf:/usr/local/etc/redis/redis.conf -d redis:3.2.9 redis-server /usr/local/etc/redis/redis.conf
docker run --privileged=true --name salve2 --network mynetwork --ip 172.18.0.5 -p 63793:6379   -v /Users/kirra/Desktop/project/docker-using/redis\ master\ salve\ with\ docker/redis-slave.conf:/usr/local/etc/redis/redis.conf -d redis:3.2.9 redis-server /usr/local/etc/redis/redis.conf
```
