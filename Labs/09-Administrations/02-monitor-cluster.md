# Monitor cluster
```
GET _cat/nodes?v
GET _cat/indices?v
```

### Open Monitoring dasboard
```
http://elk-vm-master.eastus.cloudapp.azure.com:5601/app/monitoring
```

### Enable  monitoring
```
PUT _cluster/settings
{
 "persistent": {
  "xpack.monitoring.collection.enabled": true
 }
}
```

### Open Monitoring dasboard again
```
http://elk-vm-master.eastus.cloudapp.azure.com:5601/app/monitoring
```


### Note that indices for monitoring are available now
```
GET _cat/indices?v
```
