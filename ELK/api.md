* 修改最小主节点数
```
put
http://<ip>:<port>/_cluster/settings
{
    "transient" : {
        "discovery.zen.minimum_master_nodes" : 2   // 计算方法：( master 候选节点个数 / 2) + 1
    }
}
```
* 设置license
```
put
http://<ip>:<port>/_xpack/license?acknowledge=true
{
    "license":{
        "uid": "7b989ff3-61ec-497b-a106-c2567ab4955d",
        "type": "platinum",  // 修改类型为platinum，我们所需要的 watcher 和 machine learning 就都能用了
        "issue_date_in_millis":1508457600000,
        "expiry_date_in_millis": 1508457600000,   // 过期时间，随意修改
        "max_nodes":100,
        "issued_to":"diancan MT (mt)",
        "issuer":"Web Form",
        "signature":"AAAAAwAAAA22Z4GFoVF9hKAmfYaJTNS0+rvnKzQ0hhLanm3YKRx9Q7HUk5M7/5IAB9zKzcuy+Iopvpv",
        "start_date_in_millis":1508457600000
    }
}
```
* 修改副本数量
```
put
http://<ip>:<port>/<index>/_settings
{
    "index": {
        "number_of_replicas": 1  // 副本数量
     }
}
```
* 手动修改分片分布
```
post
http://<ip>:<port>/_cluster/reroute
{
    "commands": [{
        "allocate_replica": {
            "index": <index>,  // 索引名
            "shard": <shard>,  // 分片编号
            "node": <machine>   // 目标节点名
        }
    }]
}
```
* 删除索引
```
delete
http://<ip>:<port>/<index>
```
* 批量删除数据
```
post
http://<ip>:<port>/<index>/_delete_by_query    // index 直接正则匹配，例如 *, prajna-beta-*
{
    "query": {
        "range": {
            "@timestamp": {
                "lt": 'now-3d'   // 删除三天之前的数据
            }
        }
    }
}
```
* 获取集群健康状态
```
curl 'http://<ip>:<port>/_cluster/health?pretty'
```
* 获取es节点情况
```
get
http://<ip>:<port>/_nodes/stats/fs?pretty=1
```
* 获取分片情况
```
get
http://<ip>:<port>/_cat/shards"
```
* 获取unassigned分片
```
curl -s "http://<ip>:<port>/_cat/shards" | grep UNASSIGNED
```
