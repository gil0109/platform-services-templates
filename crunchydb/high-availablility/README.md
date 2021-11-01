# Sample HA CrunchyDB PostgresCluster

This is a sample `PostgresCluster` adapted from the example at <https://github.com/CrunchyData/postgres-operator-examples/tree/main/kustomize/high-availability>.

You can find a full tutorial and docs on CrunchyDB at <https://access.crunchydata.com/documentation/postgres-operator/5.0.0/tutorial/>.

In this example, my license plate is `be1c6b`.

`NetworkPolicy.yaml` has a sample NetworkPolicy for allowing the cluster pods to talk to each other, and the one needed by Monitoring for access.

`RoleBinding` has the Role and RoleBinding needed by the Monitoring stack.

`PostgresCluster` has the sample PostgresCluster with 3 replicas, backups, and monitoring.

## Monitoring Sidecar

The monitoring side car is added like this.

```yaml
spec:
  monitoring:
    pgmonitor:
      exporter:
        image: registry.developers.crunchydata.com/crunchydata/crunchy-postgres-exporter:ubi8-5.0.3-0
```

## Storage Class

Postgres works best on block storage.

```yaml
spec:
  instances:
    - name: pgha1
      dataVolumeClaimSpec:
        storageClassName: netapp-block-standard
```

## Backups

Keep 2 copies of full backups.

```yaml
spec:
  backups:
    pgbackrest:
      global:
        repo1-retention-full: "2"
```

Set the cron schedule for full and incremental backups.

```yaml
spec:
  backups:
    pgbackrest:
      repos:
      - name: repo1
        schedules:
          full: 0 1 * * *
          incremental: 0 */4 * * *
```

Set the backups to go to a PVC that is [backed up](https://developer.gov.bc.ca/OCP4-Backup-and-Restore).

```yaml
spec:
  backups:
    pgbackrest:
      repos:
      - name: repo1
        volume:
            storageClassName: netapp-file-backup
```
