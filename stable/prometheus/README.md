# Prometheus

DEPRECATED and moved to <https://github.com/prometheus-community/helm-charts>

[Prometheus](https://prometheus.io/), a [Cloud Native Computing Foundation](https://cncf.io/) project, is a systems and service monitoring system. It collects metrics from configured targets at given intervals, evaluates rule expressions, displays the results, and can trigger alerts if some condition is observed to be true.

## TL;DR;

```console
helm install stable/prometheus
```

## Introduction

This chart bootstraps a [Prometheus](https://prometheus.io/) deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.3+ with Beta APIs enabled

## Installing the Chart

To install the chart with the release name `my-release`:

```console
helm install --name my-release stable/prometheus
```

The command deploys Prometheus on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Prometheus 2.x

Prometheus version 2.x has made changes to alertmanager, storage and recording rules. Check out the migration guide [here](https://prometheus.io/docs/prometheus/2.0/migration/)

Users of this chart will need to update their alerting rules to the new format before they can upgrade.

## Upgrading from previous chart versions.

Version 9.0 adds a new option to enable or disable the Prometheus Server.
This supports the use case of running a Prometheus server in one k8s cluster and scraping exporters in another cluster while using the same chart for each deployment.
To install the server `server.enabled` must be set to `true`.

As of version 5.0, this chart uses Prometheus 2.x. This version of prometheus introduces a new data format and is not compatible with prometheus 1.x. It is recommended to install this as a new release, as updating existing releases will not work. See the [prometheus docs](https://prometheus.io/docs/prometheus/latest/migration/#storage) for instructions on retaining your old data.

### Example migration

Assuming you have an existing release of the prometheus chart, named `prometheus-old`. In order to update to prometheus 2.x while keeping your old data do the following:

1. Update the `prometheus-old` release. Disable scraping on every component besides the prometheus server, similar to the configuration below:

  ```yaml
  alertmanager:
    enabled: false
  alertmanagerFiles:
    alertmanager.yml: ""
  kubeStateMetrics:
    enabled: false
  nodeExporter:
    enabled: false
  pushgateway:
    enabled: false
  server:
    extraArgs:
      storage.local.retention: 720h
  serverFiles:
    alerts: ""
    prometheus.yml: ""
    rules: ""
  ```

1. Deploy a new release of the chart with version 5.0+ using prometheus 2.x. In the values.yaml set the scrape config as usual, and also add the `prometheus-old` instance as a remote-read target.

   ```yaml
    prometheus.yml:
      ...
      remote_read:
      - url: http://prometheus-old/api/v1/read
      ...
   ```

   Old data will be available when you query the new prometheus instance.

## Scraping Pod Metrics via Annotations

This chart uses a default configuration that causes prometheus
to scrape a variety of kubernetes resource types, provided they have the correct annotations.
In this section we describe how to configure pods to be scraped;
for information on how other resource types can be scraped you can
do a `helm template` to get the kubernetes resource definitions,
and then reference the prometheus configuration in the ConfigMap against the prometheus documentation
for [relabel_config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#relabel_config)
and [kubernetes_sd_config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes_sd_config).

In order to get prometheus to scrape pods, you must add annotations to the the pods as below:

```yaml
metadata:
  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/path: /metrics
    prometheus.io/port: "8080"
spec:
...
```

You should adjust `prometheus.io/path` based on the URL that your pod serves metrics from.
`prometheus.io/port` should be set to the port that your pod serves metrics from.
Note that the values for `prometheus.io/scrape` and `prometheus.io/port` must be
enclosed in double quotes.

## Configuration

The following table lists the configurable parameters of the Prometheus chart and their default values.

Parameter | Description | Default
--------- | ----------- | -------
`alertmanager.enabled` | If true, create alertmanager | `true`
`alertmanager.name` | alertmanager container name | `alertmanager`
`alertmanager.useClusterRole` | Use a ClusterRole (and ClusterRoleBinding). If set to false - we define a Role and RoleBinding in the defined namespaces ONLY. This makes alertmanager work - for users who do not have ClusterAdmin privs, but wants alertmanager to operate on their own namespaces, instead of clusterwide. | `alertmanager`
`alertmanager.useExistingRole` | Set to a rolename to use existing role - skipping role creating - but still doing serviceaccount and rolebinding to the rolename set here.  | `alertmanager`
`alertmanager.image.repository` | alertmanager container image repository | `prom/alertmanager`
`alertmanager.image.tag` | alertmanager container image tag | `v0.21.0`
`alertmanager.image.pullPolicy` | alertmanager container image pull policy | `IfNotPresent`
`alertmanager.prefixURL` | The prefix slug at which the server can be accessed | ``
`alertmanager.baseURL` | The external url at which the server can be accessed | `"http://localhost:9093"`
`alertmanager.extraArgs` | Additional alertmanager container arguments | `{}`
`alertmanager.extraSecretMounts` | Additional alertmanager Secret mounts | `[]`
`alertmanager.configMapOverrideName` | Prometheus alertmanager ConfigMap override where full-name is `{{.Release.Name}}-{{.Values.alertmanager.configMapOverrideName}}` and setting this value will prevent the default alertmanager ConfigMap from being generated | `""`
`alertmanager.configFromSecret` | The name of a secret in the same kubernetes namespace which contains the Alertmanager config, setting this value will prevent the default alertmanager ConfigMap from being generated | `""`
`alertmanager.configFileName` | The configuration file name to be loaded to alertmanager. Must match the key within configuration loaded from ConfigMap/Secret. | `alertmanager.yml`
`alertmanager.ingress.enabled` | If true, alertmanager Ingress will be created | `false`
`alertmanager.ingress.annotations` | alertmanager Ingress annotations | `{}`
`alertmanager.ingress.extraLabels` | alertmanager Ingress additional labels | `{}`
`alertmanager.ingress.hosts` | alertmanager Ingress hostnames | `[]`
`alertmanager.ingress.extraPaths` | Ingress extra paths to prepend to every alertmanager host configuration. Useful when configuring [custom actions with AWS ALB Ingress Controller](https://kubernetes-sigs.github.io/aws-alb-ingress-controller/guide/ingress/annotation/#actions) | `[]`
`alertmanager.ingress.tls` | alertmanager Ingress TLS configuration (YAML) | `[]`
`alertmanager.nodeSelector` | node labels for alertmanager pod assignment | `{}`
`alertmanager.tolerations` | node taints to tolerate (requires Kubernetes >=1.6) | `[]`
`alertmanager.affinity` | pod affinity | `{}`
`alertmanager.podDisruptionBudget.enabled` | If true, create a PodDisruptionBudget | `false`
`alertmanager.podDisruptionBudget.maxUnavailable` | Maximum unavailable instances in PDB | `1`
`alertmanager.schedulerName` | alertmanager alternate scheduler name | `nil`
`alertmanager.persistentVolume.enabled` | If true, alertmanager will create a Persistent Volume Claim | `true`
`alertmanager.persistentVolume.accessModes` | alertmanager data Persistent Volume access modes | `[ReadWriteOnce]`
`alertmanager.persistentVolume.annotations` | Annotations for alertmanager Persistent Volume Claim | `{}`
`alertmanager.persistentVolume.existingClaim` | alertmanager data Persistent Volume existing claim name | `""`
`alertmanager.persistentVolume.mountPath` | alertmanager data Persistent Volume mount root path | `/data`
`alertmanager.persistentVolume.size` | alertmanager data Persistent Volume size | `2Gi`
`alertmanager.persistentVolume.storageClass` | alertmanager data Persistent Volume Storage Class | `unset`
`alertmanager.persistentVolume.volumeBindingMode` | alertmanager data Persistent Volume Binding Mode | `unset`
`alertmanager.persistentVolume.subPath` | Subdirectory of alertmanager data Persistent Volume to mount | `""`
`alertmanager.podAnnotations` | annotations to be added to alertmanager pods | `{}`
`alertmanager.podLabels` | labels to be added to Prometheus AlertManager pods | `{}`
`alertmanager.podSecurityPolicy.annotations` | Specify pod annotations in the pod security policy | `{}` |
`alertmanager.replicaCount` | desired number of alertmanager pods | `1`
`alertmanager.statefulSet.enabled` | If true, use a statefulset instead of a deployment for pod management | `false`
`alertmanager.statefulSet.podManagementPolicy` | podManagementPolicy of alertmanager pods | `OrderedReady`
`alertmanager.statefulSet.headless.annotations` | annotations for alertmanager headless service | `{}`
`alertmanager.statefulSet.headless.labels` | labels for alertmanager headless service | `{}`
`alertmanager.statefulSet.headless.enableMeshPeer` | If true, enable the mesh peer endpoint for the headless service | `false`
`alertmanager.statefulSet.headless.servicePort` | alertmanager headless service port | `80`
`alertmanager.priorityClassName` | alertmanager priorityClassName | `nil`
`alertmanager.resources` | alertmanager pod resource requests & limits | `{}`
`alertmanager.securityContext` | Custom [security context](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) for Alert Manager containers | `{}`
`alertmanager.service.annotations` | annotations for alertmanager service | `{}`
`alertmanager.service.clusterIP` | internal alertmanager cluster service IP | `""`
`alertmanager.service.externalIPs` | alertmanager service external IP addresses | `[]`
`alertmanager.service.loadBalancerIP` | IP address to assign to load balancer (if supported) | `""`
`alertmanager.service.loadBalancerSourceRanges` | list of IP CIDRs allowed access to load balancer (if supported) | `[]`
`alertmanager.service.servicePort` | alertmanager service port | `80`
`alertmanager.service.sessionAffinity` | Session Affinity for alertmanager service, can be `None` or `ClientIP` | `None`
`alertmanager.service.type` | type of alertmanager service to create | `ClusterIP`
`alertmanager.strategy` | Deployment strategy | `{ "type": "RollingUpdate" }`
`alertmanagerFiles.alertmanager.yml` | Prometheus alertmanager configuration | example configuration
`configmapReload.prometheus.enabled` | If false, the configmap-reload container for Prometheus will not be deployed | `true`
`configmapReload.prometheus.name` | configmap-reload container name | `configmap-reload`
`configmapReload.prometheus.image.repository` | configmap-reload container image repository | `jimmidyson/configmap-reload`
`configmapReload.prometheus.image.tag` | configmap-reload container image tag | `v0.4.0`
`configmapReload.prometheus.image.pullPolicy` | configmap-reload container image pull policy | `IfNotPresent`
`configmapReload.prometheus.extraArgs` | Additional configmap-reload container arguments | `{}`
`configmapReload.prometheus.extraVolumeDirs` | Additional configmap-reload volume directories | `{}`
`configmapReload.prometheus.extraConfigmapMounts` | Additional configmap-reload configMap mounts | `[]`
`configmapReload.prometheus.resources` | configmap-reload pod resource requests & limits | `{}`
`configmapReload.alertmanager.enabled` | If false, the configmap-reload container for AlertManager will not be deployed | `true`
`configmapReload.alertmanager.name` | configmap-reload container name | `configmap-reload`
`configmapReload.alertmanager.image.repository` | configmap-reload container image repository | `jimmidyson/configmap-reload`
`configmapReload.alertmanager.image.tag` | configmap-reload container image tag | `v0.4.0`
`configmapReload.alertmanager.image.pullPolicy` | configmap-reload container image pull policy | `IfNotPresent`
`configmapReload.alertmanager.extraArgs` | Additional configmap-reload container arguments | `{}`
`configmapReload.alertmanager.extraVolumeDirs` | Additional configmap-reload volume directories | `{}`
`configmapReload.alertmanager.extraConfigmapMounts` | Additional configmap-reload configMap mounts | `[]`
`configmapReload.alertmanager.resources` | configmap-reload pod resource requests & limits | `{}`
`initChownData.enabled`  | If false, don't reset data ownership at startup | true
`initChownData.name` | init-chown-data container name | `init-chown-data`
`initChownData.image.repository` | init-chown-data container image repository | `busybox`
`initChownData.image.tag` | init-chown-data container image tag | `latest`
`initChownData.image.pullPolicy` | init-chown-data container image pull policy | `IfNotPresent`
`initChownData.resources` | init-chown-data pod resource requests & limits | `{}`
`kubeStateMetrics.enabled` | If true, create kube-state-metrics sub-chart, see the [kube-state-metrics chart for configuration options](https://github.com/nholuongut/Helmcharts/tree/master/stable/kube-state-metrics) | `true`
`kube-state-metrics` | [kube-state-metrics configuration options](https://github.com/nholuongut/Helmcharts/tree/master/stable/kube-state-metrics) | `Same as sub-chart's`
`nodeExporter.enabled` | If true, create node-exporter | `true`
`nodeExporter.name` | node-exporter container name | `node-exporter`
`nodeExporter.image.repository` | node-exporter container image repository| `prom/node-exporter`
`nodeExporter.image.tag` | node-exporter container image tag | `v1.0.1`
`nodeExporter.image.pullPolicy` | node-exporter container image pull policy | `IfNotPresent`
`nodeExporter.extraArgs` | Additional node-exporter container arguments | `{}`
`nodeExporter.extraInitContainers` | Init containers to launch alongside the node-exporter | `[]`
`nodeExporter.extraHostPathMounts` | Additional node-exporter hostPath mounts | `[]`
`nodeExporter.extraConfigmapMounts` | Additional node-exporter configMap mounts | `[]`
`nodeExporter.hostNetwork` | If true, node-exporter pods share the host network namespace | `true`
`nodeExporter.hostPID` | If true, node-exporter pods share the host PID namespace | `true`
`nodeExporter.nodeSelector` | node labels for node-exporter pod assignment | `{}`
`nodeExporter.podAnnotations` | annotations to be added to node-exporter pods | `{}`
`nodeExporter.pod.labels` | labels to be added to node-exporter pods | `{}`
`nodeExporter.podDisruptionBudget.enabled` | If true, create a PodDisruptionBudget | `false`
`nodeExporter.podDisruptionBudget.maxUnavailable` | Maximum unavailable instances in PDB | `1`
`nodeExporter.podSecurityPolicy.annotations` | Specify pod annotations in the pod security policy | `{}` |
`nodeExporter.podSecurityPolicy.enabled` | Specify if a Pod Security Policy for node-exporter must be created | `false`
`nodeExporter.tolerations` | node taints to tolerate (requires Kubernetes >=1.6) | `[]`
`nodeExporter.priorityClassName` | node-exporter priorityClassName | `nil`
`nodeExporter.resources` | node-exporter resource requests and limits (YAML) | `{}`
`nodeExporter.securityContext` | securityContext for containers in pod | `{}`
`nodeExporter.service.annotations` | annotations for node-exporter service | `{prometheus.io/scrape: "true"}`
`nodeExporter.service.clusterIP` | internal node-exporter cluster service IP | `None`
`nodeExporter.service.externalIPs` | node-exporter service external IP addresses | `[]`
`nodeExporter.service.hostPort` | node-exporter service host port | `9100`
`nodeExporter.service.loadBalancerIP` | IP address to assign to load balancer (if supported) | `""`
`nodeExporter.service.loadBalancerSourceRanges` | list of IP CIDRs allowed access to load balancer (if supported) | `[]`
`nodeExporter.service.servicePort` | node-exporter service port | `9100`
`nodeExporter.service.type` | type of node-exporter service to create | `ClusterIP`
`podSecurityPolicy.enabled` | If true, create & use pod security policies resources | `false`
`pushgateway.enabled` | If true, create pushgateway | `true`
`pushgateway.name` | pushgateway container name | `pushgateway`
`pushgateway.image.repository` | pushgateway container image repository | `prom/pushgateway`
`pushgateway.image.tag` | pushgateway container image tag | `v1.2.0`
`pushgateway.image.pullPolicy` | pushgateway container image pull policy | `IfNotPresent`
`pushgateway.extraArgs` | Additional pushgateway container arguments | `{}`
`pushgateway.extraInitContainers` | Init containers to launch alongside the pushgateway | `[]`
`pushgateway.ingress.enabled` | If true, pushgateway Ingress will be created | `false`
`pushgateway.ingress.annotations` | pushgateway Ingress annotations | `{}`
`pushgateway.ingress.hosts` | pushgateway Ingress hostnames | `[]`
`pushgateway.ingress.extraPaths` | Ingress extra paths to prepend to every pushgateway host configuration. Useful when configuring [custom actions with AWS ALB Ingress Controller](https://kubernetes-sigs.github.io/aws-alb-ingress-controller/guide/ingress/annotation/#actions) | `[]`
`pushgateway.ingress.tls` | pushgateway Ingress TLS configuration (YAML) | `[]`
`pushgateway.nodeSelector` | node labels for pushgateway pod assignment | `{}`
`pushgateway.podAnnotations` | annotations to be added to pushgateway pods | `{}`
`pushgateway.podSecurityPolicy.annotations` | Specify pod annotations in the pod security policy | `{}` |
`pushgateway.tolerations` | node taints to tolerate (requires Kubernetes >=1.6) | `[]`
`pushgateway.replicaCount` | desired number of pushgateway pods | `1`
`pushgateway.podDisruptionBudget.enabled` | If true, create a PodDisruptionBudget | `false`
`pushgateway.podDisruptionBudget.maxUnavailable` | Maximum unavailable instances in PDB | `1`
`pushgateway.schedulerName` | pushgateway alternate scheduler name | `nil`
`pushgateway.persistentVolume.enabled` | If true, Prometheus pushgateway will create a Persistent Volume Claim | `false`
`pushgateway.persistentVolume.accessModes` | Prometheus pushgateway data Persistent Volume access modes | `[ReadWriteOnce]`
`pushgateway.persistentVolume.annotations` | Prometheus pushgateway data Persistent Volume annotations | `{}`
`pushgateway.persistentVolume.existingClaim` | Prometheus pushgateway data Persistent Volume existing claim name | `""`
`pushgateway.persistentVolume.mountPath` | Prometheus pushgateway data Persistent Volume mount root path | `/data`
`pushgateway.persistentVolume.size` | Prometheus pushgateway data Persistent Volume size | `2Gi`
`pushgateway.persistentVolume.storageClass` | Prometheus pushgateway data Persistent Volume Storage Class |  `unset`
`pushgateway.persistentVolume.volumeBindingMode` | Prometheus pushgateway data Persistent Volume Binding Mode |  `unset`
`pushgateway.persistentVolume.subPath` | Subdirectory of Prometheus server data Persistent Volume to mount | `""`
`pushgateway.priorityClassName` | pushgateway priorityClassName | `nil`
`pushgateway.resources` | pushgateway pod resource requests & limits | `{}`
`pushgateway.service.annotations` | annotations for pushgateway service | `{}`
`pushgateway.service.clusterIP` | internal pushgateway cluster service IP | `""`
`pushgateway.service.externalIPs` | pushgateway service external IP addresses | `[]`
`pushgateway.service.loadBalancerIP` | IP address to assign to load balancer (if supported) | `""`
`pushgateway.service.loadBalancerSourceRanges` | list of IP CIDRs allowed access to load balancer (if supported) | `[]`
`pushgateway.service.servicePort` | pushgateway service port | `9091`
`pushgateway.service.type` | type of pushgateway service to create | `ClusterIP`
`pushgateway.strategy` | Deployment strategy | `{ "type": "RollingUpdate" }`
`rbac.create` | If true, create & use RBAC resources | `true`
`server.enabled` | If false, Prometheus server will not be created | `true`
`server.name` | Prometheus server container name | `server`
`server.image.repository` | Prometheus server container image repository | `prom/prometheus`
`server.image.tag` | Prometheus server container image tag | `v2.20.1`
`server.image.pullPolicy` | Prometheus server container image pull policy | `IfNotPresent`
`server.configPath` |  Path to a prometheus server config file on the container FS  | `/etc/config/prometheus.yml`
`server.global.scrape_interval` | How frequently to scrape targets by default | `1m`
`server.global.scrape_timeout` | How long until a scrape request times out | `10s`
`server.global.evaluation_interval` | How frequently to evaluate rules | `1m`
`server.remoteWrite` | The remote write feature of Prometheus allow transparently sending samples. | `[]`
`server.remoteRead` | The remote read feature of Prometheus allow transparently receiving samples. | `[]`
`server.extraArgs` | Additional Prometheus server container arguments | `{}`
`server.extraFlags` | Additional Prometheus server container flags | `["web.enable-lifecycle"]`
`server.extraInitContainers` | Init containers to launch alongside the server | `[]`
`server.prefixURL` | The prefix slug at which the server can be accessed |``
`server.baseURL` | The external url at which the server can be accessed | ``
`server.env` | Prometheus server environment variables | `[]`
`server.extraHostPathMounts` | Additional Prometheus server hostPath mounts | `[]`
`server.extraConfigmapMounts` | Additional Prometheus server configMap mounts | `[]`
`server.extraSecretMounts` | Additional Prometheus server Secret mounts | `[]`
`server.extraVolumeMounts` | Additional Prometheus server Volume mounts | `[]`
`server.extraVolumes` | Additional Prometheus server Volumes | `[]`
`server.configMapOverrideName` | Prometheus server ConfigMap override where full-name is `{{.Release.Name}}-{{.Values.server.configMapOverrideName}}` and setting this value will prevent the default server ConfigMap from being generated | `""`
`server.ingress.enabled` | If true, Prometheus server Ingress will be created | `false`
`server.ingress.annotations` | Prometheus server Ingress annotations | `[]`
`server.ingress.extraLabels` | Prometheus server Ingress additional labels | `{}`
`server.ingress.hosts` | Prometheus server Ingress hostnames | `[]`
`server.ingress.extraPaths` | Ingress extra paths to prepend to every Prometheus server host configuration. Useful when configuring [custom actions with AWS ALB Ingress Controller](https://kubernetes-sigs.github.io/aws-alb-ingress-controller/guide/ingress/annotation/#actions) | `[]`
`server.ingress.tls` | Prometheus server Ingress TLS configuration (YAML) | `[]`
`server.nodeSelector` | node labels for Prometheus server pod assignment | `{}`
`server.tolerations` | node taints to tolerate (requires Kubernetes >=1.6) | `[]`
`server.affinity` | pod affinity | `{}`
`server.podDisruptionBudget.enabled` | If true, create a PodDisruptionBudget | `false`
`server.podDisruptionBudget.maxUnavailable` | Maximum unavailable instances in PDB | `1`
`server.priorityClassName` | Prometheus server priorityClassName | `nil`
`server.enableServiceLinks` | Set service environment variables in Prometheus server pods | `true`
`server.schedulerName` | Prometheus server alternate scheduler name | `nil`
`server.persistentVolume.enabled` | If true, Prometheus server will create a Persistent Volume Claim | `true`
`server.persistentVolume.accessModes` | Prometheus server data Persistent Volume access modes | `[ReadWriteOnce]`
`server.persistentVolume.annotations` | Prometheus server data Persistent Volume annotations | `{}`
`server.persistentVolume.existingClaim` | Prometheus server data Persistent Volume existing claim name | `""`
`server.persistentVolume.mountPath` | Prometheus server data Persistent Volume mount root path | `/data`
`server.persistentVolume.size` | Prometheus server data Persistent Volume size | `8Gi`
`server.persistentVolume.storageClass` | Prometheus server data Persistent Volume Storage Class |  `unset`
`server.persistentVolume.volumeBindingMode` | Prometheus server data Persistent Volume Binding Mode | `unset`
`server.persistentVolume.subPath` | Subdirectory of Prometheus server data Persistent Volume to mount | `""`
`server.emptyDir.sizeLimit` | emptyDir sizeLimit if a Persistent Volume is not used | `""`
`server.podAnnotations` | annotations to be added to Prometheus server pods | `{}`
`server.podLabels` | labels to be added to Prometheus server pods | `{}`
`server.alertmanagers` | Prometheus AlertManager configuration for the Prometheus server | `{}`
`server.deploymentAnnotations` | annotations to be added to Prometheus server deployment | `{}`
`server.podSecurityPolicy.annotations` | Specify pod annotations in the pod security policy | `{}` |
`server.replicaCount` | desired number of Prometheus server pods | `1`
`server.statefulSet.enabled` | If true, use a statefulset instead of a deployment for pod management | `false`
`server.statefulSet.annotations` | annotations to be added to Prometheus server stateful set | `{}`
`server.statefulSet.labels` | labels to be added to Prometheus server stateful set | `{}`
`server.statefulSet.podManagementPolicy` | podManagementPolicy of server pods | `OrderedReady`
`server.statefulSet.headless.annotations` | annotations for Prometheus server headless service | `{}`
`server.statefulSet.headless.labels` | labels for Prometheus server headless service | `{}`
`server.statefulSet.headless.servicePort` | Prometheus server headless service port | `80`
`server.statefulSet.headless.gRPC.enabled` | If true, open a second port on the service for gRPC | `false`
`server.statefulSet.headless.gRPC.servicePort` | Prometheus service gRPC port, (ignored if `server.service.gRPC.enabled` is not `true`) | `10901`
`server.statefulSet.headless.gRPC.nodePort` | Port to be used as gRPC nodePort in the prometheus service | `0`
`server.readinessProbeInitialDelay` | the initial delay for the Prometheus server readiness probe | `30`
`server.readinessProbePeriodSeconds` | how often (in seconds) to perform the Prometheus server readiness probe | `5`
`server.readinessProbeTimeout` | the timeout for the Prometheus server readiness probe | `30`
`server.readinessProbeFailureThreshold` | the failure threshold for the Prometheus server readiness probe | `3`
`server.readinessProbeSuccessThreshold` | the success threshold for the Prometheus server readiness probe | `1`
`server.livenessProbeInitialDelay` | the initial delay for the Prometheus server liveness probe | `30`
`server.livenessProbePeriodSeconds` | how often (in seconds) to perform the Prometheus server liveness probe | `15`
`server.livenessProbeTimeout` | the timeout for the Prometheus server liveness probe  | `30`
`server.livenessProbeFailureThreshold` | the failure threshold for the Prometheus server liveness probe | `3`
`server.livenessProbeSuccessThreshold` | the success threshold for the Prometheus server liveness probe | `1`
`server.resources` | Prometheus server resource requests and limits | `{}`
`server.verticalAutoscaler.enabled` | If true a VPA object will be created for the controller (either StatefulSet or Deployemnt, based on above configs) | `false`
`server.securityContext` | Custom [security context](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) for server containers | `{}`
`server.service.annotations` | annotations for Prometheus server service | `{}`
`server.service.clusterIP` | internal Prometheus server cluster service IP | `""`
`server.service.externalIPs` | Prometheus server service external IP addresses | `[]`
`server.service.loadBalancerIP` | IP address to assign to load balancer (if supported) | `""`
`server.service.loadBalancerSourceRanges` | list of IP CIDRs allowed access to load balancer (if supported) | `[]`
`server.service.nodePort` | Port to be used as the service NodePort (ignored if `server.service.type` is not `NodePort`) | `0`
`server.service.servicePort` | Prometheus server service port | `80`
`server.service.sessionAffinity` | Session Affinity for server service, can be `None` or `ClientIP` | `None`
`server.service.type` | type of Prometheus server service to create | `ClusterIP`
`server.service.gRPC.enabled` | If true, open a second port on the service for gRPC | `false`
`server.service.gRPC.servicePort` | Prometheus service gRPC port, (ignored if `server.service.gRPC.enabled` is not `true`) | `10901`
`server.service.gRPC.nodePort` | Port to be used as gRPC nodePort in the prometheus service | `0`
`server.service.statefulsetReplica.enabled` | If true, send the traffic from the service to only one replica of the replicaset | `false`
`server.service.statefulsetReplica.replica` | Which replica to send the traffice to | `0`
`server.hostAliases` | /etc/hosts-entries in container(s) | []
`server.sidecarContainers` | array of snippets with your sidecar containers for prometheus server | `""`
`server.strategy` | Deployment strategy | `{ "type": "RollingUpdate" }`
`serviceAccounts.alertmanager.create` | If true, create the alertmanager service account | `true`
`serviceAccounts.alertmanager.name` | name of the alertmanager service account to use or create | `{{ prometheus.alertmanager.fullname }}`
`serviceAccounts.alertmanager.annotations` | annotations for the alertmanager service account | `{}`
`serviceAccounts.nodeExporter.create` | If true, create the nodeExporter service account | `true`
`serviceAccounts.nodeExporter.name` | name of the nodeExporter service account to use or create | `{{ prometheus.nodeExporter.fullname }}`
`serviceAccounts.nodeExporter.annotations` | annotations for the nodeExporter service account | `{}`
`serviceAccounts.pushgateway.create` | If true, create the pushgateway service account | `true`
`serviceAccounts.pushgateway.name` | name of the pushgateway service account to use or create | `{{ prometheus.pushgateway.fullname }}`
`serviceAccounts.pushgateway.annotations` | annotations for the pushgateway service account | `{}`
`serviceAccounts.server.create` | If true, create the server service account | `true`
`serviceAccounts.server.name` | name of the server service account to use or create | `{{ prometheus.server.fullname }}`
`serviceAccounts.server.annotations` | annotations for the server service account | `{}`
`server.terminationGracePeriodSeconds` | Prometheus server Pod termination grace period | `300`
`server.retention` | (optional) Prometheus data retention | `"15d"`
`serverFiles.alerts` | (Deprecated) Prometheus server alerts configuration | `{}`
`serverFiles.rules` | (Deprecated) Prometheus server rules configuration | `{}`
`serverFiles.alerting_rules.yml` | Prometheus server alerts configuration | `{}`
`serverFiles.recording_rules.yml` | Prometheus server rules configuration | `{}`
`serverFiles.prometheus.yml` | Prometheus server scrape configuration | example configuration
`extraScrapeConfigs` | Prometheus server additional scrape configuration | ""
`alertRelabelConfigs` | Prometheus server [alert relabeling configs](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#alert_relabel_configs) for H/A prometheus | ""
`networkPolicy.enabled` | Enable NetworkPolicy | `false`
`forceNamespace` | Force resources to be namespaced | `null` |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
helm install stable/prometheus --name my-release \
    --set server.terminationGracePeriodSeconds=360
```

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example,

```console
helm install stable/prometheus --name my-release -f values.yaml
```

> **Tip**: You can use the default [values.yaml](values.yaml)

Note that you have multiple yaml files. This is particularly useful when you have alerts belonging to multiple services in the cluster. For example,

```yaml
# values.yaml
# ...

# service1-alert.yaml
serverFiles:
  alerts:
    service1:
      - alert: anAlert
      # ...

# service2-alert.yaml
serverFiles:
  alerts:
    service2:
      - alert: anAlert
      # ...
```

```console
helm install stable/prometheus --name my-release -f values.yaml -f service1-alert.yaml -f service2-alert.yaml
```

### RBAC Configuration

Roles and RoleBindings resources will be created automatically for `server` service.

To manually setup RBAC you need to set the parameter `rbac.create=false` and specify the service account to be used for each service by setting the parameters: `serviceAccounts.{{ component }}.create` to `false` and `serviceAccounts.{{ component }}.name` to the name of a pre-existing service account.

> **Tip**: You can refer to the default `*-clusterrole.yaml` and `*-clusterrolebinding.yaml` files in [templates](templates/) to customize your own.

### ConfigMap Files

AlertManager is configured through [alertmanager.yml](https://prometheus.io/docs/alerting/configuration/). This file (and any others listed in `alertmanagerFiles`) will be mounted into the `alertmanager` pod.

Prometheus is configured through [prometheus.yml](https://prometheus.io/docs/operating/configuration/). This file (and any others listed in `serverFiles`) will be mounted into the `server` pod.

### Ingress TLS

If your cluster allows automatic creation/retrieval of TLS certificates (e.g. [kube-lego](https://github.com/jetstack/kube-lego)), please refer to the documentation for that mechanism.

To manually configure TLS, first create/retrieve a key & certificate pair for the address(es) you wish to protect. Then create a TLS secret in the namespace:

```console
kubectl create secret tls prometheus-server-tls --cert=path/to/tls.cert --key=path/to/tls.key
```

Include the secret's name, along with the desired hostnames, in the alertmanager/server Ingress TLS section of your custom `values.yaml` file:

```yaml
server:
  ingress:
    ## If true, Prometheus server Ingress will be created
    ##
    enabled: true

    ## Prometheus server Ingress hostnames
    ## Must be provided if Ingress is enabled
    ##
    hosts:
      - prometheus.domain.com

    ## Prometheus server Ingress TLS configuration
    ## Secrets must be manually created in the namespace
    ##
    tls:
      - secretName: prometheus-server-tls
        hosts:
          - prometheus.domain.com
```

### NetworkPolicy

Enabling Network Policy for Prometheus will secure connections to Alert Manager
and Kube State Metrics by only accepting connections from Prometheus Server.
All inbound connections to Prometheus Server are still allowed.

To enable network policy for Prometheus, install a networking plugin that
implements the Kubernetes NetworkPolicy spec, and set `networkPolicy.enabled` to true.

If NetworkPolicy is enabled for Prometheus' scrape targets, you may also need
to manually create a networkpolicy which allows it.
