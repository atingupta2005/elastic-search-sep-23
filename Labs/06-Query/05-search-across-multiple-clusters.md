# Search Across Multiple Clusters
- It is common to have multiple Elasticsearch clusters whenever you’re dealing with several use cases that have different performance requirements
- To make it easier to interact with multiple clusters, you can leverage cross-cluster search

- In a web browser, navigate to the public IP address of the first node

## Setup:
- cluster-1: Having 2-3 nodes
- cluster-2 Having 2-3 nodes
- Cluster-3: Having 2-3 nodes

## Configure Cross-Cluster Search on the Primary Server
```
GET _cat/indices?v
```

- In the list of indices returned in the response pane, verify that the metricbeat-7.13.4 index is present, which is the dataset you will be querying.

### Add a persistent remote cluster setting for both the clusters:
- For first cluster, name it cluster_2, use the 10.0.0.7 IP address, and make sure to use the transport interface on port 9300
- For second cluster, name it cluster_3, use the 10.0.0.8 IP address, and make sure to use the transport interface on port 9300
```
PUT _cluster/settings
{
  "persistent": {
    "cluster": {
      "remote": {
        "cluster_2": {
          "seeds": ["10.0.0.7:9300"]
        },
        "cluster_3": {
          "seeds": ["10.0.0.8:9300"]
        }
      }
    }
  }
}
```

```
GET _cluster/settings
```


- The cluster settings should display in the response pane, indicating it was configured successfully.

## Execute the Cross-Cluster Search
### Query the metricbeat-7.13.4 dataset on cluster_2:
```
GET cluster_2:metricbeat-7.13.4/_search
```

- Scroll through the response pane and verify that the data returned is from the dataset on es2.

### Query the metricbeat-7.13.4 dataset on cluster_3:
```
GET cluster_3:metricbeat-7.13.4/_search
```

- Scroll through the response pane and verify that the data returned is from the dataset on cluster-3

## Execute the Cross-Cluster Search on All Clusters Simultaneously
```
GET metricbeat-7.13.4,cluster_2:metricbeat-7.13.4,cluster_3:metricbeat-7.13.4/_search
```

- Scroll through the response pane and verify that the data returned is from the datasets on both cluster-2 and cluster-3