## Search as few fields as possible
- The more fields a query_string or multi_match query targets, the slower it is
- Technique to improve search speed over multiple fields is to copy their values into a single field at index time, and then use this field at search time
- This can be automated with the copy-to directive
```
PUT movies
{
  "mappings": {
    "properties": {
      "name_and_plot": {
        "type": "text"
      },
      "name": {
        "type": "text",
        "copy_to": "name_and_plot"
      },
      "plot": {
        "type": "text",
        "copy_to": "name_and_plot"
      }
    }
  }
}
```


## Pre-index data
- You should leverage patterns in your queries to optimize the way data is indexed. 
```
PUT index
{
  "mappings": {
    "properties": {
      "price_range": {
        "type": "keyword"
      }
    }
  }
}

PUT index/_doc/1
{
  "designation": "spoon",
  "price": 13,
  "price_range": "10-100"
}
```

```
GET index/_search
{
  "aggs": {
    "price_ranges": {
      "terms": {
        "field": "price_range"
      }
    }
  }
}
```

## Avoid scriptsedit
- If possible, avoid using script-based sorting, scripts in aggregations, and the script_score query.


## Replicas might help with throughput, but not alwaysedit
- In addition to improving resiliency, replicas can help improve throughput. For instance if you have a single-shard index and three nodes, you will need to set the number of replicas to 2 in order to have 3 copies of your shard in total so that all nodes are utilized.

- Now imagine that you have a 2-shards index and two nodes. In one case, the number of replicas is 0, meaning that each node holds a single shard. In the second case the number of replicas is 1, meaning that each node has two shards. Which setup is going to perform best in terms of search performance? Usually, the setup that has fewer shards per node in total will perform better. The reason for that is that it gives a greater share of the available filesystem cache to each shard, and the filesystem cache is probably Elasticsearch’s number 1 performance factor. At the same time, beware that a setup that does not have replicas is subject to failure in case of a single node failure, so there is a trade-off between throughput and availability.

- So what is the right number of replicas? If you have a cluster that has num_nodes nodes, num_primaries primary shards in total and if you want to be able to cope with max_failures node failures at once at most, then the right number of replicas for you is max(max_failures, ceil(num_nodes / num_primaries) - 1).

## Tune your queries with the Search Profileredit
- The Profile API provides detailed information about how each component of your queries and aggregations impacts the time it takes to process the request.

- The Search Profiler in Kibana makes it easy to navigate and analyze the profile results and give you insight into how to tune your queries to improve performance and reduce load.

- Because the Profile API itself adds significant overhead to the query, this information is best used to understand the relative cost of the various query components. It does not provide a reliable measure of actual processing time.

## Use constant_keyword to speed up filtering
- There is a general rule that the cost of a filter is mostly a function of the number of matched documents. Imagine that you have an index containing cycles. There are a large number of bicycles and many searches perform a filter on cycle_type: bicycle. This very common filter is unfortunately also very costly since it matches most documents. There is a simple way to avoid running this filter: move bicycles to their own index and filter bicycles by searching this index instead of adding a filter to the query.
- Unfortunately this can make client-side logic tricky, which is where constant_keyword helps. By mapping cycle_type as a constant_keyword with value bicycle on the index that contains bicycles, clients can keep running the exact same queries as they used to run on the monolithic index and Elasticsearch will do the right thing on the bicycles index by ignoring filters on cycle_type if the value is bicycle and returning no hits otherwise.
- Here is what mappings could look like:
```
PUT bicycles
{
  "mappings": {
    "properties": {
      "cycle_type": {
        "type": "constant_keyword",
        "value": "bicycle"
      },
      "name": {
        "type": "text"
      }
    }
  }
}

PUT other_cycles
{
  "mappings": {
    "properties": {
      "cycle_type": {
        "type": "keyword"
      },
      "name": {
        "type": "text"
      }
    }
  }
}
```

- We are splitting our index in two: one that will contain only bicycles, and another one that contains other cycles: unicycles, tricycles, etc. Then at search time, we need to search both indices, but we don’t need to modify queries.

```
GET bicycles,other_cycles/_search
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "description": "dutch"
        }
      },
      "filter": {
        "term": {
          "cycle_type": "bicycle"
        }
      }
    }
  }
}
```

- On the bicycles index, Elasticsearch will simply ignore the cycle_type filter and rewrite the search request to the one below:

```
GET bicycles,other_cycles/_search
{
  "query": {
    "match": {
      "description": "dutch"
    }
  }
}
```
 - On the other_cycles index, Elasticsearch will quickly figure out that bicycle doesn’t exist in the terms dictionary of the cycle_type field and return a search response with no hits.
- This is a powerful way of making queries cheaper by putting common values in a dedicated index. This idea can also be combined across multiple fields: for instance if you track the color of each cycle and your bicycles index ends up having a majority of black bikes, you could split it into a bicycles-black and a bicycles-other-colors indices.
- The constant_keyword is not strictly required for this optimization: it is also possible to update the client-side logic in order to route queries to the relevant indices based on filters. However constant_keyword makes it transparently and allows to decouple search requests from the index topology in exchange of very little overhead.

## Deleted documents
- Having a large number of deleted documents in the Elasticsearch index also causes search performance issues, as explained in this official document. Force merge API can be used to remove a large number of deleted documents and optimize the shards.

## Search filters
- Effective use of filters in Elasticsearch queries can improve search performance dramatically as the filter clauses are 1) cached, and 2) able to reduce the target documents to be searched in the query clause.

## Wildcard queries
Avoid wildcard, which causes the entire Elasticsearch index to be scanned

## Multitude of small shards
- Having many small shards could cause a lot of network calls and threads, which severely impact search performance

## Multi search API
- Use msearch whenever possible. In most of the applications it’s required to query multiple Elasticsearch indices for a single transaction, and sometimes users do so in a serial order even when it’s not required
- In both cases, when you need to query multiple indices for the same transaction and when the result of these queries are independent, you should always use msearch to execute the queries in parallel in Elasticsearch.


## Term queries
- Use term query when you need an exact match

## Keep track of metrics
- This piece of advice is related to Elasticsearch monitoring
- To ensure efficient work of the site searcg, we need to understand the current performance level
- The Elasticsearch system unites multiple components, and each of them affects its overall performance

## Take care of the shard size
- The appropriate number of shards should be determined by the quantity of data in an index
- In general, an ideal shard should store 30-50GB of data. For example, if you plan to gather roughly 300GB of application logs, having around 10 shards in that index would be reasonable

## Elasticsearch data cannot be changed
- Take into account the fact that Elasticsearch data cannot be changed once it is stored. A document is then filed away in an index. However, if you need to make changes to the values in this document, you cannot just edit the values there. When this occurs, Elasticsearch generates a new document that includes the most up-to-date information.

## Reindexing the database
- Elasticsearch also uses version numbers to maintain tabs on the most up-to-date content. Therefore, the most recent version is always the one with the largest number. However, the index now contains both old and new documents, which causes the index size to grow. You can fix this by reindexing the database. Reindexing your data ensures that you are using the most up-to-date information while also reducing your data storage needs.

## Interval for refreshing
- Elasticsearch takes some time to index new data, so it can't be quickly accessed
- Setting the refresh interval to a higher number, such as 300 s (5 minutes), may assist in increasing the indexing speed.
- The standard refresh rate should be sufficient in most situations. However, you should consider what is most suitable for you. There is a cost associated with maintaining and regularly updating indexes. A daily refresh is sufficient if you don't want data in near-real-time; for instance, if you only deal with data from the previous day.

## Denormalize Documents

- Limit joins as much possible, nested queries can significantly lower down the search query performance
- Design your data model in a way that the data can be denormalized to great extent and hence eliminating the need of some unwanted joins.

## Index Performance Tuning
- Use Bulk Requests
- Use multiple workers/threads to send data to Elasticsearch
- Unset or increase the refresh interval
- Disable refresh and replicas for initial loads
- Disable swapping
- Use faster hardware
- Indexing buffer size ( Increase the size of the indexing buffer – JAVA Heap Size )

## Search Performance Tuning
- Give memory to the file-system cache
- Use faster hardware
- Document modeling (documents should not be joined)
- Search as few fields as possible
- Pre-index data (give values to your search)
- Shards might help with throughput, but not always
- Warm up the file-system cache (index.store.preload)


## file-system cache
- The file system cache holds data that was recently read from the disk, making it possible for subsequent requests to obtain data from cache rather than having to read it again from the disk.

## Search as few fields as possible
- The more fields a query_string or multi_match query targets, the slower it is
- Technique to improve search speed over multiple fields is to copy their values into a single field at index time, and then use this field at search time
- This can be automated with the copy-to directive
```
PUT movies
{
  "mappings": {
    "properties": {
      "name_and_plot": {
        "type": "text"
      },
      "name": {
        "type": "text",
        "copy_to": "name_and_plot"
      },
      "plot": {
        "type": "text",
        "copy_to": "name_and_plot"
      }
    }
  }
}
```


## Pre-index data
- You should leverage patterns in your queries to optimize the way data is indexed. 
```
PUT index
{
  "mappings": {
    "properties": {
      "price_range": {
        "type": "keyword"
      }
    }
  }
}

PUT index/_doc/1
{
  "designation": "spoon",
  "price": 13,
  "price_range": "10-100"
}
```

```
GET index/_search
{
  "aggs": {
    "price_ranges": {
      "terms": {
        "field": "price_range"
      }
    }
  }
}
```

## Avoid scriptsedit
- If possible, avoid using script-based sorting, scripts in aggregations, and the script_score query.


## Replicas might help with throughput, but not alwaysedit
- In addition to improving resiliency, replicas can help improve throughput. For instance if you have a single-shard index and three nodes, you will need to set the number of replicas to 2 in order to have 3 copies of your shard in total so that all nodes are utilized.

- Now imagine that you have a 2-shards index and two nodes. In one case, the number of replicas is 0, meaning that each node holds a single shard. In the second case the number of replicas is 1, meaning that each node has two shards. Which setup is going to perform best in terms of search performance? Usually, the setup that has fewer shards per node in total will perform better. The reason for that is that it gives a greater share of the available filesystem cache to each shard, and the filesystem cache is probably Elasticsearch’s number 1 performance factor. At the same time, beware that a setup that does not have replicas is subject to failure in case of a single node failure, so there is a trade-off between throughput and availability.

- So what is the right number of replicas? If you have a cluster that has num_nodes nodes, num_primaries primary shards in total and if you want to be able to cope with max_failures node failures at once at most, then the right number of replicas for you is max(max_failures, ceil(num_nodes / num_primaries) - 1).

## Tune your queries with the Search Profileredit
- The Profile API provides detailed information about how each component of your queries and aggregations impacts the time it takes to process the request.

- The Search Profiler in Kibana makes it easy to navigate and analyze the profile results and give you insight into how to tune your queries to improve performance and reduce load.

- Because the Profile API itself adds significant overhead to the query, this information is best used to understand the relative cost of the various query components. It does not provide a reliable measure of actual processing time.

## Use constant_keyword to speed up filtering
- There is a general rule that the cost of a filter is mostly a function of the number of matched documents. Imagine that you have an index containing cycles. There are a large number of bicycles and many searches perform a filter on cycle_type: bicycle. This very common filter is unfortunately also very costly since it matches most documents. There is a simple way to avoid running this filter: move bicycles to their own index and filter bicycles by searching this index instead of adding a filter to the query.
- Unfortunately this can make client-side logic tricky, which is where constant_keyword helps. By mapping cycle_type as a constant_keyword with value bicycle on the index that contains bicycles, clients can keep running the exact same queries as they used to run on the monolithic index and Elasticsearch will do the right thing on the bicycles index by ignoring filters on cycle_type if the value is bicycle and returning no hits otherwise.
- Here is what mappings could look like:
```
PUT bicycles
{
  "mappings": {
    "properties": {
      "cycle_type": {
        "type": "constant_keyword",
        "value": "bicycle"
      },
      "name": {
        "type": "text"
      }
    }
  }
}

PUT other_cycles
{
  "mappings": {
    "properties": {
      "cycle_type": {
        "type": "keyword"
      },
      "name": {
        "type": "text"
      }
    }
  }
}
```

- We are splitting our index in two: one that will contain only bicycles, and another one that contains other cycles: unicycles, tricycles, etc. Then at search time, we need to search both indices, but we don’t need to modify queries.

```
GET bicycles,other_cycles/_search
{
  "query": {
    "bool": {
      "must": {
        "match": {
          "description": "dutch"
        }
      },
      "filter": {
        "term": {
          "cycle_type": "bicycle"
        }
      }
    }
  }
}
```

- On the bicycles index, Elasticsearch will simply ignore the cycle_type filter and rewrite the search request to the one below:

```
GET bicycles,other_cycles/_search
{
  "query": {
    "match": {
      "description": "dutch"
    }
  }
}
```
 - On the other_cycles index, Elasticsearch will quickly figure out that bicycle doesn’t exist in the terms dictionary of the cycle_type field and return a search response with no hits.
- This is a powerful way of making queries cheaper by putting common values in a dedicated index. This idea can also be combined across multiple fields: for instance if you track the color of each cycle and your bicycles index ends up having a majority of black bikes, you could split it into a bicycles-black and a bicycles-other-colors indices.
- The constant_keyword is not strictly required for this optimization: it is also possible to update the client-side logic in order to route queries to the relevant indices based on filters. However constant_keyword makes it transparently and allows to decouple search requests from the index topology in exchange of very little overhead.

## Deleted documents
- Having a large number of deleted documents in the Elasticsearch index also causes search performance issues, as explained in this official document. Force merge API can be used to remove a large number of deleted documents and optimize the shards.

## Search filters
- Effective use of filters in Elasticsearch queries can improve search performance dramatically as the filter clauses are 1) cached, and 2) able to reduce the target documents to be searched in the query clause.

## Wildcard queries
Avoid wildcard, which causes the entire Elasticsearch index to be scanned

## Multitude of small shards
- Having many small shards could cause a lot of network calls and threads, which severely impact search performance

## Multi search API
- Use msearch whenever possible. In most of the applications it’s required to query multiple Elasticsearch indices for a single transaction, and sometimes users do so in a serial order even when it’s not required
- In both cases, when you need to query multiple indices for the same transaction and when the result of these queries are independent, you should always use msearch to execute the queries in parallel in Elasticsearch.


## Term queries
- Use term query when you need an exact match

## Keep track of metrics
- This piece of advice is related to Elasticsearch monitoring
- To ensure efficient work of the site searcg, we need to understand the current performance level
- The Elasticsearch system unites multiple components, and each of them affects its overall performance

## Take care of the shard size
- The appropriate number of shards should be determined by the quantity of data in an index
- In general, an ideal shard should store 30-50GB of data. For example, if you plan to gather roughly 300GB of application logs, having around 10 shards in that index would be reasonable

## Elasticsearch data cannot be changed
- Take into account the fact that Elasticsearch data cannot be changed once it is stored. A document is then filed away in an index. However, if you need to make changes to the values in this document, you cannot just edit the values there. When this occurs, Elasticsearch generates a new document that includes the most up-to-date information.

## Reindexing the database
- Elasticsearch also uses version numbers to maintain tabs on the most up-to-date content. Therefore, the most recent version is always the one with the largest number. However, the index now contains both old and new documents, which causes the index size to grow. You can fix this by reindexing the database. Reindexing your data ensures that you are using the most up-to-date information while also reducing your data storage needs.

## Interval for refreshing
- Elasticsearch takes some time to index new data, so it can't be quickly accessed
- Setting the refresh interval to a higher number, such as 300 s (5 minutes), may assist in increasing the indexing speed.
- The standard refresh rate should be sufficient in most situations. However, you should consider what is most suitable for you. There is a cost associated with maintaining and regularly updating indexes. A daily refresh is sufficient if you don't want data in near-real-time; for instance, if you only deal with data from the previous day.

## Denormalize Documents

- Limit joins as much possible, nested queries can significantly lower down the search query performance
- Design your data model in a way that the data can be denormalized to great extent and hence eliminating the need of some unwanted joins.

## Index Performance Tuning
- Use Bulk Requests
- Use multiple workers/threads to send data to Elasticsearch
- Unset or increase the refresh interval
- Disable refresh and replicas for initial loads
- Disable swapping
- Use faster hardware
- Indexing buffer size ( Increase the size of the indexing buffer – JAVA Heap Size )

## Search Performance Tuning
- Give memory to the file-system cache
- Use faster hardware
- Document modeling (documents should not be joined)
- Search as few fields as possible
- Pre-index data (give values to your search)
- Shards might help with throughput, but not always
- Warm up the file-system cache (index.store.preload)


## file-system cache
- The file system cache holds data that was recently read from the disk, making it possible for subsequent requests to obtain data from cache rather than having to read it again from the disk.

