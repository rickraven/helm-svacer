# Helm Chart for Svacer

[![Artifact Hub](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/helm-svacer)](https://artifacthub.io/packages/search?repo=helm-svacer)

## Introduction

This [Helm](https://github.com/kubernetes/helm) chart installs [Svacer](https://svacer.ispras.ru/mediawiki/index.php?title=Svacer) in a Kubernetes cluster.

Svacer - server for storing and processing static analysis results. Supports import of analysis results from [Svace](https://svace.ispras.ru/) directly and from other analyzers via the format [SARIF](https://svacer.ispras.ru/mediawiki/index.php?title=Help:Sarif)

## Prerequisites

- Kubernetes cluster 1.10+
- Helm 3.0.0+
- PV provisioner support in the underlying infrastructure.

## Installation

### Add Helm repository

```bash
helm repo add rickraven https://rickraven.github.io/helm-svacer
helm repo update
```

### Configure the chart

The following items can be set via `--set` flag during installation or configured by editing the `values.yaml` directly (need to download the chart first).

#### Configure the way how to expose service:

- **Ingress**: The ingress controller must be installed in the Kubernetes cluster.
- **ClusterIP**: Exposes the service on a cluster-internal IP. Choosing this value makes the service only reachable from within the cluster.
- **NodePort**: Exposes the service on each Node’s IP at a static port (the NodePort). You’ll be able to contact the NodePort service, from outside the cluster, by requesting `NodeIP:NodePort`.
- **LoadBalancer**: Exposes the service externally using a cloud provider’s load balancer.

### Install the chart

Install the Adminer helm chart with a release name `my-release`:

```bash
helm upgrade --install my-release rickraven/svacer
```

## Uninstallation

To uninstall/delete the `my-release` deployment:

```bash
helm delete my-release
```

## Configuration

The following table lists the configurable parameters of the svacer chart and the default values.

### Deployment spec

| Parameter | Description | Default values |
| --- | --- | --- |
| `svacer.podLables` | Extra svacer pod lables | `{}` |
| `svacer.podAnnotations` | Extra svacer pod annotations | `{}` |
| `svacer.terminationGracePeriod` | Timeout for normal shutdown in seconds | `30` |
| `svacer.image.registry` |  | `docker.io` |
| `svacer.image.repository` |  | `ispras/svacer` |
| `svacer.image.tag` |  | `9-0-2` |
| `svacer.image.pullPolicy` |  | `IfNotPresent` |
| `svacer.resources` | CPU/Memory resource requests/limits | `{}` |
| `svacer.podSecurityContext.enabled` | Enable podSecurityContext for svacer pod | `false` |
| `svacer.podSecurityContext.runAsUser` | Run svacer process in container as user | `65534` |
| `svacer.podSecurityContext.runAsGroup` | Run svacer process in container as group | `65534` |
| `svacer.podSecurityContext.fsGroup` | Run svacer process in container with filesystem group | `65534` |
| `svacer.podSecurityContext.runAsNonRoot` | Run svacer process in container as non root user | `true` |
| `svacer.serviceWeb.enabled` | Create service for web interface | `true` |
| `svacer.serviceWeb.port` | Run web interface on port | `8080` |
| `svacer.serviceWeb.protocol` |  | `TCP` |
| `svacer.serviceWeb.type` | Set web interface service type | `ClusterIP` |
| `svacer.serviceWeb.loadBalancerIP` | Set external ip address for web interface service. Works only with `svacer.serviceWeb.type: LoadBalancer` | `` |
| `svacer.serviceGrpc.enabled` | Create service for snapshots uploading over grpc | `true` |
| `svacer.serviceGrpc.port` | Run snapshots uploading service on port | `8080` |
| `svacer.serviceGrpc.protocol` |  | `TCP` |
| `svacer.serviceGrpc.type` | Set snapshots uploading service service type | `ClusterIP` |
| `svacer.serviceGrpc.loadBalancerIP` | Set external ip address for snapshots uploading service. Works only with `svacer.serviceGrpc.type: LoadBalancer` | `` |
| `svacer.logLevel` | Defines log level. Available: `info`, `warn`, `error` | `warn` |
| `svacer.memsettings` | Defines memory usage settings (experimental option). Supported options: `badger-tiny` - reduce memory usage of badger key-value store `badger-metrics` - turn on metrics calculation for badger `sync_writes` - turn on sync writes to file system `server_minimal` - turn on recommended settings for server with limited resources | `default` |
| `svacer.readinessProbe.enabled` | Enabled readiness probe | `false` |
| `svacer.readinessProbe.failureThreshold` | Probe failure times | `3` |
| `svacer.readinessProbe.periodSeconds` | Probe frequency | `10` |
| `svacer.readinessProbe.initialDelaySeconds` | Run probes after start in seconds | `60` |
| `svacer.readinessProbe.timeoutSeconds` | Check timeout in seconds | `5` |
| `svacer.livenessProbe.enabled` | Enabled liveness probe | `false` |
| `svacer.livenessProbe.failureThreshold` | Probe failure times | `6` |
| `svacer.livenessProbe.periodSeconds` | Probe frequency | `10` |
| `svacer.livenessProbe.initialDelaySeconds` | Run probes after start in seconds | `60` |
| `svacer.livenessProbe.timeoutSeconds` | Check timeout in seconds | `5` |
| `svacer.storage.enabled` | Create PVC for samples storage | `true` |
| `svacer.storage.storageClassName` | Set storage class instead of default | `` |
| `svacer.storage.accessMode` |  | `ReadWriteOnce` |
| `svacer.storage.size` | Storage size limit | `5Gi` |

### Database connection

Svacer needs postgresql database for service data storage. IMPORTAT: database user must have superuser permissions for extensions creation.

| Parameter | Description | Default values |
| --- | --- | --- |
| `database.existingSecret.name` | Get database settings from existing secret | `` |
| `database.existingSecret.passwordKey` | Password key in existing secret | `` |
| `database.existingSecret.uasernameKey` | User key in existing secret | `` |
| `database.existingSecret.databaseKey` | Database key in existing secret | `` |
| `database.existingSecret.hostKey` | Database address key in existing secret | `` |
| `database.existingSecret.portKey` | Database port key in existing secret | `` |
| `database.password` | Database password | `` |
| `database.username` | Database user | `` |
| `database.database` | Database name | `` |
| `database.host` | Database address | `` |
| `database.port` | Database port | `` |

### Ingress settings

| Parameter | Description | Default values |
| --- | --- | --- |
| `ingress.enabled` | Deploy ingress for svacer | `false` |
| `ingress.hosts` | Set hostnames for svacer | `[]` |
| `ingress.tls` | TLS configuration for ingress | `[]` |

### Monitoring settings

Chart can deploy [prometheus operator](https://github.com/prometheus-operator/prometheus-operator) CRD

| Parameter | Description | Default values |
| --- | --- | --- |
| `metrics.enabled` | Enable prometheus format metrics | `false` |
| `metrics.serviceMonitor.enabled` | Deploy prometheus service monitor custom resource | `false` |
| `metrics.serviceMonitor.interval` | Metrics check interval | `1m` |

### LDAP integration

| Parameter | Description | Default values |
| --- | --- | --- |
| `ldap.enabled` | Enable LDAP auth | `false` |
| `ldap.disableLocalAuth` | Use only LDAP auth | `false` |
| `ldap.existingSecret` | Name of the secret with ldap integration config | `` |
| `ldap.existingSecretKey` | Key for ldap integration config | `ldap.json` |

## Contributing

Feel free to contribute by making a [pull request](https://github.com/rickraven/helm-svacer/pull/new/master).

Please read the official [Contribution Guide](https://github.com/helm/charts/blob/master/CONTRIBUTING.md) from Helm for more information on how you can contribute to this Chart.

## License

[GNU GPL v3](/LICENSE.md)