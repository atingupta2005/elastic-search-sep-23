# CAT APIs

### The verbose output gives a nice display of results of a cat command. In the example given below, we get the details of various indices present in the cluster.
```
GET _cat
```

```
GET _cat/indices?v
```

```
GET _cat/master?v=true
```


```
GET _cat/nodes?v
```


```
GET _cat/health?v
```


```
GET _cat/health?format=json
```


```
GET _cat/health?format=yaml
```


### The h parameter, also called header, is used to display only those columns mentioned in the command.
```
GET /_cat/nodes?h=ip,port
```


```
GET _cat/master?h=host,node&v
```


### The below example, gives a result of templates arranged in descending order of the filed index patterns.
```
GET _cat/templates?v&s=order:desc,index_patterns
```

### The count parameter provides the count of total number of documents in the entire cluster.
```
GET /_cat/count?v
```


### Each of the commands accepts a query string parameter help which will output its available columns. For example:
```
GET _cat/master?help
```