# Configure and create a cluster
## Important Node:
 - Please make sure that the master node should be up and running before we start the data nodes

## On Each node
```
ip addr show
sudo su
sudo apt install iputils-ping
ping <ip-address-of-other-vms>
```

## On each node, change IP Address on below line 
```
seed_hosts=10.0.0.4   # Should be private ip of master node
```

```
cat /etc/elasticsearch/elasticsearch.yml
cat <<EOT >> /etc/elasticsearch/elasticsearch.yml
cluster.name: cluster-1
network.host: [_local_, _site_]
discovery.seed_hosts: ["$seed_hosts"]
cluster.initial_master_nodes: ["master-1"]
EOT
cat /etc/elasticsearch/elasticsearch.yml
exit
```

## On Master Node
```
sudo su
```


```
node_name=master-1
cat <<EOT >> /etc/elasticsearch/elasticsearch.yml
node.name: $node_name
node.master: true
node.data: false
node.ingest: false
node.ml: false
EOT
```

```
cat /etc/elasticsearch/elasticsearch.yml
```

```
sudo cat /etc/elasticsearch/jvm.options | grep Xms4g
sudo cat /etc/elasticsearch/jvm.options | grep Xmx4g
sudo sed -i 's/Xms4g/Xms3g/' /etc/elasticsearch/jvm.options
sudo sed -i 's/Xmx4g/Xmx3g/' /etc/elasticsearch/jvm.options
sudo cat /etc/elasticsearch/jvm.options | grep Xms3g
sudo cat /etc/elasticsearch/jvm.options | grep Xmx3g
```

## Start the elasticsearch process on Master node
- Note: We need to make sure that we first start ElasticSearch on master node
```
sudo systemctl enable elasticsearch
sudo systemctl stop elasticsearch
sudo systemctl start elasticsearch
curl -X GET "localhost:9200"
```

### Check the node configuration:
```
curl localhost:9200/_cat/nodes?v
```

### Sample output of the above command
```
10.0.0.6  54 34  0 0.00 0.01 0.01 mr        * master-2
```

## Important Node:
 - Please make sure that the master node should be up and running before we start the data nodes
 - Run below commands only when the master node is setup correctly



## On Data Node:
```
sudo su
```

```
node_name=data-1
```

```
cat <<EOT >> /etc/elasticsearch/elasticsearch.yml
node.name: $node_name
node.attr.temp: hot
node.master: false
node.data: true
node.ingest: true
node.ml: false
EOT
```

```
sudo sed 's/Xms1g/Xms2g/' /etc/elasticsearch/jvm.options
sudo sed 's/Xmx1g/Xmx2g/' /etc/elasticsearch/jvm.options
```

## On Data Node:
```
node_name=data-2


```
cat <<EOT >> /etc/elasticsearch/elasticsearch.yml
node.name: $node_name
node.attr.temp: warm
node.master: false
node.data: true
node.ingest: true
node.ml: false
EOT
```

```
sudo sed 's/Xms1g/Xms2g/' /etc/elasticsearch/jvm.options
sudo sed 's/Xmx1g/Xmx2g/' /etc/elasticsearch/jvm.options
```

```
sudo systemctl enable elasticsearch
sudo systemctl stop elasticsearch
sudo systemctl start elasticsearch
curl -X GET "localhost:9200"
```

### Check the startup process:
```
sudo tail -f /var/log/elasticsearch/cluster-1.log
```


### Check the node configuration:
```
curl localhost:9200/_cat/nodes?v
```
