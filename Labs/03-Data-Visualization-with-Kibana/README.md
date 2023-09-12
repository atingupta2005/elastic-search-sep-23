# Data Visualization with Kibana

## Add Sample data to Kibana
On the home page, click Try sample data.
Click Other sample data sets.
On the Sample eCommerce orders card, click Add data.


## Creating index patterns
 - Open the main menu, then click to Stack Management > Index Patterns.
 - Click Create index pattern.
 - Start typing in the Index pattern field, and Kibana looks for the names of indices, data streams, and aliases that match your input.
    - To match multiple sources, use a wildcard (*). For example, filebeat-* matches filebeat-apache-a, filebeat-apache-b, and so on.
    - To match multiple single sources, enter their names, separated with a comma. Do not include a space after the comma. filebeat-a,filebeat-b matches two indices, but not match filebeat-c.
    - To exclude a source, use a minus sign (-), for example, -test3.
 - If Kibana detects an index with a timestamp, expand the Timestamp field menu, and then select the default field for filtering your data by time.
    - If your index doesn’t have time-based data, choose I don’t want to use the time filter.
    - If you don’t set a default time field, you can’t use global time filters on your dashboards. This is useful if you have multiple time fields and want to create dashboards that combine visualizations based on different timestamps.
 - Click Create index pattern.
    - Kibana is now configured to use your Elasticsearch data. When a new field is added to an index, the index pattern field list is updated the next time the index pattern is loaded, for example, when you load the page or move between Kibana apps.
 - Select this index pattern when you search and visualize your data.


## Delete index patterns
 - When you delete an index pattern, you cannot recover the associated field formatters, runtime fields, source filters, and field popularity data. Deleting an index pattern does not remove any indices or data documents from Elasticsearch.

 1. Open the main menu, then click Stack Management > Index Patterns.
 1. Click the index pattern to delete.
 1. Delete (Delete icon) the index pattern.


## Kibana Query Language (KQL)
- Simple text-based query language for filtering data.
- Only filters data
- Allows users to get started quickly in the platform, and perform search operations in the web interface easily by using the search bar

### Boolean Operators
```
elasticsearch AND get
```

### Using AND with fields
```
machine.os.keyword : "win xp" AND response.keyword : "404"
```

### Using OR with field values
```
machine.os.keyword : "win xp" OR response.keyword : "404"
```

```
David and (Patrick or Stevie)
```

### Not
```
not location: "Blouse Barn"
```

### Searching by Field
```
name: Twyla
location: (“Bob’s Garage” or “Café Tropical”)
tags: (black and white and formal)
response.keyword : 404 200
```

### Ranges
```
@timestamp <= "2021-09-02"
```

### Wildcard(*)
- Used for unknown characters
```
mu*s
```

- The value of the room number field should exist
```
room_number:*
```



## Saving queries


## Kibana Dashboard
o get started, you’ll need to click “Dashboard” on the left sidebar, then click “Create new dashboard.” Next, you’ll be asked to Add Panels to your Kibana dashboard.
Any visualization you have previously created and saved in Kibana can be added as a Panel in your dashboard.


## Important Metrics to Monitor

