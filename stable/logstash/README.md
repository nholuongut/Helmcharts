# ⚠️ Repo Archive Notice

As of Nov 13, 2020, charts in this repo will no longer be updated.
For more information, see the Helm Charts [Deprecation and Archive Notice](https://github.com/nholuongut/Helmcharts#%EF%B8%8F-deprecation-and-archive-notice), and [Update](https://helm.sh/blog/charts-repo-deprecation/).

# Logstash

[Logstash](https://www.elastic.co/products/logstash) is an open source, server-side data processing pipeline that ingests data from a multitude of sources simultaneously, transforms it, and then sends it to your favorite “stash.”

## DEPRECATION NOTICE

This chart is deprecated and no longer supported.

## TL;DR;

```console
$ helm install stable/logstash
```

## Installing the Chart

To install the chart with the release name `my-release`:

```console
$ helm install --name my-release stable/logstash
```

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
$ helm delete my-release
```

The command removes nearly all the Kubernetes components associated with the
chart and deletes the release.

## Best Practices

### Release and tune this chart once per Logstash pipeline

To achieve multiple pipelines with this chart, current best practice is to
maintain one pipeline per chart release. In this way configuration is
simplified and pipelines are more isolated from one another.

### Default Pipeline: Beats Input -> Elasticsearch Output

Current best practice for ELK logging is to ship logs from hosts using Filebeat
to logstash where persistent queues are enabled. Filebeat supports structured
(e.g. JSON) and unstructured (e.g. log lines) log shipment.

### Load Beats-generated index template into Elasticsearch

To best utilize the combination of Beats, Logstash and Elasticsearch,
load Beats-generated index templates into Elasticsearch as described [here](
https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-template.html).

On a remote-to-Kubernetes Linux instance you might run the following command to
load that instance's Beats-generated index template into Elasticsearch
(Elasticsearch hostname will vary).

```
filebeat setup --template -E output.logstash.enabled=false \
  -E 'output.elasticsearch.hosts=["elasticsearch.cluster.local:9200"]'
```

### Links

Please review the following links that expound on current best practices.

- https://www.elastic.co/blog/structured-logging-filebeat
- https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-template.html
- https://www.elastic.co/guide/en/logstash/current/deploying-and-scaling.html
- https://www.elastic.co/guide/en/logstash/current/persistent-queues.html

## Configuration

The following table lists the configurable parameters of the chart and its default values.

|              Parameter          |                    Description                     |                     Default                      |
| ------------------------------- | -------------------------------------------------- | ------------------------------------------------ |
| `replicaCount`                  | Number of replicas                                 | `1`                                              |
| `podDisruptionBudget`           | Pod disruption budget                              | `maxUnavailable: 1`                              |
| `updateStrategy`                | Update strategy                                    | `type: RollingUpdate`                            |
| `image.repository`              | Container image name                               | `docker.elastic.co/logstash/logstash-oss`        |
| `image.tag`                     | Container image tag                                | `7.1.1`                                          |
| `image.pullPolicy`              | Container image pull policy                        | `IfNotPresent`                                   |
| `service.type`                  | Service type (ClusterIP, NodePort or LoadBalancer) | `ClusterIP`                                      |
| `service.annotations`           | Service annotations                                | `{}`                                             |
| `service.ports`                 | Ports exposed by service                           | beats                                            |
| `service.loadBalancerIP`        | The load balancer IP for the service               | unset                                            |
| `service.loadBalancerSourceRanges` | CIDR ranges to allow access to load balancer       | unset                                            |
| `service.clusterIP`             | The cluster IP for the service                     | unset                                            |
| `service.nodePort`              | The nodePort for the service                       | unset                                            |
| `service.externalTrafficPolicy` | Set externalTrafficPolicy                          | unset                                            |
| `ports`                         | Ports exposed by logstash container                | beats                                            |
| `ingress.enabled`               | Enables Ingress                                    | `false`                                          |
| `ingress.annotations`           | Ingress annotations                                | `{}`                                             |
| `ingress.path`                  | Ingress path                                       | `/`                                              |
| `ingress.hosts`                 | Ingress accepted hostnames                         | `["logstash.cluster.local"]`                     |
| `ingress.tls`                   | Ingress TLS configuration                          | `[]`                                             |
| `logstashJavaOpts`              | Java options for logstash like heap size           | `"-Xmx1g -Xms1g"`                                |
| `resources`                     | Pod resource requests & limits                     | `{}`                                             |
| `priorityClassName`             | priorityClassName                                  | `nil`                                            |
| `nodeSelector`                  | Node selector                                      | `{}`                                             |
| `tolerations`                   | Tolerations                                        | `[]`                                             |
| `affinity`                      | Affinity or Anti-Affinity                          | `{}`                                             |
| `podAnnotations`                | Pod annotations                                    | `{}`                                             |
| `podLabels`                     | Pod labels                                         | `{}`                                             |
| `extraEnv`                      | Extra pod environment variables                    | `[]`                                             |
| `envFrom`                       | Environment variables from configMaps & secrets    | `[]`                                             |
| `extraContainers`               | Add additional containers                          | `[]`                                             |
| `extraInitContainers`           | Add additional initContainers                      | `[]`                                             |
| `podManagementPolicy`           | podManagementPolicy of the StatefulSet             | `OrderedReady`                                   |
| `livenessProbe`                 | Liveness probe settings for logstash container     | (see `values.yaml`)                              |
| `readinessProbe`                | Readiness probe settings for logstash container    | (see `values.yaml`)                              |
| `persistence.enabled`           | Enable persistence                                 | `true`                                           |
| `persistence.storageClass`      | Storage class for PVCs                             | unset                                            |
| `persistence.accessMode`        | Access mode for PVCs                               | `ReadWriteOnce`                                  |
| `persistence.size`              | Size for PVCs                                      | `2Gi`                                            |
| `volumeMounts`                  | Volume mounts to configure for logstash container  | (see `values.yaml`)                              |
| `volumes`                       | Volumes to configure for logstash container        | []                                               |
| `terminationGracePeriodSeconds` | Duration the pod needs to terminate gracefully     | `30`                                             |
| `exporter.logstash`             | Prometheus logstash-exporter settings              | (see `values.yaml`)                              |
| `exporter.logstash.enabled`     | Enables Prometheus logstash-exporter               | `false`                                          |
| `exporter.serviceMonitor.enabled` | If true, a ServiceMonitor CRD is created for a prometheus operator | `false`
| `exporter.serviceMonitor.namespace` | If set, the ServiceMonitor will be installed in a different namespace  | `""`
| `exporter.serviceMonitor.labels` | Labels for prometheus operator | `{}`
| `exporter.serviceMonitor.interval` | Interval at which metrics should be scraped | `10s`
| `exporter.serviceMonitor.scrapeTimeout` | Timeout after which the scrape is ended | `10s`
| `exporter.serviceMonitor.scheme` | Scheme to use for scraping | `http`
| `elasticsearch.host`            | ElasticSearch hostname (passed through tpl)        | `elasticsearch-client.default.svc.cluster.local` |
| `elasticsearch.port`            | ElasticSearch port                                 | `9200`                                           |
| `config`                        | Logstash configuration key-values                  | (see `values.yaml`)                              |
| `patterns`                      | Logstash patterns configuration                    | `nil`                                            |
| `files`                         | Logstash custom files configuration                | `nil`                                            |
| `binaryFiles`                   | Logstash custom binary files                       | `nil`                                            |
| `inputs`                        | Logstash inputs configuration                      | beats                                            |
| `filters`                       | Logstash filters configuration                     | `nil`                                            |
| `outputs`                       | Logstash outputs configuration                     | elasticsearch                                    |
| `securityContext.fsGroup`       | Group ID for the container                         | `1000`                                           |
| `securityContext.runAsUser`     | User ID for the container                          | `1000`                                           |
| `args`                          | Additional arguments to pass to Logstash           | `1000`                                           |
| `serviceAccount.create`         | Specifies if a ServiceAccount should be created    | `true`                                           |
| `serviceAccount.name`           | The name of the ServiceAccount to use              | `""`                                             |
