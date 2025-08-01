# ⚠️ Repo Archive Notice

As of Nov 13, 2020, charts in this repo will no longer be updated.
For more information, see the Helm Charts [Deprecation and Archive Notice](https://github.com/nholuongut/Helmcharts#%EF%B8%8F-deprecation-and-archive-notice), and [Update](https://helm.sh/blog/charts-repo-deprecation/).

# Chaoskube Helm Chart

[chaoskube](https://github.com/linki/chaoskube) periodically kills random pods in your Kubernetes cluster.

## DEPRECATION NOTICE

This chart is deprecated and no longer supported.

## TL;DR;

```console
$ helm install stable/chaoskube
```

## Installing the Chart

To install the chart with the release name `my-release`:

```console
$ helm install --name my-release stable/chaoskube
```

The command deploys chaoskube on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the my-release deployment:

```console
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

By default `chaoskube` runs in dry-run mode so it doesn't actually kill anything.
If you're sure you want to use it run `helm` with:

```console
$ helm install stable/chaoskube --set dryRun=false
```

| Parameter                                 | Description                                                                           | Default                          |
|-------------------------------------------|---------------------------------------------------------------------------------------|----------------------------------|
| `name`                                    | container name                                                                        | chaoskube                        |
| `image`                                   | docker image                                                                          | quay.io/linki/chaoskube          |
| `imageTag`                                | docker image tag                                                                      | v0.11.0                          |
| `imagePullSecrets`                        | A list of secret names for accessing private image registries                         | `[]`                             |
| `replicas`                                | number of replicas to run                                                             | 1                                |
| `interval`                                | interval between pod terminations                                                     | 10m                              |
| `labels`                                  | label selector to filter pods by                                                      | "" (matches everything)          |
| `kinds`                                   | OwnerReference's Kind selector to filter pods by                                      | "" (matches everything)          |
| `annotations`                             | annotation selector to filter pods by                                                 | "" (matches everything)          |
| `namespaces`                              | namespace selector to filter pods by                                                  | "" (all namespaces)              |
| `dryRun`                                  | don't kill pods, only log what would have been done                                   | true                             |
| `logFormat`                               | Options to choose log format(i.e. "text" or "json")                                   | "text"                           |
| `debug`                                   | Enable debug logging mode, for detailed logs                                          | false                            |
| `timezone`                                | Set timezone for running actions (Optional)                                           | "" (UTC)                         |
| `excludedWeekdays`                        | Set Days of the Week to avoid actions (Optional)                                      | "" (Don't skip any weekdays)     |
| `excludedTimesOfDay`                      | Set Time Range to avoid actions (Optional)                                            | "" (Don't skip any times of day) |
| `excludedDaysOfYear`                      | Set Days of the Year to avoid actions (Optional)                                      | "" (Don't skip any days)         |
| `priorityClassName`                       | priorityClassName                                                                     | `nil`                            |
| `rbac.create`                             | create rbac service account and roles                                                 | false                            |
| `rbac.serviceAccountName`                 | name of serviceAccount to use when create is false                                    | default                          |
| `resources`                               | CPU/Memory resource requests/limits                                                   | `{}`                             |
| `nodeSelector`                            | Node labels for pod assignment                                                        | `{}`                             |
| `tolerations`                             | Toleration labels for pod assignment                                                  | `[]`                             |
| `affinity`                                | Affinity settings for pod assignment                                                  | `{}`                             |
| `minimumAge`                              | Set minimum pod age to filter pod by                                                  | `0s`                             |
| `podAnnotations`                          | Annotations for the chaoskube pod                                                     | `{}`                             |
| `podLabels`                               | Labels for the chaoskube pod                                                          | `{}`                             |
| `gracePeriod`                             | grace period to give pods when terminating them                                       | `-1s` (pod decides)              |
| `metrics.enabled`                         | Enable metrics handler                                                                | `false`                          |
| `metrics.port`                            | Listening port for metrics handler                                                    | `8080`                           |
| `metrics.service.type`                    | Metrics service type                                                                  | `ClusterIP`                      |
| `metrics.service.port`                    | Metrics service port                                                                  | `8080`                           |
| `metrics.serviceMonitor.enabled`          | Set this to `true` to create ServiceMonitor for Prometheus operator                   | `false`                          |
| `metrics.serviceMonitor.additionalLabels` | Additional labels that can be used so ServiceMonitor will be discovered by Prometheus | `{}`                             |

Setting label and namespaces selectors from the shell can be tricky but is possible (example with zsh):

```console
$ helm install \
  --set labels='app=mate\,stage!=prod',namespaces='!kube-system\,!production' \
  stable/chaoskube --debug --dry-run | grep -A7 args
    args:
    - --interval=10m
    - --labels=app=foo,stage!=prod
    - --namespaces=!kube-system,!production
    - --timezone=America/New_York
    - --excludedWeekdays="Sat,Tue"
    - --excludedTimesOfDay="12:00-18:00"
    - --excludedDaysOfYear="Apr1,Dec24"
```
