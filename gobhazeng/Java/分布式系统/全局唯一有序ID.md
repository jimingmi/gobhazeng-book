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

**代码实现**


```java
public class IdWorker {

	private final long workerId;
	private final static long twepoch = 1288834974657L;
	private long sequence = 0L;
	private final static long workerIdBits = 4L;
	public final static long maxWorkerId = -1L ^ -1L << workerIdBits;
	private final static long sequenceBits = 10L;
	private final static long workerIdShift = sequenceBits;
	private final static long timestampLeftShift = sequenceBits + workerIdBits;
	public final static long sequenceMask = -1L ^ -1L << sequenceBits;
	private long lastTimestamp = -1L;

	public IdWorker(final long workerId) {
		super();
		if (workerId > this.maxWorkerId || workerId < 0) {
			throw new IllegalArgumentException(
					String.format("worker Id can't be greater than %d or less than 0", this.maxWorkerId));
		}
		this.workerId = workerId;
	}

	public synchronized long nextId() {
		long timestamp = this.timeGen();
		if (this.lastTimestamp == timestamp) {
			this.sequence = (this.sequence + 1) & this.sequenceMask;
			if (this.sequence == 0) {
				System.out.println("###########" + sequenceMask);
				timestamp = this.tilNextMillis(this.lastTimestamp);
			}
		} else {
			this.sequence = 0;
		}
		if (timestamp < this.lastTimestamp) {
			try {
				throw new Exception(String.format("Clock moved backwards. Refusing to generate id for %d milliseconds",
						this.lastTimestamp - timestamp));
			} catch (Exception e) {
				e.printStackTrace();
			}
		}

		this.lastTimestamp = timestamp;
		long nextId = ((timestamp - twepoch << timestampLeftShift)) | (this.workerId << this.workerIdShift)
				| (this.sequence);
		System.out.println("timestamp:" + timestamp + ",timestampLeftShift:" + timestampLeftShift + ",nextId:" + nextId
				+ ",workerId:" + workerId + ",sequence:" + sequence);
		return nextId;
	}

	private long tilNextMillis(final long lastTimestamp) {
		long timestamp = this.timeGen();
		while (timestamp <= lastTimestamp) {
			timestamp = this.timeGen();
		}
		return timestamp;
	}

	private long timeGen() {
		return System.currentTimeMillis();
	}

	public static void main(String[] args) {
		IdWorker worker2 = new IdWorker(2);
		System.out.println(worker2.nextId());
	}

}
```