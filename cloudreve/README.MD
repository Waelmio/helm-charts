# Cloudreve Helm Chart

Not finding an official Helm Chart for CloudReve, I created one myself.
This is NOT an official Helm Chart for CloudReve.

# Values

## `cloudreve.system`

| Value | Default | Description |
|-------|---------|-------------|
| `cloudreve.system.mode` | `master` | Running mode. Available values: `master` or `slave`. |
| `cloudreve.system.listen` | `5212` | HTTP listening port. |
| `cloudreve.system.debug` | `false` | Enable debug mode. |
| `cloudreve.system.proxyHeader` | `""` | Header used to extract the real client IP behind a reverse proxy (e.g. `X-Forwarded-For`). Only set this when the proxy is trusted. |
| `cloudreve.system.gracePeriod` | `0` | Maximum buffer time (seconds) for a safe process exit. `0` means no limit. |
| `cloudreve.system.logLevel` | `info` | Log verbosity. Available values: `debug`, `info`, `warning`, `error`. |

## `cloudreve.database`

This chart does not deploy a database. Bring your own for anything other than SQLite.

| Value | Default | Description |
|-------|---------|-------------|
| `cloudreve.database.type` | `sqlite` | Database backend. Available values: `sqlite`, `mysql`, `postgres`, `mariadb`. |
| `cloudreve.database.host` | `127.0.0.1` | Database server address. |
| `cloudreve.database.port` | `3306` | Database server port. |
| `cloudreve.database.name` | `cloudreve` | Database name. |
| `cloudreve.database.user` | `""` | Database username. |
| `cloudreve.database.password` | `""` | Database password (plaintext). |
| `cloudreve.database.existingSecret` | `""` | Name of an existing Secret to use for the database password instead of `password`. |
| `cloudreve.database.existingSecretName` | `password` | Key within the existing Secret that holds the password. |
| `cloudreve.database.charset` | `utf8mb4` | Connection character set (MySQL/MariaDB). |
| `cloudreve.database.dbFile` | `cloudreve.db` | SQLite database file path (relative to the data directory). Only used when `type` is `sqlite`. |
| `cloudreve.database.databaseURL` | `""` | Full database connection string (DSN). When set, all individual fields above are ignored (except `type`). |

## `cloudreve.cors`

| Value | Default | Description |
|-------|---------|-------------|
| `cloudreve.cors.allowOrigins` | `*` | Allowed CORS origins. |
| `cloudreve.cors.allowMethods` | `OPTIONS,GET,POST` | Allowed CORS methods. |
| `cloudreve.cors.allowHeaders` | `*` | Allowed CORS headers. |
| `cloudreve.cors.allowCredentials` | `false` | Whether to allow credentials in CORS requests. |

## `cloudreve.redis`

This chart does not deploy Redis. Bring your own and point `server` at it.

| Value | Default | Description |
|-------|---------|-------------|
| `cloudreve.redis.server` | `""` | Redis server address (e.g. `127.0.0.1:6379`). Leave empty to disable Redis. |
| `cloudreve.redis.network` | `tcp` | Connection type (`tcp` or `unix`). |
| `cloudreve.redis.user` | `""` | Redis username (ACL). |
| `cloudreve.redis.password` | `""` | Redis password. |
| `cloudreve.redis.db` | `0` | Redis logical database index. |
| `cloudreve.redis.useTLS` | `false` | Connect to Redis over TLS. |
| `cloudreve.redis.tlsSkipVerify` | `false` | Skip TLS certificate verification. |

## `cloudreve.optionOverwrite`

Free-form map that writes directly into the Cloudreve `[OptionOverwrite]` config section. Use `setting_name: value` pairs to override any Cloudreve database-stored option at startup.

Example:
```yaml
cloudreve:
  optionOverwrite:
    queue_slave_worker_num: 50
```

## `persistence`

Persistent storage for the Cloudreve data directory (uploads, SQLite DB, etc.).

| Value | Default | Description |
|-------|---------|-------------|
| `persistence.enabled` | `true` | Whether to create and mount a PersistentVolumeClaim. |
| `persistence.storageClass` | `""` | StorageClass for the PVC. Empty uses the cluster default. |
| `persistence.existingClaim` | `""` | Name of an existing PVC to use instead of creating one. |
| `persistence.accessMode` | `ReadWriteOnce` | PVC access mode. |
| `persistence.size` | `50Gi` | Requested storage size. |
| `persistence.additionalVolumes` | `[]` | Extra volumes to add to the Deployment (standard Kubernetes volume spec). |
| `persistence.additionalMounts` | `[]` | Extra volumeMounts to add to the container (standard Kubernetes volumeMount spec). |

## `httpRoute`

Exposes the service via a Gateway API `HTTPRoute` resource instead of (or alongside) a classic `Ingress`.
Requires the Gateway API CRDs and a compatible controller to be installed in the cluster.

| Value | Default | Description |
|-------|---------|-------------|
| `httpRoute.enabled` | `false` | Whether to create an HTTPRoute resource. |
| `httpRoute.annotations` | `{}` | Annotations added to the HTTPRoute. |
| `httpRoute.parentRefs` | see values.yaml | Gateways this route attaches to. |
| `httpRoute.hostnames` | `[chart-example.local]` | Hostnames matched by this route. |
| `httpRoute.rules` | see values.yaml | List of routing rules and filters applied to matching requests. |