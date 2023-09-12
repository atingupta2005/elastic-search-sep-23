# Asynchronously Searching
- Occasionally, we need to ask data questions that may take some time to answer
- In these situations, it is beneficial to have the option of executing said query asynchronously

```
GET _cat/indices?v
```


```
POST /recipes/_async_search?wait_for_completion_timeout=0
{
  "size": 100,
  "query": {
  "match_all": { }
  }
}
```

###Note the id.
###Check the Status of the Asynchronous Search
###Note: If you did not get an async search ID, then make sure you set the wait_for_completion_timeout parameter to 0 when creating the async search.

### Use the async search ID to get the status of the request:
```
GET _async_search/status/FmVUZlJKOHZyU2Z1QzFsUXd1UW9qMFEcTGZuNU5CVUxUZUtabWdicEVraFg5Zzo0NTAwMg==
```

```
GET _cat/indices?v
```

###there is only 1 shard.


## Get the Results of the Asynchronous Search
### Use the async search ID to get the results of the request:
```
GET _async_search/FmVUZlJKOHZyU2Z1QzFsUXd1UW9qMFEcTGZuNU5CVUxUZUtabWdicEVraFg5Zzo0NTAwMg==
```

### Delete the Asynchronous Search
#### Use the async search ID to delete the request:
```
DELETE _async_search/FmVUZlJKOHZyU2Z1QzFsUXd1UW9qMFEcTGZuNU5CVUxUZUtabWdicEVraFg5Zzo0NTAwMg==
```

```
GET _async_search/status/FmVUZlJKOHZyU2Z1QzFsUXd1UW9qMFEcTGZuNU5CVUxUZUtabWdicEVraFg5Zzo0NTAwMg==
```

###Confirm the request was deleted. You should see error