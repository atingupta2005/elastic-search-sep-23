# Set the built-in user passwords on Master Node
```
sudo su
```

```
/usr/share/elasticsearch/bin/elasticsearch-setup-passwords interactive
```

## Use the following passwords:
```
User: elastic
Password: elastic_566
```

```
User: apm_system
Password: apm_system_566
```

```
User: kibana
Password: kibana_566
```

```
User: logstash_system
Password: logstash_system_566
```

```
User: beats_system
Password: beats_system_566
```

```
User: remote_monitoring_user
Password: remote_monitoring_user_566
```

```
cat <<EOT >> /etc/kibana/kibana.yml
elasticsearch.username: "kibana"
elasticsearch.password: "kibana_566"
EOT
```

```
cat /etc/kibana/kibana.yml
```

```
sudo systemctl stop elasticsearch
sudo systemctl start elasticsearch
sudo systemctl status elasticsearch
```

```
sudo systemctl stop kibana
sudo systemctl start kibana
sleep 30
sudo systemctl status kibana
```

## Check kibana logs
```
tail -f /var/log/kibana/kibana.log
```

## Test Password
```
curl --user kibana_system:kibana_566 -X GET "http://localhost:9200/?pretty"
```

## Open Kibana on Public IP of Master node:
```
http://elk-vm-master.eastus.cloudapp.azure.com:5601
```
