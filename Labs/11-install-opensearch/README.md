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
https://artifacts.opensearch.org/releases/bundle/opensearch-dashboards/2.9.0/opensearch-dashboards-2.9.0-linux-x64.deb
sudo dpkg -i opensearch-dashboards-2.9.0-linux-x64.deb
sudo systemctl daemon-reload
sudo systemctl enable opensearch-dashboards
sudo systemctl start opensearch-dashboards
sudo systemctl status opensearch-dashboards
```

```
 echo "server.host: 0.0.0.0" >> sudo vi /etc/opensearch-dashboards/opensearch_dashboards.yml
```