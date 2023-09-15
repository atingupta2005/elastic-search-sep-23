# Upgrade
## Prepare to upgrade from 7.x
- To upgrade to 8.10.0 from 7.16 or earlier, you must first upgrade to 7.17. This enables you to use the Upgrade Assistant to identify and resolve issues, reindex indices created before 7.0, and then perform a rolling upgrade

- Upgrading to 7.17 before upgrading to 8.10.0 is required even if you opt to do a full-cluster restart of your Elasticsearch cluster. Alternatively, you can create a new 8.10.0 deployment and reindex from remote

## Remote cluster compatibility
If you use cross-cluster search, note that 8.10.0 can only search remote clusters running the previous minor version or later.


- Use the Upgrade Assistant to prepare for your upgrade from 7.17 to 8.10.0.


- The Upgrade Assistant identifies deprecated settings and guides you through resolving issues and reindexing indices created before 7.0. Make sure you have a current snapshot before making configuration changes or reindexing.

- Review the deprecation logs from the Upgrade Assistant to determine if your applications are using features that are not supported or behave differently in 8.x.

- If you use any Elasticsearch plugins, make sure there is a version of each plugin that is compatible with Elasticsearch version 8.10.0.

- Test the upgrade in an isolated environment before upgrading your production cluster.
- Make sure you have a current snapshot before you start the upgrade.



- Upgrade nodes that are NOT master-eligible first. 

- This order ensures that all nodes can join the cluster during the upgrade. Upgraded nodes can join a cluster with an older master, but older nodes cannot always join a cluster with a upgraded master.

## To upgrade a cluster:
### Disable shard allocation
```
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "primaries"
  }
}
```

### Shut down a single node

### Upgrade the node you shut down

### Upgrade any plugins.
- Use the elasticsearch-plugin script to install the upgraded version of each installed Elasticsearch plugin
- All plugins must be upgraded when you upgrade a node

### Start the upgraded node

### Reenable shard allocation

```
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": null
  }
}
```

### Wait for the node to recover
```
GET _cat/health?v=true
```

### Repeat
- When the node has recovered and the cluster is stable, repeat these steps for each node that needs to be updated. You can monitor the health of the cluster with a _cat/health request:
```
GET /_cat/health?v=true
```

- And check which nodes have been upgraded with a _cat/nodes request

# Use the Kibana Upgrade Assistant
- The Kibana Upgrade Assistant is very helpful
- Check the deprecation log
```
/var/log/elasticsearch/Your-Cluster-Name_deprecation.log
```
- Review the breaking changes
- Check the Elasticsearch plugins’ compatibility
- 