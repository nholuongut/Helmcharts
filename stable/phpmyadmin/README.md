# phpMyAdmin

[phpMyAdmin](https://www.phpmyadmin.net/) is a free and open source administration tool for MySQL and MariaDB. As a portable web application written primarily in PHP, it has become one of the most popular MySQL administration tools, especially for web hosting services.

## This Helm chart is deprecated

Given the [`stable` deprecation timeline](https://github.com/nholuongut/Helmcharts#deprecation-timeline), the Bitnami maintained phpMyAdmin Helm chart is now located at [bitnami/charts](https://github.com/bitnami/charts/).

The Bitnami repository is already included in the Hubs and we will continue providing the same cadence of updates, support, etc that we've been keeping here these years. Installation instructions are very similar, just adding the _bitnami_ repo and using it during the installation (`bitnami/<chart>` instead of `stable/<chart>`)

```bash
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm install my-release bitnami/<chart>           # Helm 3
$ helm install --name my-release bitnami/<chart>    # Helm 2
```

To update an exisiting _stable_ deployment with a chart hosted in the bitnami repository you can execute

```bash
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm upgrade my-release bitnami/<chart>
```

Issues and PRs related to the chart itself will be redirected to `bitnami/charts` GitHub repository. In the same way, we'll be happy to answer questions related to this migration process in [this issue](https://github.com/nholuongut/Helmcharts/issues/20969) created as a common place for discussion.

## TL;DR;

```console
$ helm install my-release stable/phpmyadmin
```

## Introduction

This chart bootstraps a [phpMyAdmin](https://github.com/bitnami/bitnami-docker-phpmyadmin) deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

Bitnami charts can be used with [Kubeapps](https://kubeapps.com/) for deployment and management of Helm Charts in clusters. This chart has been tested to work with NGINX Ingress, cert-manager, fluentd and Prometheus on top of the [BKPR](https://kubeprod.io/).

## Prerequisites

- Kubernetes 1.8+ with Beta APIs enabled

## Installing the Chart

To install the chart with the release name `my-release`:

```console
$ helm install my-release stable/phpmyadmin
```

The command deploys phpMyAdmin on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

The following table lists the configurable parameters of the phpMyAdmin chart and their default values.

| Parameter                    | Description                                                                                             | Default                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| `global.imageRegistry`       | Global Docker image registry                                                                            | `nil`                                                                                         |
| `global.imagePullSecrets`    | Global Docker registry secret names as an array                                                         | `[]` (does not add image pull secrets to deployed pods)                                       |
| `image.registry`             | phpMyAdmin image registry                                                                               | `docker.io`                                                                                   |
| `image.repository`           | phpMyAdmin image name                                                                                   | `bitnami/phpmyadmin`                                                                          |
| `image.tag`                  | phpMyAdmin image tag                                                                                    | `{TAG_NAME}`                                                                                  |
| `image.pullPolicy`           | Image pull policy                                                                                       | `IfNotPresent`                                                                                |
| `image.pullSecrets`          | Specify docker-registry secret names as an array                                                        | `[]` (does not add image pull secrets to deployed pods)                                       |
| `nameOverride`               | String to partially override phpmyadmin.fullname template with a string (will prepend the release name) | `nil`                                                                                         |
| `fullnameOverride`           | String to fully override phpmyadmin.fullname template with a string                                     | `nil`                                                                                         |
| `service.type`               | Type of service for phpMyAdmin frontend                                                                 | `ClusterIP`                                                                                   |
| `service.port`               | Port to expose service                                                                                  | `80`                                                                                          |
| `db.port`                    | Database port to use to connect                                                                         | `3306`                                                                                        |
| `db.chartName`               | Database suffix if included in the same release                                                         | `nil`                                                                                         |
| `db.host`                    | Database host to connect to                                                                             | `nil`                                                                                         |
| `db.bundleTestDB`            | Deploy a MariaDB instance for testing purposes                                                          | `false`                                                                                       |
| `db.enableSsl`               | Enable SSL for the connection between phpMyAdmin and the database                                       | `false`                                                                                       |
| `db.ssl.clientKey`           | Client key file when using SSL                                                                          | ``                                                                                            |
| `db.ssl.clientCertificate`   | Client certificate file when using SSL                                                                  | ``                                                                                            |
| `db.ssl.caCertificate`       | CA file when using SSL                                                                                  | ``                                                                                            |
| `db.ssl.ciphers`             | List of allowable ciphers for connections when using SSL                                                | `nil`                                                                                         |
| `db.ssl.verify`              | Enable SSL certificate validation                                                                       | `true`                                                                                        |
| `ingress.enabled`            | Enable ingress controller resource                                                                      | `false`                                                                                       |
| `ingress.certManager`        | Add annotations for cert-manager                                                                        | `false`                                                                                       |
| `ingress.rewriteTarget`      | Add annotations to redirect traffic to `/`                                                              | `true`                                                                                        |
| `ingress.annotations`        | Ingress annotations                                                                                     | `{}`                                                                                          |
| `ingress.hosts[0].name`      | Hostname to your PHPMyAdmin installation                                                                | `phpmyadmin.local`                                                                            |
| `ingress.hosts[0].path`      | Path within the url structure                                                                           | `/`                                                                                           |
| `ingress.hosts[0].tls`       | Utilize TLS backend in ingress                                                                          | `false`                                                                                       |
| `ingress.hosts[0].tlsHosts`  | Array of TLS hosts for ingress record (defaults to `ingress.hosts[0].name` if `nil`)                    | `nil`                                                                                         |
| `ingress.hosts[0].tlsSecret` | TLS Secret (certificates)                                                                               | `phpmyadmin.local-tls-secret`                                                                 |
| `resources`                  | CPU/Memory resource requests/limits                                                                     | `{}`                                                                                          |
| `nodeSelector`               | Node labels for pod assignment                                                                          | `{}`                                                                                          |
| `tolerations`                | List of node taints to tolerate                                                                         | `[]`                                                                                          |
| `affinity`                   | Map of node/pod affinities                                                                              | `{}`                                                                                          |
| `podLabels`                  | Pod labels                                                                                              | `{}`                                                                                          |
| `podAnnotations`             | Pod annotations                                                                                         | `{}`                                                                                          |
| `metrics.enabled`            | Start a side-car prometheus exporter                                                                    | `false`                                                                                       |
| `metrics.image.registry`     | Apache exporter image registry                                                                          | `docker.io`                                                                                   |
| `metrics.image.repository`   | Apache exporter image name                                                                              | `bitnami/apache-exporter`                                                                     |
| `metrics.image.tag`          | Apache exporter image tag                                                                               | `{TAG_NAME}`                                                                                  |
| `metrics.image.pullPolicy`   | Image pull policy                                                                                       | `IfNotPresent`                                                                                |
| `metrics.image.pullSecrets`  | Specify docker-registry secret names as an array                                                        | `[]` (does not add image pull secrets to deployed pods)                                       |
| `metrics.podAnnotations`     | Additional annotations for Metrics exporter pod                                                         | `{prometheus.io/scrape: "true", prometheus.io/port: "9117"}`                                  |
| `metrics.resources`          | Exporter resource requests/limit                                                                        | {}                                                                                            |

For more information please refer to the [bitnami/phpmyadmin](http://github.com/bitnami/bitnami-docker-Phpmyadmin) image documentation.

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
$ helm install my-release \
  --set db.host=mymariadb,db.port=3306 stable/phpmyadmin
```

The above command sets the phpMyAdmin to connect to a database in `mymariadb` host and `3306` port respectively.

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example,

```console
$ helm install my-release -f values.yaml stable/phpmyadmin
```

> **Tip**: You can use the default [values.yaml](values.yaml)

## Configuration and installation details

### [Rolling VS Immutable tags](https://docs.bitnami.com/containers/how-to/understand-rolling-tags-containers/)

It is strongly recommended to use immutable tags in a production environment. This ensures your deployment does not change automatically if the same tag is updated with a different image.

Bitnami will release a new chart updating its containers if a new version of the main container, significant changes, or critical vulnerabilities exist.

## Upgrading

### To 1.0.0

Backwards compatibility is not guaranteed unless you modify the labels used on the chart's deployments.
Use the workaround below to upgrade from versions previous to `1.0.0`. The following example assumes that the release name is `phpmyadmin`:

```console
$ kubectl patch deployment phpmyadmin-phpmyadmin --type=json -p='[{"op": "remove", "path": "/spec/selector/matchLabels/chart"}]'
```
