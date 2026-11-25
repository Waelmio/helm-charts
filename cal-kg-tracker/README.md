# cal-kg-tracker Helm Chart

Helm chart for the [cal-kg-tracker](https://github.com/Waelmio/cal-kg-tracker) weight loss tracker application.

# Install

```sh
helm repo add waelmio https://waelmio.github.io/helm-charts
helm install my-cal-kg-tracker waelmio/cal-kg-tracker
```

The default values work as they are: SQLite on a 1Gi volume.

# Upgrading to 1.3.2

- Back up your database: the app migrates its data model at startup.
- `database.sqlite.persistence` is now `persistence`. Move your values there to keep using the same volume.
- Postgres installations now get a volume too, for the keys that encrypt integration tokens. Set `persistence.enabled: false` if you use no integration.

# Values

## `persistence`

Volume for all the data of the app, mounted at `/data`: the SQLite database and the keys that encrypt integration tokens (`/data/keys`). Used with both databases.

| Value | Default | Description |
|-------|---------|-------------|
| `persistence.enabled` | `true` | Whether to use a PersistentVolumeClaim. Otherwise the data is lost with the pod. Required by the integrations, unless they set `allowWithoutPersistence`. |
| `persistence.storageClass` | `""` | StorageClass for the PVC. Empty uses the cluster default. |
| `persistence.existingClaim` | `""` | Name of an existing PVC to use instead of creating one. |
| `persistence.accessMode` | `ReadWriteOnce` | PVC access mode. |
| `persistence.size` | `1Gi` | Requested storage size. |

## `database`

| Value | Default | Description |
|-------|---------|-------------|
| `database.type` | `sqlite` | Database backend. Available values: `sqlite` or `postgres`. |

### SQLite (`database.sqlite`)

| Value | Default | Description |
|-------|---------|-------------|
| `database.sqlite.dbPath` | `/data/weighttracker.db` | Path inside the container where the SQLite database file is stored. Must be under `/data` to be persisted. |

### PostgreSQL (`database.postgres`)

Only used when `database.type` is `postgres`. Either supply a full `connectionUrl`, or fill in the individual fields.

| Value | Default | Description |
|-------|---------|-------------|
| `database.postgres.connectionUrl` | `""` | Full PostgreSQL DSN. When set, all individual fields below are ignored. |
| `database.postgres.host` | `""` | PostgreSQL server hostname or IP. |
| `database.postgres.db` | `weighttracker` | Database name. |
| `database.postgres.user` | `postgres` | Database username (plaintext). |
| `database.postgres.existingUserSecret` | `""` | Name of an existing Secret to use for the username instead of `user`. |
| `database.postgres.existingUserSecretKey` | `username` | Key within the existing Secret that holds the username. |
| `database.postgres.password` | `""` | Database password (plaintext). |
| `database.postgres.existingPasswordSecret` | `""` | Name of an existing Secret to use for the password instead of `password`. |
| `database.postgres.existingPasswordSecretKey` | `password` | Key within the existing Secret that holds the password. |

## General

| Value | Default | Description |
|-------|---------|-------------|
| `strategy` | `RollingUpdate`, `maxSurge: 0` | Deployment strategy. The default stops the old pod before starting the new one, as the volume is `ReadWriteOnce`. |
| `publicBaseUrl` | `""` | Public address of the app, where FatSecret sends the browser back after login. Empty uses the address the browser is using. |

## `integrations`

Integrations require `persistence.enabled: true`; the deployment fails otherwise, unless the integration sets `allowWithoutPersistence: true`.

### FatSecret (`integrations.fatsecret`)

Optional. Uses the **OAuth 1.0** Consumer Key and Consumer Secret from [platform.fatsecret.com](https://platform.fatsecret.com), not the OAuth 2.0 ones. Both are needed. Once deployed, connect in Settings > Integrations.

| Value | Default | Description |
|-------|---------|-------------|
| `integrations.fatsecret.consumerKey` | `""` | Consumer Key (plaintext). |
| `integrations.fatsecret.consumerSecret` | `""` | Consumer Secret (plaintext). |
| `integrations.fatsecret.existingSecret` | `""` | Name of an existing Secret to read both from instead of the plaintext values. |
| `integrations.fatsecret.existingSecretConsumerKeyKey` | `consumer-key` | Key within the existing Secret that holds the Consumer Key. |
| `integrations.fatsecret.existingSecretConsumerSecretKey` | `consumer-secret` | Key within the existing Secret that holds the Consumer Secret. |
| `integrations.fatsecret.allowWithoutPersistence` | `false` | Deploy even when `persistence.enabled` is `false`. FatSecret must then be connected again each time the pod is replaced. |

Example:

```sh
kubectl create secret generic fatsecret \
  --from-literal=consumer-key=... \
  --from-literal=consumer-secret=...
```

```yaml
integrations:
  fatsecret:
    existingSecret: fatsecret
```
