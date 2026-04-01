# cal-kg-tracker Helm Chart

Helm chart for the [cal-kg-tracker](https://github.com/Waelmio/cal-kg-tracker) weight loss tracker application.

# Values

## `database`

| Value | Default | Description |
|-------|---------|-------------|
| `database.type` | `sqlite` | Database backend. Available values: `sqlite` or `postgres`. |

### SQLite (`database.sqlite`)

| Value | Default | Description |
|-------|---------|-------------|
| `database.sqlite.dbPath` | `/data/weighttracker.db` | Path inside the container where the SQLite database file is stored. |
| `database.sqlite.persistence.enabled` | `true` | Whether to create and mount a PersistentVolumeClaim for the SQLite file. |
| `database.sqlite.persistence.storageClass` | `""` | StorageClass for the PVC. Empty uses the cluster default. |
| `database.sqlite.persistence.existingClaim` | `""` | Name of an existing PVC to use instead of creating one. |
| `database.sqlite.persistence.accessMode` | `ReadWriteOnce` | PVC access mode. |
| `database.sqlite.persistence.size` | `1Gi` | Requested storage size. |

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
