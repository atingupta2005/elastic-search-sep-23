# Diagnose and repair shard issues
```
PUT /logs
```

```
GET _cat/nodes?v
GET _cat/indices?v
```


```
GET _cluster/allocation/explain
{
 "index": "logs",
 "shard": 0,
 "primary": true
}
```

```
PUT logs/_settings
{
 "number_of_replicas": 3
}
```


```
GET _cat/indices?v
```

```
GET _cluster/allocation/explain
{
 "index": "logs",
 "shard": 0,
 "primary": true
}
```

```
GET _cat/shards/logs?v
```

```
GET _cat/indices?v
```

```
PUT logs/_settings
{
 "number_of_replicas": 1
}
```

```
GET _cat/indices?v
```

```
GET _cluster/allocation/explain
{
 "index": "logs",
 "shard": 0,
 "primary": true
}
```

```
GET _cat/shards/logs?v
```

```
GET _cat/indices?v
```

```
PUT logs/_settings
{
 "index.routing.allocation.include._name": "data=3"
}
```


```
GET _cluster/allocation/explain
{
 "index": "logs",
 "shard": 0,
 "primary": true
}
```

