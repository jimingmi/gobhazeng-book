# 全局唯一有序ID

数据在分片时，典型的是分库分表，就有一个全局ID生成的问题。

单纯的生成全局ID并不是什么难题，但是生成的ID通常要满足分片的一些要求：

- 1. 不能有单点故障。
- 2. 以时间为序，或者ID里包含时间。这样一是可以少一个索引，二是冷热数据容易分离。
- 3. 可以控制`ShardingId`。比如某一个用户的文章要放在同一个分片内，这样查询效率高，修改也容易。
- 4. 不要太长，最好`64bit`。使用`long`比较好操作，如果是`96bit`，那就要各种移位相当的不方便，还有可能有些组件不能支持这么大的ID。

# Twitter的方案——Snowflake

Twitter在把存储系统从MySQL迁移到Cassandra的过程中由于Cassandra没有顺序ID生成机制，于是自己开发了一套全局唯一ID生成服务：Snowflake。

- 1. 41位的时间序列（精确到毫秒，41位的长度可以使用69年）
- 2. 10位的机器标识（10位的长度最多支持部署1024个节点）
- 3. 12位的计数顺序号（12位的计数顺序号支持每个节点每毫秒产生4096个ID序号） 最高位是符号位，始终为0。

> 优点：高性能，低延迟；独立的应用；按时间有序。 缺点：需要独立的开发和部署。

![](_v_images/_1521116873_16378.png)

```java

```