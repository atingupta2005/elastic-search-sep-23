# Create PKI
## On master node
- Run below commands on linux terminal
```
sudo su
```

```
master_hostname=vm1
data_1_host_name=vm2
data_2_host_name=vm3
master_ip_address=10.0.0.6
data_1_ip_address=10.0.0.5
data_2_ip_address=10.0.0.7
```

```
/usr/share/elasticsearch/bin/elasticsearch-certutil ca --out /etc/elasticsearch/ca --pass elastic_ca
```

```
/usr/share/elasticsearch/bin/elasticsearch-certutil cert --ca /etc/elasticsearch/ca --ca-pass elastic_ca --name master-1 --dns $master_hostname --ip $master_ip_address --out /etc/elasticsearch/master-1 --pass elastic_master_1
```

```
/usr/share/elasticsearch/bin/elasticsearch-certutil cert --ca /etc/elasticsearch/ca --ca-pass elastic_ca --name data-1 --dns $data_1_host_name --ip $data_1_ip_address --out /etc/elasticsearch/data-1 --pass elastic_data_1
```

```
/usr/share/elasticsearch/bin/elasticsearch-certutil cert --ca /etc/elasticsearch/ca --ca-pass elastic_ca --name data-2 --dns $data_2_host_name --ip $data_2_ip_address --out /etc/elasticsearch/data-2 --pass elastic_data_2
```

```
chmod 640 /etc/elasticsearch/master-1
```

```
chmod 640 /etc/elasticsearch/data-*
```

```
ls -al /etc/elasticsearch/
```

## Copy the generated certificates on each node one by one
- First data node
```
scp /etc/elasticsearch/data-1 esuser@$data_1_ip_address:/tmp
```

- Second data node
```
scp /etc/elasticsearch/data-2 esuser@$data_2_ip_address:/tmp
```

### Node: Master node setup is now done

## Run below commands on each data node
```
sudo su
cp /tmp/data* /etc/elasticsearch/
ls -al /etc/elasticsearch/
```