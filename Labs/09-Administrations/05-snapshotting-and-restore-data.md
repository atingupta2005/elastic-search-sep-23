# Snapshotting Data
- You work as a data infrastructure engineer for an online bank who uses Elasticsearch as a NoSQL database
- You've been tasked with demonstrating the capability and data integrity of backing up and restoring the accounts index as part of an audit process
- For this, you will need to create a new filesystem-type snapshot repository called accounts and an accounts_test_backup snapshot of the accounts index, including the global cluster state
- Once the accounts_test_backup snapshot is created, you need to restore the accounts index as the index accounts_test_restore
- Lastly, you'll need to verify data integrity by comparing the accounts_test_restore index to the accounts index from the accounts_test_backup snapshot.

```
GET _cat/indices?v
```

## Create the accounts snapshot repository:
```
PUT _snapshot/accounts
{
  "type": "fs",
  "settings": {
    "location": "/mnt/backups/accounts"
  }
}
```

## Create the accounts_test_backup Snapshot of the accounts Index
### Create the snapshot and include the global state:
```
PUT _snapshot/accounts/accounts_test_backup
{
  "indices": "accounts",
  "include_global_state": true
}
```

### Verify the snapshot was created:
```
GET _snapshot/accounts/accounts_test_backup
```

## Restore the accounts Index from the accounts_test_backup Snapshot
### Restore the index:
```
POST _snapshot/accounts/accounts_test_backup/_restore
{
  "indices": "accounts",
  "rename_pattern": "(.+)",
  "rename_replacement": "$1_test_restore"
}
```

## Mount the accounts Index from the accounts_test_backup Snapshot
### To make the accounts index a searchable snapshot, mount the index as accounts_text_backup:
```
POST _snapshot/accounts/accounts_test_backup/_mount
{
  "index": "accounts",
  "renamed_index": "accounts_test_backup"
}
```