# Install ElasticSearch, Logstash and Kibana

```
sudo apt update
```


```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```

```
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
```

```
sudo apt update
```

```
sudo apt install elasticsearch -y
```



# Run below commands only on master node
```
sudo apt install kibana -y
```


```
sudo systemctl enable kibana
sudo systemctl start kibana
```

```
sudo apt install logstash -y
```

```
sudo systemctl start logstash
sudo systemctl enable logstash
```