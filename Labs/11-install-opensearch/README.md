# Install Opensearch
```
sudo apt update
```


```
wget https://artifacts.opensearch.org/releases/bundle/opensearch/2.9.0/opensearch-2.9.0-linux-x64.deb
```

```
sudo dpkg -i opensearch-2.9.0-linux-x64.deb
```

```
sudo systemctl enable opensearch
```

```
sudo systemctl start opensearch
```

```
sudo systemctl status opensearch
```

```
curl -X GET https://localhost:9200 -u 'admin:admin' --insecure
```

```
seed_hosts=$(ip -4 addr show eth0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}')
```

```
export seed_hosts
```

```
echo $seed_hosts
```

```
cat << EOF | sudo tee -a /etc/opensearch/opensearch.yml
cluster.name: cluster-1
network.host: [_local_, _site_]
discovery.seed_hosts: ["$seed_hosts"]
cluster.initial_master_nodes: ["master-1"]
EOF
```

```
cat /etc/opensearch/opensearch.yml
```

```
sudo sed -i 's/Xms1g/Xms4g/' /etc/opensearch/jvm.options
```


```
sudo sed -i 's/Xmx1g/Xmx4g/' /etc/opensearch/jvm.options
```


```
cat /etc/opensearch/jvm.options | grep Xm
```

```
sudo systemctl stop opensearch
sudo systemctl start opensearch
sudo systemctl status opensearch
```


```
curl -X GET https://localhost:9200 -u 'admin:admin' --insecure
```

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-8.9.2-amd64.deb
shasum -a 512 kibana-8.9.2-amd64.deb 
sudo dpkg -i kibana-8.9.2-amd64.deb
```


```
cat << EOF | sudo tee -a /etc/kibana/kibana.yml
elasticsearch.username: "admin"
elasticsearch.password: "admin"
server.host: 0.0.0.0
EOF
```


```
cat << EOF | sudo tee -a /etc/kibana/kibana.yml
elasticsearch.hosts: ["https://localhost:9200"]
elasticsearch.ssl.verificationMode: none
EOF
```


```
cat /etc/kibana/kibana.yml
```

```
sudo systemctl enable kibana
sudo systemctl start kibana
```
