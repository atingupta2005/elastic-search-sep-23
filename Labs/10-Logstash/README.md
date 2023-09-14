# LogStash
- Logstash is a flexible Extract, Transform, Load (ETL) tool
- Logstash can act as the receiver for data from sources such as Beats agents or Syslog streams. It can also listen for data over HTTP for any compatible source system to send events through.
- Logstash can extract data from source systems such as Kafka
- Once data is input, Logstash can perform operations
- Logstash is designed to run as a standalone component to load data into Elasticsearch

## How it works:
### A Logstash instance is a running Logstash process
 - It is recommended to run Logstash on a separate host to Elasticsearch to ensure sufficient compute resources are available to the two components.

### A pipeline is a collection of plugins configured to process a given workload. 
 - A Logstash instance can run multiple pipelines (independent of each other).

### An input plugin is used to extract or receive data from a given source system
 - A list of supported input plugins is available on the Logstash reference guide
   - https://www.elastic.co/guide/en/logstash/current/input-plugins.html

### A filter plugin is used to apply transformations and enrichments to the incoming events
 - A list of supported filter plugins is available on the Logstash reference guide: 
   - https://www.elastic.co/guide/en/logstash/current/filterplugins.html

### An output plugin is used to load or send data to a given destination system
 - A list of supported output plugins is available on the Logstash reference guide
    - https://www.elastic.co/guide/en/logstash/current/output-plugins.html

- Logstash works by running one or more Logstash pipelines as part of a Logstash instance to process ETL workloads.

- Logstash is not a clustered component and cannot be made aware of other Logstash instances

- Settings and configuration options for Logstash are defined in the logstash.yml configuration file.

## Running your first pipeline
- Create a new logstash-pipeline.conf file

- Run Logstash from the command line:
```
echo -e 'Hello world!' | /usr/share/logstash/bin/logstash -f logstash-pipeline.conf
```

```
echo 'Hello world x1 \nHello world x2' | /usr/share/logstash/bin/logstash -f logstash-pipeline.conf
```

