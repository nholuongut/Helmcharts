# TestLink

[TestLink](http://www.testlink.org) is a web-based test management system that facilitates software quality assurance. It is developed and maintained by Teamtest. The platform offers support for test cases, test suites, test plans, test projects and user management, as well as various reports and statistics.

## This Helm chart is deprecated

Given the [`stable` deprecation timeline](https://github.com/nholuongut/Helmcharts#deprecation-timeline), the Bitnami maintained TestLink Helm chart is now located at [bitnami/charts](https://github.com/bitnami/charts/).

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
$ helm install my-release stable/testlink
```

## Introduction

This chart bootstraps a [TestLink](https://github.com/bitnami/bitnami-docker-testlink) deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

It also packages the [Bitnami MariaDB chart](https://github.com/kubernetes/charts/tree/master/stable/mariadb) which is required for bootstrapping a MariaDB deployment for the database requirements of the TestLink application.

Bitnami charts can be used with [Kubeapps](https://kubeapps.com/) for deployment and management of Helm Charts in clusters. This chart has been tested to work with NGINX Ingress, cert-manager, fluentd and Prometheus on top of the [BKPR](https://kubeprod.io/).

## Prerequisites

- Kubernetes 1.12+
- Helm 2.11+ or Helm 3.0-beta3+
- PV provisioner support in the underlying infrastructure
- ReadWriteMany volumes for deployment scaling

## Installing the Chart

To install the chart with the release name `my-release`:

```console
$ helm install my-release stable/testlink
```

The command deploys TestLink on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

The following table lists the configurable parameters of the TestLink chart and their default values.

| Parameter                           | Description                                                                                           | Default                                                      |
|-------------------------------------|-------------------------------------------------------------------------------------------------------|--------------------------------------------------------------|
| `global.imageRegistry`              | Global Docker image registry                                                                          | `nil`                                                        |
| `global.imagePullSecrets`           | Global Docker registry secret names as an array                                                       | `[]` (does not add image pull secrets to deployed pods)      |
| `global.storageClass`               | Global storage class for dynamic provisioning                                                         | `nil`                                                        |
| `image.registry`                    | TestLink image registry                                                                               | `docker.io`                                                  |
| `image.repository`                  | TestLink image name                                                                                   | `bitnami/testlink`                                           |
| `image.tag`                         | TestLink image tag                                                                                    | `{TAG_NAME}`                                                 |
| `image.pullPolicy`                  | Image pull policy                                                                                     | `IfNotPresent`                                               |
| `image.pullSecrets`                 | Specify docker-registry secret names as an array                                                      | `[]` (does not add image pull secrets to deployed pods)      |
| `nameOverride`                      | String to partially override testlink.fullname template with a string (will prepend the release name) | `nil`                                                        |
| `fullnameOverride`                  | String to fully override testlink.fullname template with a string                                     | `nil`                                                        |
| `testlinkUsername`                  | Admin username                                                                                        | `user`                                                       |
| `testlinkPassword`                  | Admin user password                                                                                   | _random 10 character long alphanumeric string_               |
| `testlinkEmail`                     | Admin user email                                                                                      | `user@example.com`                                           |
| `smtpEnable`                        | Enable SMTP                                                                                           | `false`                                                      |
| `smtpHost`                          | SMTP host                                                                                             | `nil`                                                        |
| `smtpPort`                          | SMTP port                                                                                             | `nil`                                                        |
| `smtpUser`                          | SMTP user                                                                                             | `nil`                                                        |
| `smtpPassword`                      | SMTP password                                                                                         | `nil`                                                        |
| `smtpConnectionMode`                | SMTP connection mode [`ssl`, `tls`]                                                                   | `nil`                                                        |
| `allowEmptyPassword`                | Allow DB blank passwords                                                                              | `yes`                                                        |
| `ingress.enabled`                   | Enable ingress controller resource                                                                    | `false`                                                      |
| `ingress.annotations`               | Ingress annotations                                                                                   | `[]`                                                         |
| `ingress.certManager`               | Add annotations for cert-manager                                                                      | `false`                                                      |
| `ingress.hosts[0].name`             | Hostname to your testlink installation                                                                | `testlink.local`                                             |
| `ingress.hosts[0].path`             | Path within the url structure                                                                         | `/`                                                          |
| `ingress.hosts[0].tls`              | Utilize TLS backend in ingress                                                                        | `false`                                                      |
| `ingress.hosts[0].tlsHosts`         | Array of TLS hosts for ingress record (defaults to `ingress.hosts[0].name` if `nil`)                  | `nil`                                                        |
| `ingress.hosts[0].tlsSecret`        | TLS Secret (certificates)                                                                             | `testlink.local-tls-secret`                                  |
| `ingress.secrets[0].name`           | TLS Secret Name                                                                                       | `nil`                                                        |
| `ingress.secrets[0].certificate`    | TLS Secret Certificate                                                                                | `nil`                                                        |
| `ingress.secrets[0].key`            | TLS Secret Key                                                                                        | `nil`                                                        |
| `externalDatabase.host`             | Host of the external database                                                                         | `nil`                                                        |
| `externalDatabase.port`             | Port of the external database                                                                         | `3306`                                                       |
| `externalDatabase.user`             | Existing username in the external db                                                                  | `bn_testlink`                                                |
| `externalDatabase.password`         | Password for the above username                                                                       | `nil`                                                        |
| `externalDatabase.database`         | Name of the existing database                                                                         | `bitnami_testlink`                                           |
| `mariadb.enabled`                   | Whether to use the MariaDB chart                                                                      | `true`                                                       |
| `mariadb.db.name`                   | Database name to create                                                                               | `bitnami_testlink`                                           |
| `mariadb.db.user`                   | Database user to create                                                                               | `bn_testlink`                                                |
| `mariadb.db.password`               | Password for the database                                                                             | `nil`                                                        |
| `mariadb.rootUser.password`         | MariaDB admin password                                                                                | `nil`                                                        |
| `service.type`                      | Kubernetes Service type                                                                               | `LoadBalancer`                                               |
| `service.port`                      | Service HTTP port                                                                                     | `80`                                                         |
| `service.httpsPort`                 | Service HTTPS port                                                                                    | `443`                                                        |
| `service.nodePorts.http`            | Kubernetes http node port                                                                             | `""`                                                         |
| `service.nodePorts.https`           | Kubernetes https node port                                                                            | `""`                                                         |
| `service.externalTrafficPolicy`     | Enable client source IP preservation                                                                  | `Cluster`                                                    |
| `service.loadBalancerIP`            | LoadBalancer service IP address                                                                       | `""`                                                         |
| `service.annotations`               | Kubernetes service annotations, evaluated as a template                                               | `{}`                                                         |
| `persistence.enabled`               | Enable persistence using PVC                                                                          | `true`                                                       |
| `persistence.testlink.storageClass` | PVC Storage Class for TestLink volume                                                                 | `nil` (uses alpha storage class annotation)                  |
| `persistence.testlink.accessMode`   | PVC Access Mode for TestLink volume                                                                   | `ReadWriteOnce`                                              |
| `persistence.testlink.size`         | PVC Storage Request for TestLink volume                                                               | `8Gi`                                                        |
| `resources`                         | CPU/Memory resource requests/limits                                                                   | Memory: `512Mi`, CPU: `300m`                                 |
| `podAnnotations`                    | Pod annotations                                                                                       | `{}`                                                         |
| `affinity`                          | Map of node/pod affinities                                                                            | `{}`                                                         |
| `metrics.enabled`                   | Start a side-car prometheus exporter                                                                  | `false`                                                      |
| `metrics.image.registry`            | Apache exporter image registry                                                                        | `docker.io`                                                  |
| `metrics.image.repository`          | Apache exporter image name                                                                            | `bitnami/apache-exporter`                                    |
| `metrics.image.tag`                 | Apache exporter image tag                                                                             | `{TAG_NAME}`                                                 |
| `metrics.image.pullPolicy`          | Image pull policy                                                                                     | `IfNotPresent`                                               |
| `metrics.image.pullSecrets`         | Specify docker-registry secret names as an array                                                      | `[]` (does not add image pull secrets to deployed pods)      |
| `metrics.podAnnotations`            | Additional annotations for Metrics exporter pod                                                       | `{prometheus.io/scrape: "true", prometheus.io/port: "9117"}` |
| `metrics.resources`                 | Exporter resource requests/limit                                                                      | {}                                                           |

The above parameters map to the env variables defined in [bitnami/testlink](http://github.com/bitnami/bitnami-docker-testlink). For more information please refer to the [bitnami/testlink](http://github.com/bitnami/bitnami-docker-testlink) image documentation.

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
$ helm install my-release \
  --set testlinkUsername=admin,testlinkPassword=password,mariadb.mariadbRootPassword=secretpassword \
    stable/testlink
```

The above command sets the TestLink administrator account username and password to `admin` and `password` respectively. Additionally, it sets the MariaDB `root` user password to `secretpassword`.

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example,

```console
$ helm install my-release -f values.yaml stable/testlink
```

> **Tip**: You can use the default [values.yaml](values.yaml)

## Configuration and installation details

### [Rolling VS Immutable tags](https://docs.bitnami.com/containers/how-to/understand-rolling-tags-containers/)

It is strongly recommended to use immutable tags in a production environment. This ensures your deployment does not change automatically if the same tag is updated with a different image.

Bitnami will release a new chart updating its containers if a new version of the main container, significant changes, or critical vulnerabilities exist.

## Persistence

The [Bitnami TestLink](https://github.com/bitnami/bitnami-docker-testlink) image stores the TestLink data and configurations at the `/bitnami/testlink` path of the container.

Persistent Volume Claims are used to keep the data across deployments. This is known to work in GCE, AWS, and minikube.
See the [Parameters](#parameters) section to configure the PVC or to disable persistence.

## Upgrading

### 7.0.0

Helm performs a lookup for the object based on its group (apps), version (v1), and kind (Deployment). Also known as its GroupVersionKind, or GVK. Changing the GVK is considered a compatibility breaker from Kubernetes' point of view, so you cannot "upgrade" those objects to the new GVK in-place. Earlier versions of Helm 3 did not perform the lookup correctly which has since been fixed to match the spec.

In https://github.com/nholuongut/Helmcharts/pull/17311 the `apiVersion` of the deployment resources was updated to `apps/v1` in tune with the api's deprecated, resulting in compatibility breakage.

This major version signifies this change.

### To 3.0.0

Backwards compatibility is not guaranteed unless you modify the labels used on the chart's deployments.
Use the workaround below to upgrade from versions previous to 3.0.0. The following example assumes that the release name is testlink:

```console
$ kubectl patch deployment testlink-testlink --type=json -p='[{"op": "remove", "path": "/spec/selector/matchLabels/chart"}]'
$ kubectl delete statefulset testlink-mariadb --cascade=false
```
