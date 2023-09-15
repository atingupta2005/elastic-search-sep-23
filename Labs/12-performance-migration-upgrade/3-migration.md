# Migrating data

## Reindex from a remote cluster
```
reindex.remote.whitelist: [81693ca13302469c8cbca193625c941c.us-east-1.aws.found.io:9200]
```

```
POST _reindex
{
  "source": {
    "remote": {
      "host": "https://REMOTE_ELASTICSEARCH_ENDPOINT:PORT",
      "username": "USER",
      "password": "PASSWORD"
    },
    "index": "INDEX_NAME",
    "query": {
      "match_all": {}
    }
  },
  "dest": {
    "index": "INDEX_NAME"
  }
}
```

```
GET INDEX-NAME/_search?pretty
```

## Restore from a snapshot
GET /_snapshot
GET /_snapshot/_all

GET /_snapshot/NEW-REPOSITORY-NAME/_all


From the Elasticsearch Service Console of the new Elasticsearch cluster, add the snapshot repository.

Open Kibana and go to Management > Snapshot and Restore


- reindex.ssl.certificate_authorities
