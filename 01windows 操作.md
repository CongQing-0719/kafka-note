# 配置kafka运行环境
kafka的包中已经包含了zookeeper但是我还是喜欢单独下载一个zookeeper
## 下载kafka
https://archive.apache.org/dist/kafka/2.6.3/kafka_2.12-2.6.3.tgz

## 下载zookeeper
https://www.apache.org/dyn/closer.lua/zookeeper/zookeeper-3.6.3/apache-zookeeper-3.6.3-bin.tar.gz


## 配置zookeeper
解压下载的zookeeper安装包
![image](https://user-images.githubusercontent.com/40539414/182032056-4a742065-787a-43fa-9ecd-97e3e472187a.png)
复制三份并修改文件夹的名字(其实可以用三份配置但是我喜欢复制三份)

### 修改点一 新建zkData文件夹
在每一份的目录下新建zkData文件夹
![image](https://user-images.githubusercontent.com/40539414/182032122-b1957e61-bf47-46cb-9504-39b92682e995.png)

### 修改点二 新建myid文件
在每一份的zkData文件夹中新建myid文件，并分别把内容修改为 1 2 3 
![image](https://user-images.githubusercontent.com/40539414/182032175-97c4d81d-6947-420b-8316-3eb8756facd2.png)
![image](https://user-images.githubusercontent.com/40539414/182032187-5c7b717d-fd75-415b-9309-0850605163a0.png)

### 修改点三 修改配置文件
将conf目录下的zoo_sample.cfg 文件修改为 zoo.cfg,并修改文件内容


![image](https://user-images.githubusercontent.com/40539414/182032218-1df40518-f11c-478e-a2a3-361941d0c9af.png)

![image](https://user-images.githubusercontent.com/40539414/182032232-0daad4ab-045b-4790-be6d-0aca82a9a88c.png)

![image](https://user-images.githubusercontent.com/40539414/182032258-888a856c-5fd5-4ccb-beda-d9740245bb28.png)

修改文件内容
第一处 dataDir 修改为zkData文件夹 注意路径要用双反斜杠
![image](https://user-images.githubusercontent.com/40539414/182032278-91426b1d-e582-4daa-a87c-d9c672b3ff9d.png)

第二处 端口号  因为是在一台机器上跑多个实例所以端口号不能冲突
![image](https://user-images.githubusercontent.com/40539414/182032329-b8acecaf-c93f-4d4d-9e57-2bad7f20ec75.png)

第三处添加入下内容
![image](https://user-images.githubusercontent.com/40539414/182032372-8fa4d5a2-ec9b-4102-b810-ac0874cfbe00.png)
```
server.1=localhost:2881:3881
server.2=localhost:2882:3882
server.3=localhost:2883:3883
```
配置参数说明
server.A=B:C:D
A是一个数字，表示这个是第几号服务器；
集群模式下配置一个文件myid，这个文件在dataDir目录下，这个文件里面有一个数据就是A的值，Zookeeper启动时读取此文件，拿到里面的数据与zoo.cfg里面的配置信息比较从而判断到底是哪个server。
B是这个服务器的地址；
C是这个服务器Follower与集群中的Leader服务器交换信息的端口；
D是万一集群中的Leader服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互通信的端口。

修改后的三个文件内容
```
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=D:\\soft-repository\\zookeeper\\apache-zookeeper-3.6.3-bin-1\\zkData
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1

## Metrics Providers
#
# https://prometheus.io Metrics Exporter
#metricsProvider.className=org.apache.zookeeper.metrics.prometheus.PrometheusMetricsProvider
#metricsProvider.httpPort=7000
#metricsProvider.exportJvmInfo=true
server.1=localhost:2881:3881
server.2=localhost:2882:3882
server.3=localhost:2883:3883
```

## 启动zookeeper集群

在cmd中进入每一份的zookeeper文件夹中，执行bin\zkServer.cmd
![image](https://user-images.githubusercontent.com/40539414/182032555-844cd57d-75ea-450e-a433-7f8bc0477266.png)



## 配置kafka
在D盘根目录复制解压后的kafka三份

### 修改config\server.properties

broker.id=0
log.dirs=/tmp/kafka-logs-1
zookeeper.connect=localhost:2181,localhost:2182,localhost:2183/kafka
port=9091

端口号三份分别为9091 9092 9093

```
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# see kafka.server.KafkaConfig for additional details and defaults

############################# Server Basics #############################

# The id of the broker. This must be set to a unique integer for each broker.
broker.id=1

############################# Socket Server Settings #############################

# The address the socket server listens on. It will get the value returned from 
# java.net.InetAddress.getCanonicalHostName() if not configured.
#   FORMAT:
#     listeners = listener_name://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
#listeners=PLAINTEXT://:9092

# Hostname and port the broker will advertise to producers and consumers. If not set, 
# it uses the value for "listeners" if configured.  Otherwise, it will use the value
# returned from java.net.InetAddress.getCanonicalHostName().
#advertised.listeners=PLAINTEXT://your.host.name:9092

# Maps listener names to security protocols, the default is for them to be the same. See the config documentation for more details
#listener.security.protocol.map=PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL

# The number of threads that the server uses for receiving requests from the network and sending responses to the network
num.network.threads=3

# The number of threads that the server uses for processing requests, which may include disk I/O
num.io.threads=8

# The send buffer (SO_SNDBUF) used by the socket server
socket.send.buffer.bytes=102400

# The receive buffer (SO_RCVBUF) used by the socket server
socket.receive.buffer.bytes=102400

# The maximum size of a request that the socket server will accept (protection against OOM)
socket.request.max.bytes=104857600


############################# Log Basics #############################

# A comma separated list of directories under which to store log files
log.dirs=/tmp/kafka-logs-1

# The default number of log partitions per topic. More partitions allow greater
# parallelism for consumption, but this will also result in more files across
# the brokers.
num.partitions=1

# The number of threads per data directory to be used for log recovery at startup and flushing at shutdown.
# This value is recommended to be increased for installations with data dirs located in RAID array.
num.recovery.threads.per.data.dir=1

############################# Internal Topic Settings  #############################
# The replication factor for the group metadata internal topics "__consumer_offsets" and "__transaction_state"
# For anything other than development testing, a value greater than 1 is recommended to ensure availability such as 3.
offsets.topic.replication.factor=1
transaction.state.log.replication.factor=1
transaction.state.log.min.isr=1

############################# Log Flush Policy #############################

# Messages are immediately written to the filesystem but by default we only fsync() to sync
# the OS cache lazily. The following configurations control the flush of data to disk.
# There are a few important trade-offs here:
#    1. Durability: Unflushed data may be lost if you are not using replication.
#    2. Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.
#    3. Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to excessive seeks.
# The settings below allow one to configure the flush policy to flush data after a period of time or
# every N messages (or both). This can be done globally and overridden on a per-topic basis.

# The number of messages to accept before forcing a flush of data to disk
#log.flush.interval.messages=10000

# The maximum amount of time a message can sit in a log before we force a flush
#log.flush.interval.ms=1000

############################# Log Retention Policy #############################

# The following configurations control the disposal of log segments. The policy can
# be set to delete segments after a period of time, or after a given size has accumulated.
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens
# from the end of the log.

# The minimum age of a log file to be eligible for deletion due to age
log.retention.hours=168

# A size-based retention policy for logs. Segments are pruned from the log unless the remaining
# segments drop below log.retention.bytes. Functions independently of log.retention.hours.
#log.retention.bytes=1073741824

# The maximum size of a log segment file. When this size is reached a new log segment will be created.
log.segment.bytes=1073741824

# The interval at which log segments are checked to see if they can be deleted according
# to the retention policies
log.retention.check.interval.ms=300000

############################# Zookeeper #############################

# Zookeeper connection string (see zookeeper docs for details).
# This is a comma separated host:port pairs, each corresponding to a zk
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".
# You can also append an optional chroot string to the urls to specify the
# root directory for all kafka znodes.
zookeeper.connect=localhost:2181,localhost:2182,localhost:2183/kafka

# Timeout in ms for connecting to zookeeper
zookeeper.connection.timeout.ms=18000


############################# Group Coordinator Settings #############################

# The following configuration specifies the time, in milliseconds, that the GroupCoordinator will delay the initial consumer rebalance.
# The rebalance will be further delayed by the value of group.initial.rebalance.delay.ms as new members join the group, up to a maximum of max.poll.interval.ms.
# The default value for this is 3 seconds.
# We override this to 0 here as it makes for a better out-of-the-box experience for development and testing.
# However, in production environments the default value of 3 seconds is more suitable as this will help to avoid unnecessary, and potentially expensive, rebalances during application startup.
group.initial.rebalance.delay.ms=0

port=9091
```

## 启动kafka
```cmd
bin\windows\kafka-server-start.bat config\server.properties
```

# topic

## 查看topic
```cmd
bin\windows\kafka-topics.bat --bootstrap-server localhost:9091 --list
```

## 创建topic
```cmd
bin\windows\kafka-topics.bat --bootstrap-server localhost:9091 --create --partitions 1 --replication-factor 1 --topic hello-test
```

## 查看topic的详情

```cmd
bin\windows\kafka-topics.bat --bootstrap-server localhost:9091 --describe --topic hello-test
```

## 修改分区数
注意：分区数只能增加，不能减少
```cmd
bin\windows\kafka-topics.bat --bootstrap-server localhost:9091 --alter --topic hello-test --partitions 3
```

## 删除topic
```cmd
bin\windows\kafka-topics.bat --bootstrap-server localhost:9091 --delete --topic hello
```

# 集群压力测试
用 Kafka自带的脚本，对 Kafka 进行压测。
生产者压测脚本：kafka-producer-perf-test.sh
消费者压测脚本：kafka-consumer-perf-test.sh

## Kafka Producer 压力测试

### 创建一个 hello-test topic，设置为 3 个分区 3 个副本
```cmd
bin\windows\kafka-topics.bat --bootstrap-server localhost:9091 --create --replication-factor 3 --partitions 3 --topic hello-test
```

### 测试生产者
```cmd
bin\windows\kafka-producer-perf-test.bat --topic hello-test --record-size 1024 --num-records 1000000 --throughput -1 --producer-props bootstrap.servers=localhost:9091,localhost:9092,localhost:9093 batch.size=16384 linger.ms=0
```
--record-size 是一条信息有多大，单位是字节，本次测试设置为 1k。
--num-records 是总共发送多少条信息，本次测试设置为 100 万条。
--throughput 是每秒多少条信息，设成-1，表示不限流，尽可能快的生产数据，可测出生产者最大吞吐量。
--producer-props 后面可以配置生产者相关参数，batch.size 配置为 16k

#### 调整 batch.size 大小
batch.size 默认值是 16k 将 batch.size 设置为 32k
```cmd
bin\windows\kafka-producer-perf-test.bat --topic hello-test --record-size 1024 --num-records 1000000 --throughput 10000 --producer-props bootstrap.servers=localhost:9091,localhost:9092,localhost:9093 batch.size=32768 linger.ms=0
```

batch.size 默认值是 16k。将 batch.size 设置为 4k。
```cmd
bin\windows\kafka-producer-perf-test.bat --topic hello-test --record-size 1024 --num-records 1000000 --throughput 10000 --producer-props bootstrap.servers=localhost:9091,localhost:9092,localhost:9093 batch.size=4096 linger.ms=0
```
#### 调整 linger.ms
linger.ms 默认是 0ms。将 linger.ms 设置为 50ms。
```cmd
bin\windows\kafka-producer-perf-test.bat --topic hello-test --record-size 1024 --num-records 1000000 --throughput 10000 --producer-props bootstrap.servers=localhost:9091,localhost:9092,localhost:9093 batch.size=4096 linger.ms=50
```

#### 压缩方式
调整压缩方式 默认的压缩方式是 none。将compression.type 设置为 snappy。
```cmd
bin\windows\kafka-producer-perf-test.bat --topic hello-test --record-size 1024 --num-records 1000000 --throughput 10000 --producer-props bootstrap.servers=localhost:9091,localhost:9092,localhost:9093 batch.size=4096 linger.ms=50 compression.type=snappy
```

默认的压缩方式是 none。将compression.typee 设置为 zstd。
```cmd
bin\windows\kafka-producer-perf-test.bat --topic hello-test --record-size 1024 --num-records 1000000 --throughput 10000 --producer-props bootstrap.servers=localhost:9091,localhost:9092,localhost:9093 batch.size=4096 linger.ms=50 compression.type=zstd
```
默认的压缩方式是 none。将compression.type 设置为 gzip。
```cmd
bin\windows\kafka-producer-perf-test.bat --topic hello-test --record-size 1024 --num-records 1000000 --throughput 10000 --producer-props bootstrap.servers=localhost:9091,localhost:9092,localhost:9093 batch.size=4096 linger.ms=50 compression.type=gzip
```
默认的压缩方式是 none。将compression.type 设置为 lz4
```cmd
bin\windows\kafka-producer-perf-test.bat --topic hello-test --record-size 1024 --num-records 1000000 --throughput 10000 --producer-props bootstrap.servers=localhost:9091,localhost:9092,localhost:9093 batch.size=4096 linger.ms=50 compression.type=lz4
```

#### 调整缓存大小buffer.memory

调整缓存大小 默认生产者端缓存大小 32m。将 buffer.memory 设置为 64m。

```cmd
bin\windows\kafka-producer-perf-test.bat --topic hello-test --record-size 1024 --num-records 1000000 --throughput -1 --producer-props bootstrap.servers=localhost:9091,localhost:9092,localhost:9093 batch.size=4096 linger.ms=50 buffer.memory=67108864
```


### 测试Consumer性能

#### 默认情况的吞吐量
```cmd
bin\windows\kafka-consumer-perf-test.bat --bootstrap-server localhost:9091,localhost:9092,localhost:9093 --topic hello-test --messages 10000000 --consumer.config config/consumer.properties --timeout 10000
```
--bootstrap-server 指定 Kafka 集群地址
--topic 指定 topic 的名称
--messages 总共要消费的消息个数

![image](https://user-images.githubusercontent.com/40539414/182033176-2b88b789-a1ff-4afa-943d-02e23047a879.png)



#### 调整 max.poll.records
一次拉取条数为 2000
修改config/consumer.properties 文件中的一次拉取条数为 2000
```
max.poll.records=2000
```

![image](https://user-images.githubusercontent.com/40539414/182033220-014a3516-8192-4725-abbd-a4ab91d2d4a2.png)

一次拉取条数为 20000
修改config/consumer.properties 文件中的一次拉取条数为 20000
```
max.poll.records=20000
```
![image](https://user-images.githubusercontent.com/40539414/182033279-e62bafa3-7b47-4de4-83c5-69e107ec3d99.png)

一次拉取条数为 200000
修改config/consumer.properties 文件中的一次拉取条数为 200000
```
max.poll.records=200000
```
可以看到每秒的消息数量已经不会提升了
![image](https://user-images.githubusercontent.com/40539414/182033323-51fabea7-2562-4160-bbeb-bae631de26ab.png)

一次拉取条数为 100000
修改config/consumer.properties 文件中的一次拉取条数为 100000
```
max.poll.records=100000
```
![image](https://user-images.githubusercontent.com/40539414/182033400-9b2e4b08-41e4-4d80-b0c9-b07b1e085789.png)


#### fetch.max.bytes
修改/opt/module/kafka/config/consumer.properties 文件中的拉取一批数据大小 100m
```
fetch.max.bytes=104857600
```

