# Spring Cloud Data Flow Chart

[Spring Cloud Data Flow](https://cloud.spring.io/spring-cloud-dataflow/) is a toolkit for microservices-based Streaming and Batch data processing pipelines in Cloud Foundry and Kubernetes

Data processing pipelines consist of [Spring Boot](https://projects.spring.io/spring-boot/) apps, built using the [Spring Cloud Stream](https://cloud.spring.io/spring-cloud-stream/) or [Spring Cloud Task](https://cloud.spring.io/spring-cloud-task/) microservice frameworks. This makes Spring Cloud Data Flow suitable for a range of data processing use cases, from import/export to event streaming and predictive analytics.

## This Helm chart is deprecated

Given the [`stable` deprecation timeline](https://github.com/nholuongut/Helmcharts#deprecation-timeline), the Bitnami maintained Spring Cloud Data Flow Helm chart is now located at [bitnami/charts](https://github.com/bitnami/charts/).

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

## Chart Details
This chart will provision a fully functional and fully featured Spring Cloud Data Flow installation
that can deploy and manage data processing pipelines in the cluster that it is deployed to.

Either the default MySQL deployment or an external database can be used as the data store for Spring Cloud Data Flow state and either RabbitMQ or Kafka can be used as the messaging layer for streaming apps to communicate with one another.

For more information on Spring Cloud Data Flow and its capabilities, see it's [documentation](https://dataflow.spring.io/).

## Prerequisites

Assumes that serviceAccount credentials are available so the deployed Data Flow server can access the API server (Works on GKE and Minikube by default). See [Configure Service Accounts for Pods](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release stable/spring-cloud-data-flow
```

If you are using a cluster that does not have a load balancer (like Minikube) then you can install using a NodePort:

```bash
$ helm install --name my-release --set server.service.type=NodePort stable/spring-cloud-data-flow
```

To restrict the load balancer to an IP address range:

```bash
$ helm install --name my-release  --set server.service.loadBalancerSourceRanges='[10.0.0.0/8]' stable/spring-cloud-data-flow
```


### Data Store

By default, MySQL is deployed with this chart. However, if you wish to use an external database, please use the following `set` flags to the `helm` command to disable MySQL deployment, for example:

`--set mysql.enabled=false`

In addition, you are required to set all fields listed in [External Database Configuration](#external-database-configuration).

### Messaging Layer

There are three messaging layers available in this chart:
- RabbitMQ (default)
- RabbitMQ HA
- Kafka

To change the messaging layer to a highly available (HA) version of RabbitMQ, use the following `set` flags to the `helm` command, for example:

`--set rabbitmq-ha.enabled=true,rabbitmq.enabled=false`

Alternatively, to change the messaging layer to Kafka, use the following `set` flags to the `helm` command, for example:

`--set kafka.enabled=true,rabbitmq.enabled=false`

Only one messaging layer can be used at a given time. If RabbitMQ and Kafka are enabled, both charts will be installed with RabbitMQ being used in the deployment.

Note that this chart pulls in many different Docker images so can take a while to fully install.

### Feature Toggles

If you only need to deploy tasks and schedules, streams can be disabled:

`--set features.streaming.enabled=false --set rabbitmq.enabled=false`

If you only need to deploy streams, tasks and schedules can be disabled:

`--set features.batch.enabled=false`

NOTE: Both `features.streaming.enabled` and `features.batch.enabled` should not be set to `false` at the same time.

Streaming and batch applications can be monitored through Prometheus and Grafana. To deploy these components and enable monitoring, set the following:

`--set features.monitoring.enabled=true`

When using Minikube, the Grafana URL can be obtained for example, via:

`minikube service my-release-grafana --url`

On a platform that provides a LoadBalancer such as GKE, the following can be checked against until the `EXTERNAL-IP` field is populated with the assigned load balancer IP address:

`kubectl get svc my-release-grafana`

See the Grafana table below for default credentials and override parameters.

### Using an Ingress

If you would like to use an Ingress instead of having the services use the `LoadBalancer` type there are a few things to consider.

First you need to have an [Ingress Controller](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) installed in your cluster. If you don't already have one instaled, you can use the following helm command to install an NGINX Ingress Controller:

```bash
kubectl create namespace nginx-ingress
helm install --name nginx-ingress --namespace nginx-ingress stable/nginx-ingress
```

You can look up the IP address used by the NGINX Ingress Controller with:

```bash
ingress=$(kubectl get svc nginx-ingress-controller -n nginx-ingress -ojsonpath='{.status.loadBalancer.ingress[0].ip}')
```

This is useful if you would like to use `xip.io` instead of your own DNS resolution. The folowing options assume that you will use `xip.io` but you can replace the host values below with your own DNS hosts if you prefer.

To enable the creation of an `Ingress` resource and configure the services to use `ClusterIP` type use the following set options in your helm install command:

```bash
  --set server.service.type=ClusterIP \
  --set ingress.enabled=true \
  --set ingress.protocol=http \
  --set ingress.server.host=scdf.${ingress}.xip.io \
```

If you want to use an `Ingress` with the monitoring feature enabled, then use thes options instead:

```bash
  --set features.monitoring.enabled=true \
  --set server.service.type=ClusterIP \
  --set grafana.service.type=ClusterIP \
  --set prometheus.proxy.service.type=ClusterIP \
  --set ingress.enabled=true \
  --set ingress.protocol=http \
  --set ingress.server.host=scdf.${ingress}.xip.io \
  --set ingress.grafana.host=grafana.${ingress}.xip.io \
```

## Configuration

The following tables list the configurable parameters and their default values.

### RBAC Configuration

| Parameter     | Description                 | Default                   |
| ------------- | --------------------------- | ------------------------- |
| rbac.create   | Create RBAC configurations  | true

### ServiceAccount Configuration

| Parameter             | Description            | Default                     |
| --------------------- | ---------------------- | --------------------------- |
| serviceAccount.create | Create ServiceAccount  | true
| serviceAccount.name   | ServiceAccount name    | (generated if not specified)

### Data Flow Server Configuration

| Parameter                                                                                 | Description                                                                  | Default          |
| ----------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- | ---------------- |
| server.version                                                                            | The version/tag of the Data Flow server                                      | 2.6.0
| server.imagePullPolicy                                                                    | The imagePullPolicy of the Data Flow server                                  | IfNotPresent
| server.service.type                                                                       | The service type for the Data Flow server                                    | LoadBalancer
| server.service.annotations                                                                | Extra annotations for service resource                                       | {}
| server.service.externalPort                                                               | The external port for the Data Flow server                                   | 80
| server.service.labels                                                                     | Extra labels for the service resource                                        | {}
| server.service.loadBalancerSourceRanges                                                   | A list of IP address ranges to allow through the load balancer               | no restriction
| server.platformName                                                                       | The name of the configured platform account                                  | default
| server.configMap                                                                          | Custom ConfigMap name for Data Flow server configuration                     |
| server.trustCerts                                                                         | Trust self signed certs                                                      | false
| server.extraEnv                                                                           | Extra environment variables to add to the server container                   | {}
| server.containerConfiguration.container.registry-configurations.<NAME>.registry-host      | The registry host to use for the profile represented by <NAME>               |
| server.containerConfiguration.container.registry-configurations.<NAME>.authorization-type | The registry authorization type to use for the profile represented by <NAME> |

### Skipper Server Configuration

| Parameter                         | Description                                                      | Default          |
| --------------------------------- | ---------------------------------------------------------------- | ---------------- |
| skipper.version                   | The version/tag of the Skipper server                            | 2.5.0
| skipper.imagePullPolicy           | The imagePullPolicy of the Skipper server                        | IfNotPresent
| skipper.platformName              | The name of the configured platform account                      | default
| skipper.service.type              | The service type for the Skipper server                          | ClusterIP
| skipper.service.annotations       | Extra annotations for service resources                          | {}
| skipper.service.labels            | Extra labels for the service resource                            | {}
| skipper.configMap                 | Custom ConfigMap name for Skipper server configuration           |
| skipper.trustCerts                | Trust self signed certs                                          | false
| skipper.extraEnv                  | Extra environment variables to add to the skipper container      | {}

### Spring Cloud Deployer for Kubernetes Configuration

| Parameter                                   | Description                            | Default                   |
| ------------------------------------------- | -------------------------------------- | ------------------------- |
| deployer.resourceLimits.cpu                 | Deployer resource limit for cpu        | 500m
| deployer.resourceLimits.memory              | Deployer resource limit for memory     | 1024Mi
| deployer.readinessProbe.initialDelaySeconds | Deployer readiness probe initial delay | 120
| deployer.livenessProbe.initialDelaySeconds  | Deployer liveness probe initial delay  | 90

### RabbitMQ Configuration

| Parameter                   | Description                                 | Default                   |
| --------------------------- | ------------------------------------------- | ------------------------- |
| rabbitmq.enabled            | Enable RabbitMQ as the middleware to use    | true
| rabbitmq.rabbitmq.username  | RabbitMQ user name                          | user
| rabbitmq.rabbitmq.password  | RabbitMQ password to encode into the secret | changeme

### RabbitMQ HA Configuration

| Parameter                     | Description                                 | Default                   |
| ----------------------------- | ------------------------------------------- | ------------------------- |
| rabbitmq-ha.enabled           | Enable RabbitMQ HA as the middleware to use | false
| rabbitmq-ha.rabbitmqUsername  | RabbitMQ user name                          | user

### Kafka Configuration

| Parameter                    | Description                               | Default                                     |
| ---------------------------- | ----------------------------------------- | ------------------------------------------- |
| kafka.enabled                | Enable RabbitMQ as the middleware to use  | false
| kafka.replicas               | The number of Kafka replicas to use       | 1
| kafka.configurationOverrides | Kafka deployment configuration overrides  | replication.factor=1, metrics.enabled=false
| kafka.zookeeper.replicaCount | The number of ZooKeeper replicates to use | 1

### MySQL Configuration

| Parameter                  | Description                  | Default                   |
| -------------------------- | ---------------------------- | ------------------------- |
| mysql.enabled              | Enable deployment of MySQL   | true
| mysql.mysqlDatabase        | MySQL database name          | dataflow

### External Database Configuration

| Parameter           | Description                    | Default                   |
| ------------------- | ------------------------------ | ------------------------- |
| database.driver     | Database driver                | nil
| database.scheme     | Database scheme                | nil
| database.host       | Database host                  | nil
| database.port       | Database port                  | nil
| database.user       | Database user                  | scdf
| database.password   | Database password              | nil
| database.dataflow   | Database name for SCDF server  | dataflow
| database.skipper    | Database name for SCDF skipper | skipper

### Feature Toggles

| Parameter                    | Description                             | Default                   |
| ---------------------------- | --------------------------------------- | ------------------------- |
| features.streaming.enabled   | Enables or disables streams             | true
| features.batch.enabled       | Enables or disables tasks and schedules | true
| features.monitoring.enabled  | Enables or disables monitoring          | false

### Ingress

| Parameter            | Description                              | Default                   |
| -------------------- | ---------------------------------------- | ------------------------- |
| ingress.enabled      | Enables or disables ingress support      | true
| ingress.protocol     | Sets the protocol used by ingress server | https
| ingress.server.host  | Sets the host used for server            | data-flow.local
| ingress.server.host  | Sets the host used for grafana           | grafana.local

### Grafana

| Parameter                            | Description                                                  | Default                    |
| ------------------------------------ | ------------------------------------------------------------ | -------------------------- |
| grafana.service.type                 | Service type to use                                          | LoadBalancer
| grafana.admin.existingSecret         | Existing Secret to use for login credentials                 | scdf-grafana-secret
| grafana.admin.userKey                | Secret userKey field                                         | admin-user
| grafana.admin.passwordKey            | Secret passwordKey field                                     | admin-password
| grafana.admin.defaultUsername        | The default base64 encoded login username used in the secret | admin
| grafana.admin.defaultPassword        | The default base64 encoded login password used in the secret | password
| grafana.extraConfigmapMounts         | ConfigMap mount for datasources                              | scdf-grafana-ds-cm
| grafana.dashboardProviders           | Dashboard provider for imported dashboards                   | default
| grafana.dashboards                   | Dashboards to auto import                                    | SCDF Apps, Streams & Tasks

### Prometheus
| Parameter                                    | Description                                        | Default                                |
| -------------------------------------------- | -------------------------------------------------- | -------------------------------------- |
| prometheus.server.global.scrape_interval     | Scrape interval                                    | 10s
| prometheus.server.global.scrape_timeout      | Scrape timeout                                     | 9s
| prometheus.server.global.evaluation_interval | Evaluation interval                                | 10s
| prometheus.extraScrapeConfigs                | Additional scrape configs for proxied applications | `proxied-applications` & `proxies` jobs
| prometheus.podSecurityPolicy                 | Enable or disable PodSecurityContext               | true
| prometheus.alertmanager                      | Enable or disable alert manager                    | false
| prometheus.kubeStateMetrics                  | Enable or disable kube state metrics               | false
| prometheus.nodeExporter                      | Enable or disable node exporter                    | false
| prometheus.pushgateway                       | Enable or disable push gateway                     | false
| prometheus.proxy.service.type                | Service type to use                                | LoadBalancer
