#### 一.Redis三种集群方案

##### 1.主从复制模式

```mermaid
graph TD
A[Master] --> B(SlaveX)
A[Master] --> C(SlaveY)
A[Master] --> D(SlaveZ)
B(SlaveX) --> E1(SlaveX1)
B(SlaveX) --> E2(SlaveX2)
B(SlaveX) --> E3(SlaveX3)
C(SlaveX) --> F1(SlaveX1)
C(SlaveX) --> F2(SlaveX2)
C(SlaveX) --> F3(SlaveX3)
D(SlaveX) --> G1(SlaveX1)
D(SlaveX) --> G2(SlaveX2)
D(SlaveX) --> G3(SlaveX3)
```

```
master数据库支持读写，slave只支持读取，master改变
引入主从复制机制的目的:
a.读写分离，缓解master读写压力
b.方便进行容灾恢复
```

主从复制原理(先执行全量同步，再执行增量同步，若增量同步失效，就执行全量同步)

```
从数据库启动成功后，连接主数据库，发送 SYNC 命令；
主数据库接收到 SYNC 命令后，开始执行 BGSAVE 命令生成 RDB 文件并使用缓冲区记录此后执行的所有写命令；
主数据库 BGSAVE 执行完后，向所有从数据库发送快照文件，并在发送期间继续记录被执行的写命令；
从数据库收到快照文件后丢弃所有旧数据，载入收到的快照；
主数据库快照发送完毕后开始向从数据库发送缓冲区中的写命令；
从数据库完成对快照的载入，开始接收命令请求，并执行来自主数据库缓冲区的写命令；（从数据库初始化完成）
主数据库每执行一个写命令就会向从数据库发送相同的写命令，从数据库接收并执行收到的写命令（从数据库初始化完成后的操作）
出现断开重连后，2.8之后的版本会将断线期间的命令传给从数据库，增量复制。
主从刚刚连接的时候，进行全量同步；全同步结束后，进行增量同步。当然，如果有需要，slave在任何时候都可以发起全量同步。Redis的策略是，无论如何，首先会尝试进行增量同步，如不成功，要求从机进行全量同步。
```

主从复制优点

```
支持主从复制，主机会自动将数据同步到从机，可以进行读写分离；
为了分载 Master 的读操作压力，Slave 服务器可以为客户端提供只读操作的服务，写服务仍然必须由Master来完成；
Slave 同样可以接受其它 Slaves 的连接和同步请求，这样可以有效的分载 Master 的同步压力；
Master Server 是以非阻塞的方式为 Slaves 提供服务。所以在 Master-Slave 同步期间，客户端仍然可以提交查询或修改请求；
Slave Server 同样是以非阻塞的方式完成数据同步。在同步期间，如果有客户端提交查询请求，Redis则返回同步之前的数据；
```

主从复制缺点

```
Redis不具备自动容错和恢复功能，主机从机的宕机都会导致前端部分读写请求失败，需要等待机器重启或者手动切换前端的IP才能恢复（也就是要人工介入）；
主机宕机，宕机前有部分数据未能及时同步到从机，切换IP后还会引入数据不一致的问题，降低了系统的可用性；
如果多个 Slave 断线了，需要重启的时候，尽量不要在同一时间段进行重启。因为只要 Slave 启动，就会发送sync 请求和主机全量同步，当多个 Slave 重启的时候，可能会导致 Master IO 剧增从而宕机。
Redis 较难支持在线扩容，在集群容量达到上限时在线扩容会变得很复杂；
```

docker-compose 文件

```yaml
version: '3.6'
services:
  master:
    image: redis
    container_name: redis-master
    restart: always
    command: redis-server --port 6379 --requirepass master123  --appendonly yes
    ports:
      - 6379:6379
    volumes:
      - ./data/master:/data

  slave1:
    image: redis
    container_name: redis-slave-1
    restart: always
    command: redis-server --slaveof master 6379 --port 6380  --requirepass slave123 --masterauth master123  --appendonly yes
    ports:
      - 6380:6380
    volumes:
      - ./data/slave1:/data


  slave2:
    image: redis
    container_name: redis-slave-2
    restart: always
    command: redis-server --slaveof master 6379 --port 6381  --requirepass slave456 --masterauth master123  --appendonly yes
    ports:
      - 6381:6381
    volumes:
      - ./data/slave2:/data
```

##### 2.Sentinel(哨兵)模式

```
哨兵模式是一种特殊的模式，首先 Redis 提供了哨兵的命令，哨兵是一个独立的进程，作为进程，它会独立运行。其原理是哨兵通过发送命令，等待Redis服务器响应，从而监控运行的多个 Redis 实例。
通过发送命令，让 Redis 服务器返回监控其运行状态，包括主服务器和从服务器；
当哨兵监测到 master 宕机，会自动将 slave 切换成 master ，然后通过发布订阅模式通知其他的从服务器，修改配置文件，让它们切换主机；
然而一个哨兵进程对Redis服务器进行监控，也可能会出现问题，为此，我们可以使用多个哨兵进行监控。各个哨兵之间还会进行监控，这样就形成了多哨兵模式。
```

哨兵模式原理

```
每个Sentinel（哨兵）进程以每秒钟一次的频率向整个集群中的 Master 主服务器，Slave 从服务器以及其他Sentinel（哨兵）进程发送一个 PING 命令。
如果一个实例（instance）距离最后一次有效回复 PING 命令的时间超过 down-after-milliseconds 选项所指定的值， 则这个实例会被 Sentinel（哨兵）进程标记为主观下线（SDOWN）- subjective
如果一个 Master 主服务器被标记为主观下线（SDOWN），则正在监视这个 Master 主服务器的所有 Sentinel（哨兵）进程要以每秒一次的频率确认 Master 主服务器的确进入了主观下线状态
当有足够数量的 Sentinel（哨兵）进程（大于等于配置文件指定的值）在指定的时间范围内确认 Master 主服务器进入了主观下线状态（SDOWN）， 则 Master 主服务器会被标记为客观下线（ODOWN）- objective
在一般情况下， 每个 Sentinel（哨兵）进程会以每 10 秒一次的频率向集群中的所有 Master 主服务器、Slave 从服务器发送 INFO 命令。
当 Master 主服务器被 Sentinel（哨兵）进程标记为客观下线（ODOWN）时，Sentinel（哨兵）进程向下线的 Master 主服务器的所有 Slave 从服务器发送 INFO 命令的频率会从 10 秒一次改为每秒一次。
若没有足够数量的 Sentinel（哨兵）进程同意 Master主服务器下线， Master 主服务器的客观下线状态就会被移除。若 Master 主服务器重新向 Sentinel（哨兵）进程发送 PING 命令返回有效回复，Master主服务器的主观下线状态就会被移除。
```

哨兵模式优点

```
哨兵模式是基于主从模式的，所有主从的优点，哨兵模式都具有。
主从可以自动切换，系统更健壮，可用性更高(可以看作自动版的主从复制)。
```

哨兵模式缺点

```
Redis较难支持在线扩容，在集群容量达到上限时在线扩容会变得很复杂。
```

##### 3.Cluster模式

```
Redis分布式存储
```

集群的数据分片

```
Redis 集群没有使用一致性 hash，而是引入了哈希槽【hash slot】的概念。

Redis 集群有16384 个哈希槽，每个 key 通过 CRC16 校验后对 16384 取模来决定放置哪个槽。集群的每个节点负责一部分hash槽，举个例子，比如当前集群有3个节点，那么：

节点 A 包含 0 到 5460 号哈希槽
节点 B 包含 5461 到 10922 号哈希槽
节点 C 包含 10923 到 16383 号哈希槽
这种结构很容易添加或者删除节点。比如如果我想新添加个节点 D ， 我需要从节点 A， B， C 中得部分槽到 D 上。如果我想移除节点 A ，需要将 A 中的槽移到 B 和 C 节点上，然后将没有任何槽的 A 节点从集群中移除即可。由于从一个节点将哈希槽移动到另一个节点并不会停止服务，所以无论添加删除或者改变某个节点的哈希槽的数量都不会造成集群不可用的状态。

在 Redis 的每一个节点上，都有这么两个东西，一个是插槽（slot），它的的取值范围是：0-16383。还有一个就是 cluster，可以理解为是一个集群管理的插件。当我们的存取的 Key到达的时候，Redis 会根据 CRC16 的算法得出一个结果，然后把结果对 16384 求余数，这样每个 key 都会对应一个编号在 0-16383 之间的哈希槽，通过这个值，去找到对应的插槽所对应的节点，然后直接自动跳转到这个对应的节点上进行存取操作。
```

主从复制模型

```
为了保证高可用，redis-cluster集群引入了主从复制模型，一个主节点对应一个或者多个从节点，当主节点宕机的时候，就会启用从节点。当其它主节点 ping 一个主节点 A 时，如果半数以上的主节点与 A 通信超时，那么认为主节点 A 宕机了。如果主节点 A 和它的从节点 A1 都宕机了，那么该集群就无法再提供服务了。
```

```
所有的 redis 节点彼此互联(PING-PONG机制)，内部使用二进制协议优化传输速度和带宽。
节点的 fail 是通过集群中超过半数的节点检测失效时才生效。
客户端与 Redis 节点直连，不需要中间代理层.客户端不需要连接集群所有节点，连接集群中任何一个可用节点即可。
```

##### 4.基本命令

```shell
config get *
1.string 字符串
SET A "big A"
GET A
2.hash 哈希
HMSET B filed1 "big B" field2 "this is B"
HGET B field2
3.list 列表
LPUSH C 1 2 3 5 4
LRANGE C 0 5
4.set 集合
SADD D 2
SADD D 3
SMEMEMBERS D
5.sorted set 有序集合
ZADD E 0 a
ZADD E 1 b
ZADD E 0 c
ZRANGEBYSCORE E
```







