# Encrypt the client network

## Configure client network encryption
### Master nodes
```
sudo su -
```

```
cat <<EOT >> /etc/elasticsearch/elasticsearch.yml
xpack.security.http.ssl.enabled: true
xpack.security.http.ssl.keystore.path: master-1
xpack.security.http.ssl.truststore.path: master-1
EOT
```

```
/usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.http.ssl.keystore.secure_password
##Password is elastic_master_1
```

```
/usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.http.ssl.truststore.secure_password
##Password is elastic_master_1
```


### Data nodes
#### Note: We need to change the data node name from data_1 to data_? as per the name of our data node
```
sudo su -
```

```
cat <<EOT >> /etc/elasticsearch/elasticsearch.yml
xpack.security.http.ssl.enabled: true
xpack.security.http.ssl.keystore.path: data-1
xpack.security.http.ssl.truststore.path: data-1
EOT
```


```
/usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.http.ssl.keystore.secure_password
##Password is elastic_data_1
```

```
/usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.http.ssl.truststore.secure_password
##Password is elastic_data_1
```

### On all nodes
```
sudo systemctl stop elasticsearch
sudo systemctl start elasticsearch
```

### Check Logs
```
tail -f /var/log/elasticsearch/cluster-1.log
```

### Configure kibana
```
cat <<EOT >> /etc/kibana/kibana.yml
elasticsearch.hosts: ["https://localhost:9200"]
elasticsearch.ssl.verificationMode: none
server.host: 0.0.0.0
EOT
```

cat /etc/kibana/kibana.yml

```
sudo systemctl stop kibana
sudo systemctl start kibana
```

```
sudo tail -f  /var/log/kibana/kibana.log
```

### Check Logs
```
tail -f /var/log/elasticsearch/cluster-1.log
```



## Open Kibana
```
http://elk-vm-master.eastus.cloudapp.azure.com:5601/login?next=%2F
```

## Credentials
```
elastic
elastic_566
```
