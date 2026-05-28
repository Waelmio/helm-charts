# Technitium Helm Chart

Helm chart for [Technitium DNS Server](https://technitium.com/dns/).

# Install

```sh
helm repo add waelmio https://waelmio.github.io/helm-charts
helm install my-technitium waelmio/technitium
```

# Values

## Credentials

| Value | Default | Description |
|-------|---------|-------------|
| `adminUsername.value` | `admin` | Admin username (plaintext). |
| `adminUsername.existingSecret` | `""` | Name of an existing Secret holding the username. |
| `adminUsername.existingSecretKey` | `username` | Key within the Secret. |
| `adminPassword.value` | `""` | Admin password (plaintext). |
| `adminPassword.existingSecret` | `""` | Name of an existing Secret holding the password. |
| `adminPassword.existingSecretKey` | `password` | Key within the Secret. |

## `dnsService`

LoadBalancer service for DNS traffic (port 53 TCP + UDP).

| Value | Default | Description |
|-------|---------|-------------|
| `dnsService.type` | `LoadBalancer` | Service type. |
| `dnsService.loadBalancerIP` | `""` | Static IP to request from the load balancer. |
| `dnsService.annotations` | `{}` | Annotations added to the service. |

## `adminService`

ClusterIP service for the web admin UI.

| Value | Default | Description |
|-------|---------|-------------|
| `adminService.type` | `ClusterIP` | Service type. |
| `adminService.port` | `5380` | Port exposed by the service. |

## `persistence`

| Value | Default | Description |
|-------|---------|-------------|
| `persistence.enabled` | `true` | Whether to create and mount a PersistentVolumeClaim. |
| `persistence.storageClass` | `""` | StorageClass for the PVC. Empty uses the cluster default. |
| `persistence.existingClaim` | `""` | Name of an existing PVC to use instead of creating one. |
| `persistence.accessMode` | `ReadWriteOnce` | PVC access mode. |
| `persistence.size` | `2Gi` | Requested storage size. |

## `configurator`

Optional post-install/post-upgrade Job that applies Technitium config via [technitium-configurator](https://github.com/ashtonian/technitium-configurator).

| Value | Default | Description |
|-------|---------|-------------|
| `configurator.enabled` | `false` | Whether to run the configurator Job after each deploy. |
| `configurator.image.repository` | `ashtonian/technitium-configurator` | Configurator image. |
| `configurator.image.tag` | `latest` | Configurator image tag. |
| `configurator.config` | `{}` | Raw technitium-configurator YAML config (see upstream docs). |

## Other

| Value | Default | Description |
|-------|---------|-------------|
| `timezone` | `UTC` | TZ identifier set in the container environment. |
| `replicaCount` | `1` | Number of pod replicas. |
| `image.repository` | `technitium/dns-server` | Container image. |
| `image.tag` | `""` | Image tag. Defaults to the chart `appVersion`. |
| `ingress.enabled` | `false` | Whether to create an Ingress for the admin UI. |
