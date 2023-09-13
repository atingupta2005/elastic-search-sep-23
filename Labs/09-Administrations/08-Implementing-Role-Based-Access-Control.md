# Implementing Role-Based Access Control
- Data replication is the answer to a lot of potential problems in data engineering

```
GET  _cluster/settings
```

```
GET _cat/indices?v
```

- All indices should be returned in the response pane with green health and open status.

## Configure the es1 cluster as a remote cluster with the name accounts_leader, the private IP address of the es009 node and the inter-node communication port:
```
PUT _cluster/settings
{
  "persistent": {
    "cluster": {
      "remote": {
        "accounts_leader": {
          "seeds": "vm-es009.eastus2.cloudapp.azure.com:9300"
        }
      }
    }
  }
}
```

- The accounts_leader remote cluster should display in the response pane, indicating it was configured successfuly.

## Create a follower index with the name accounts-1, which follows the accounts-1 leader index on the accounts_leader remote cluster that was just configured:
```
PUT leads_b2b-000001/_ccr/follow
{
  "remote_cluster": "accounts_leader",
  "leader_index": "leads_b2b-000001"
}
```

```
GET _cat/indices?v
```

- Verify that the accounts-1 index now appears in the list of active indices in the response pane.


## Configure an auto-follow pattern with the name accounts, which follows any future indices that are created on the leader cluster (i.e., accounts-*):
```
PUT _ccr/auto_follow/accounts
{
  "remote_cluster": "accounts_leader",
  "leader_index_patterns": ["accounts-*"]
}
```