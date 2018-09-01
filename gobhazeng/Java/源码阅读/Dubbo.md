# Dubbo 负载均衡

Dubbo有四种负载均衡策略：

 - Random LoadBalance：随机策略，配置值为`random`。可以设置权重，有利于充分利用服务器的资源，高配的可以设置权重大一些，低配的可以稍微小一些。
 - RoundRobin LoadBalance：轮询策略，配置值为`roundrobin`。
 - LeastActive LoadBalance：配置值为leastactive。根据请求调用的次数计数，处理请求更慢的节点会受到更少的请求。
 - ConsistentHash LoadBalance：一致性Hash策略，具体配置方法可以参考Dubbo文档。相同调用参数的请求会发送到同一个服务提供方节点上，如果某个节点发生故障无法提供服务，则会基于一致性Hash算法映射到虚拟节点上（其他服务提供方）。
