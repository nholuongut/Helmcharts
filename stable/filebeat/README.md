# ⚠️ Repo Archive Notice

As of Nov 13, 2020, charts in this repo will no longer be updated.
For more information, see the Helm Charts [Deprecation and Archive Notice](https://github.com/nholuongut/Helmcharts#%EF%B8%8F-deprecation-and-archive-notice), and [Update](https://helm.sh/blog/charts-repo-deprecation/).

# Filebeat

[filebeat](https://www.elastic.co/guide/en/beats/filebeat/current/index.html) is used to ship Kubernetes and host logs to multiple outputs.

## DEPRECATION NOTICE

This chart is deprecated and no longer supported.

## Prerequisites

- Kubernetes 1.10+
- Filebeat 7.4.0+
  * If wanting to use a older version of filebeat you will need  to modify the configuration to add back in the `in_cluster` configuration see [this](https://github.com/nholuongut/Helmcharts/pull/17876/files) pull request.

## Note


By default this chart only ships a single output to a file on the local system.  Users should set config.output.file.enabled=false and configure their own outputs as [documented](https://www.elastic.co/guide/en/beats/filebeat/current/configuring-output.html)

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release stable/filebeat
```

## Configuration

The following table lists the configurable parameters of the filebeat chart and their default values.

| Parameter                                                | Description                                                                                              | Default                                            |
| -------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| `image.repository`                                       | Docker image repo                                                                                        | `docker.elastic.co/beats/filebeat-oss`             |
| `image.tag`                                              | Docker image tag                                                                                         | `7.4.0`                                            |
| `image.pullPolicy`                                       | Docker image pull policy                                                                                 | `IfNotPresent`                                     |
| `image.pullSecrets`                                      | Specify image pull secrets                                                                               | `nil`                                              |
| `config.filebeat.config.modules.path`                    |                                                                                                          | `${path.config}/modules.d/*.yml`                   |
| `config.filebeat.config.modules.reload.enabled`          | Reload module configs as they change                                                                     | `false`                                            |
| `config.processors`                                      |                                                                                                          | `- add_cloud_metadata`                             |
| `config.filebeat.inputs`                            |                                                                                                          | see values.yaml                                    |
| `config.output.file.path`                                |                                                                                                          | `"/usr/share/filebeat/data"`                       |
| `config.output.file.filename`                            |                                                                                                          | `filebeat`                                         |
| `config.output.file.rotate_every_kb`                     |                                                                                                          | `10000`                                            |
| `config.output.file.number_of_files`                     |                                                                                                          | `5`                                                |
| `config.http.enabled`                                    |                                                                                                          | `false`                                            |
| `config.http.port`                                       |                                                                                                          | `5066`                                             |
| `overrideConfig`                                         | If overrideConfig is not empty, filebeat chart's default config won't be used at all.                    | `{}`                                               |
| `data.hostPath`                                          | Path on the host to mount to `/usr/share/filebeat/data` in the container.                                | `/var/lib/filebeat`                                               |
| `indexTemplateLoad`                                      | List of Elasticsearch hosts to load index template, when logstash output is used                         | `[]`                                               |
| `command`                                                | Custom command (Docker Entrypoint)                                                                       | `[]`                                               |
| `args`                                                   | Custom args (Docker Cmd)                                                                                 | `[]`                                               |
| `plugins`                                                | List of beat plugins                                                                                     | `[]`                                               |
| `extraVars`                                              | A list of additional environment variables                                                                | `[]`                                              |
| `extraVolumes`                                           | Add additional volumes                                                                                   | `[]`                                               |
| `extraVolumeMounts`                                      | Add additional mounts                                                                                    | `[]`                                               |
| `extraInitContainers`                                    | Add additional initContainers                                                                            | `[]`                                               |
| `extraSecrets`                                           | Add additional secrets                                                                                   | `{}`                                               |
| `resources`                                              |                                                                                                          | `{}`                                               |
|`priorityClassName`                                       | priorityClassName                                                                                        | `nil`                                              |
| `nodeSelector`                                           |                                                                                                          | `{}`                                               |
| `annotations`                                            |                                                                                                          | `{}`                                               |
| `tolerations`                                            |                                                                                                          | `[]`                                               |
| `affinity`                                               |                                                                                                          | `{}`                                               |
| `rbac.create`                                            | Specifies whether RBAC resources should be created                                                       | `true`                                             |
| `serviceAccount.create`                                  | Specifies whether a ServiceAccount should be created                                                     | `true`                                             |
| `serviceAccount.name`                                    | the name of the ServiceAccount to use                                                                     | `""`                                               |
| `podSecurityPolicy.enabled`                              | Should the PodSecurityPolicy be created. Depends on `rbac.create` being set to `true`.                                                                     | `false`                                               |
| `podSecurityPolicy.annotations`                                    | Annotations to be added to the created PodSecurityPolicy:                                                                    | `""`                                               |
| `privileged`                                             | Specifies wheter to run as privileged                                                                    | `false`                                            |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name my-release -f values.yaml stable/filebeat
```
