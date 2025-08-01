# ⚠️ Repo Archive Notice

As of Nov 13, 2020, charts in this repo will no longer be updated.
For more information, see the Helm Charts [Deprecation and Archive Notice](https://github.com/nholuongut/Helmcharts#%EF%B8%8F-deprecation-and-archive-notice), and [Update](https://helm.sh/blog/charts-repo-deprecation/).

# Karma

Karma is an ASL2 licensed alert dashboard for Prometheus Alertmanager.

## DEPRECATION NOTICE

This chart is deprecated and no longer supported.

## Introduction

This chart deploys karma to your cluster via a Deployment and Service.
Optionally you can also enable ingress.

# Prerequisites

- Kubernetes 1.9+

## Installing the Chart

To install the chart with the release name `my-release`, run:

```bash
$ helm install --name my-release stable/karma
```

After a few seconds, you should see service statuses being written to the configured output.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the karma chart and their default values.

|             Parameter               |            Description                                 |                    Default                |
|-------------------------------------|--------------------------------------------------------|-------------------------------------------|
| `replicaCount`                      | Number of replicas                                     | `1`                                       |
| `image.repository`                  | The image to run                                       | `lmierzwa/karma`                          |
| `image.tag`                         | The image tag to pull                                  | `v0.72`                                   |
| `image.pullPolicy`                  | Image pull policy                                      | `IfNotPresent`                            |
| `nameOverride`                      | Override name of app                                   | ``                                        |
| `fullnameOverride`                  | Override full name of app                              | ``                                        |
| `service.type`                      | Type of Service                                        | `ClusterIP`                               |
| `service.port`                      | Port for kubernetes service                            | `80`                                      |
| `service.annotations`               | Annotations to add to the service                      | `{}`                                      |
| `deployment.annotations`            | Annotations to add to the deployment                   | `{}`                                      |
| `resources.requests.cpu`            | CPU resource requests                                  |                                           |
| `resources.limits.cpu`              | CPU resource limits                                    |                                           |
| `resources.requests.memory`         | Memory resource requests                               |                                           |
| `resources.limits.memory`           | Memory resource limits                                 |                                           |
| `ingress`                           | Settings for ingress                                   | `{}`                                      |
| `nodeSelector`                      | Settings for nodeselector                              | `{}`                                      |
| `tolerations`                       | Settings for toleration                                | `{}`                                      |
| `affinity`                          | Settings for affinity                                  | `{}`                                      |
| `podLabels`                         | Labels to be added to pod                              | `{}`                                      |
| `podAnnotations`                    | Annotations to be added to pod                         | `{}`                                      |
| `securityContext`                   | Settings for security context                          | `{}`                                      |
| `serviceAccount.create`             | Create service-account                                 | `true`                                    |
| `serviceAccount.name`               | Override service-account name                          | ``                                        |
| `livenessProbe.delay`               | Specify delay in executing probe                       | `5`                                       |
| `livenessProbe.period`              | Speicy period of liveness probe                        | `5`                                       |
| `livenessProbe.path`                | Specify path liveness probe should hit                 | `/health`                                 |
| `configMap.enabled`                 | Provide a custom karma configuration                   | `false`                                   |
| `configMap.annotations`             | Annotations to add to the config map                   | `{}`                                      |
| `configMap.rawConfig`               | A karma compatible YAML configuration                  | ``                                        |
| `certSecretNames`                   | Mount Alertmanager certificates to `/etc/certs/<name>` | `[]`                                      |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
    stable/karma
```

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name my-release -f values.yaml stable/karma
```

> **Tip**: You will have to define the URL to alertmanager in env-settings in [values.yaml](values.yaml), under key ALERTMANAGER_URI .
