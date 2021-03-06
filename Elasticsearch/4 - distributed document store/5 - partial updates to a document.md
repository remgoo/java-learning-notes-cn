## 文档的部分更新(Partial Update) ##

update API会结合读和写来完成一次部分更新。

![](http://www.elasticsearch.org/guide/en/elasticsearch/guide/current/images/04-04_update.png)

下面对部分更新的步骤进行解释：

1. 客户端发送一个更新请求到节点1。
2. 节点1将请求转发到节点3，因为主要分片被分配在该节点上。
3. 节点3从主要分片中获取对应的文档，修改JSON文档中的_source字段，然后试图在该主要分片中对修改后的文档重新索引(Reindex)。如果该文档已经另外一个进程给修改了，那么它会根据`retry_on_conflict`设置的次数重试。
4. 如果节点3能够成功更新文档，它会将新版本的文档通过并行的方式给转发到副本分片所在的节点1和节点2上，也让它们进行重索引的操作。一旦所有的副本节点也执行成功，节点3就会将这一消息发送给请求节点(Requesting Node，此处就是节点1)。然后请求节点给客户端返回响应。

update API也能够接受接受`routing`，'replication'，'consistency'和'timeout'参数。

> **基于文档的复制**
> 当一个主要分片将修改转发给它的副本分片时，它不会转达更新请求。它转发的是新版本的完整文档。需要记住这些转发到副本分片的请求是异步的，也就是说它们到达的顺序和发送的顺序是不确定的。如果ES仅仅是转发修改，那么这些修改就可能以错误地顺序被接受，从而导致数据的损毁。