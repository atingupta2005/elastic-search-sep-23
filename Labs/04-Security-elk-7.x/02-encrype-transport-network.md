# Encrypt the Elasticsearch Transport Network
- Native security configurations and tools to ensure that data is only accessible to authorized users
  - Encrypt the transport network of an Elasticsearch cluster
  - Enable user authentication in Elasticsearch
  - Set built-in Elasticsearch user passwords

## Configure transport network encryption
### Master nodes
```
sudo su -
cat <<EOT >> /etc/elasticsearch/elasticsearch.yml
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: full
xpack.security.transport.ssl.keystore.path: master-1
xpack.security.transport.ssl.truststore.path: master-1
EOT
```

```
cat /etc/elasticsearch/elasticsearch.yml
```

```
/usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password
##Password is elastic_master_1
```

```
/usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password
##Password is elastic_master_1
```

```
sudo systemctl stop elasticsearch
sudo systemctl start elasticsearch
```

### Data nodes
```
sudo su -
cat <<EOT >> /etc/elasticsearch/elasticsearch.yml
xpack.security.enabled: true
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.verification_mode: full
xpack.security.transport.ssl.keystore.path: data-1
xpack.security.transport.ssl.truststore.path: data-1
EOT
```


```
cat /etc/elasticsearch/elasticsearch.yml
```

```
/usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.transport.ssl.keystore.secure_password
##Password is elastic_data_1
```

```
/usr/share/elasticsearch/bin/elasticsearch-keystore add xpack.security.transport.ssl.truststore.secure_password
##Password is elastic_data_1
```

```
sudo systemctl stop elasticsearch
sudo systemctl start elasticsearch
```

