# ⚠️ Repo Archive Notice

As of Nov 13, 2020, charts in this repo will no longer be updated.
For more information, see the Helm Charts [Deprecation and Archive Notice](https://github.com/nholuongut/Helmcharts#%EF%B8%8F-deprecation-and-archive-notice), and [Update](https://helm.sh/blog/charts-repo-deprecation/).

# Burrow

[Burrow](https://github.com/hyperledger/burrow) is a permissioned Ethereum smart-contract blockchain node which provides transaction finality and high transaction throughput 
on a proof-of-stake [Tendermint](https://tendermint.com) consensus engine.

## DEPRECATION NOTICE

This chart is deprecated and no longer supported.

## Introduction

This chart bootstraps a burrow network on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Installation

### Prerequisites

To deploy a new blockchain network, this chart requires that two objects be present in the same Kubernetes namespace: a configmap should house the genesis file and each node should 
have a secret to hold any validator keys. The provided script, `addresses.sh` automatically provisions a number of files using the [burrow](https://github.com/hyperledger/burrow) toolkit, 
so please first ensure that `burrow --version` matches the `image.tag` in the [configuration](#configuration). This sequence also requires that the [jq](https://stedolan.github.io/jq/) binary 
is installed. Two files will be generated, the first of note is `setup.yaml` which contains the two necessary Kubernetes specifications to be added to the cluster:

```bash
curl -LO https://raw.githubusercontent.com/nholuongut/Helmcharts/master/stable/burrow/initialize.sh
CHAIN_NODES=4 CHAIN_NAME="my-release-burrow" ./initialize.sh
kubectl apply --filename setup.yaml
```

Please note that the variable `$CHAIN_NAME` should be the same as the helm release name specified below with the `-burrow` suffix. 
Another file, `addresses.yaml` contains the the equivalent validator addresses to set in the charts.

### Deployment

To install the chart with the release name `my-release` with the set of custom validator addresses:

```bash
helm install stable/burrow --name my-release --values addresses.yaml
```

The [configuration](#configuration) section below lists all possible parameters that can be configured during installation. Please also see the [runtime configuration](#runtime) 
section for more information on how to setup your network properly.

## Uninstall

To uninstall/delete the `my-release` deployment:

```console
$ helm delete my-release
```

This command removes all the Kubernetes components associated with the chart and deletes the release. To remove the configmap and secret created in the [prerequisites](#prerequisites), follow these steps:

```bash
kubectl delete secret ${CHAIN_NAME}-keys
kubectl delete configmap ${CHAIN_NAME}-genesis
```

## Configuration

The following table lists the configurable parameters of the Burrow chart and its default values.

| Parameter | Description | Default |
| --------- | ----------- | ------- |
| `affinity` | node/pod affinities | `{}` |
| `chain.nodes` | number of nodes for the blockchain network | `1` |
| `chain.logLevel` | log level for the nodes (`debug`, `info`, `warn`) | `"info"` |
| `chain.extraSeeds` | network seeds to dial in addition to the cluster booted by the chart; each entry in the array should be in the form `ip:port` (note: because P2P connects over tcp, the port is absolutely required) | `[]` |
| `chain.restore.enabled` | toggle chain restore mechanism | `false` |
| `chain.restore.dumpURL` | accessible dump file from absolute url | `""` |
| `chain.testing` | toggle pre-generated keys & genesis for ci testing | `false` |
| `config` | the [burrow configuration file](https://github.com/hyperledger/burrow/blob/develop/tests/chain/burrow.toml) | `{}` |
| `config.Tendermint.ListenPort` | peer port | `26656` |
| `contracts.enabled` | toggle post-install contract deployment | `false` |
| `contracts.image` | contract deployer image | `""` |
| `contracts.tag` | contract deployer tag | `""` |
| `contracts.deploy` | command to run in post-install hook | `""` |
| `env` | environment variables to configure burrow | `{}` |
| `extraArgs` | extra arguments to give to the build in `burrow start` command | `{}` |
| `image.repository` | image repository | `"hyperledger/burrow"` |
| `image.tag` | image tag | `"0.29.0"` |
| `image.pullPolicy` | image pull policy | `"IfNotPresent"` |
| `livenessProbe.enabled` | enable liveness checks | `true` |
| `livenessProbe.path` | http endpoint | `"/status?block_seen_time_within=3m"` |
| `livenessProbe.initialDelaySeconds` | start after | `240` |
| `livenessProbe.timeoutSeconds` | retry after | `1` |
| `livenessProbe.periodSeconds` | check every | `30` |
| `nodeSelector` | node labels for pod assignment | `{}` |
| `organization` | name of the organization running these nodes (used in the peer's moniker) | `""` |
| `persistence.enabled` | enable pvc for the chain data | `true` |
| `persistence.size` | size of the chain data pvc | `"80Gi"` |
| `persistence.storageClass` | storage class for the chain data pvc | `"standard"` |
| `persistence.accessMode` | access mode for the chain data pvc | `"ReadWriteOnce"` |
| `persistence.persistentVolumeReclaimPolicy` | does not delete on node restart | `"Retain"` |
| `podAnnotations` | annotations to add to each pod | `{}` |
| `podLabels` | labels to add to each pod | `{}` |
| `readinessProbe.enabled` | enable readiness checks | `true` |
| `readinessProbe.path` | http endpoint | `"/status"` |
| `readinessProbe.initialDelaySeconds` | start after | `5` |
| `resources.limits.cpu` | - | `"500m"` |
| `resources.limits.memory` | - | `"1Gi"` |
| `resources.requests.cpu` | - | `"100m"` |
| `resources.requests.memory` | - | `"256Mi"` |
| `grpc.service.type` | service type | `"ClusterIP"` |
| `grpc.service.loadBalance` | enable load balancing across nodes | `true` |
| `grpc.ingress.enabled` | expose port | `false` |
| `grpc.ingress.hosts` | - | `[]` |
| `grpc.ingress.annotations` | extra annotations | `` |
| `grpc.ingress.tls` | - | `` |
| `info.service.type` | service type | `"ClusterIP"` |
| `info.service.loadBalance` | enable load balancing across nodes | `true` |
| `info.ingress.enabled` | expose port | `false` |
| `info.ingress.partial` | exposes the `/accounts` and `/blocks` paths externally | `false` |
| `info.ingress.pathLeader` | - | `"/"` |
| `info.ingress.annotations` | extra annotations | `` |
| `info.ingress.hosts` | - | `[]` |
| `info.ingress.tls` | - | `` |
| `peer.service.type` | service type | `"ClusterIP"` |
| `peer.ingress.enabled` | expose port | `false` |
| `peer.ingress.hosts` | - | `[]` |
| `tolerations` | list of node taints to tolerate | `[]` |
| `validatorAddresses` | list of validators to deploy | `[]` |


Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
helm install stable/burrow --name my-release \
  --set=image.tag=0.23.2,resources.limits.cpu=200m -f addresses.yaml
```

Alternatively, append additional values to the YAML file generated in the [prerequisites](#prerequisites). For example,

```bash
helm install stable/burrow --name my-release -f addresses.yaml
```

## Runtime

It is unlikely that you will want to deploy this chart with the default runtime configuration. When booting permissioned blockchains in a cloud environment there are three predominant considerations in addition to the normal configuration of any cloud application.

1. What access rights to place on the ports?
2. What is the set of initial accounts and validators for the chain?
3. What keys should the validating nodes have?

Each of these considerations will be dealt with in more detail below.

### Port Configuration

Burrow utilizes three different ports by default:

* `Peer`: Burrow's peer port is used for P2P communication within the blockchain network as part of the consensus engine ([Tendermint](https://github.com/tendermint/tendermint)) to perform bilateral gossiping communication.
* `Info`: Burrow's info port is used for conducting remote procedures.
* `GRPC`: Burrow's grpc port can be used by JavaScript libraries to interact with the chain over websockets.

The default configuration for the chart sets up the port access rights in the following manner:

* `Peer`: Peer ports are **only** opened within the cluster. By default, there is no P2P communication exposed to the general internet. Each node within the cluster has its own distinct peer service built by the chart which utilizes a `ClusterIP` service type.
* `Info`: The info port is **only** opened within the cluster. By default, there is no info communication exposed to the general internet. There is **one** info service built by the chart which utilizes a `ClusterIP` service type. The default info service used by the chart is strongly linked to node number `000` and is not load balanced across the nodes by default so as to reduce any challenges with tooling that conduct long-polling after sending transactions. The chart offers an ingress which is connected to the info service, but this is `disabled` by default.
* `GRPC`: The grpc port is **only** opened within the cluster. By default, there is no grpc communication exposed to the general internet. There is **one** grpc service built by the chart which utilizes a `ClusterIP` service type. The default grpc service used by the chart is load balanced across the nodes within the cluster by default because libraries which utilize this port typical do so on websockets and the service is able to utilize a sessionAffinity setting.

In order to expose the peers to the general internet change the `peer.service.type` to `NodePort`. It is not advised to run P2P traffic through an ingress or other load balancing service as there is uncertainty with respect to the IP address which the blockchain node advertises and gossips. As such, the best way to expose P2P traffic to the internet is to utilize a `NodePort` service type. While such service types can be a challenge to work with in many instances, the P2P libraries that these blockchains utilize are very resilient to movement between machine nodes. The biggest gotcha with `NodePort` service types is to ensure that the machine nodes have proper egress within the cloud or data center provider. As long as the machine nodes do not have egress restrictions disabling the utilization of `NodePort` service types, the P2P traffic will be exposed fluidly.

To expose the info service to the general internet change the default `rpcInfo.ingress.enabled` to `true` and add the appropriate fields to the ingress for your Kubernetes cluster. This will allow developers to connect to the info service from their local machines.

To disable load balancing on the grpc service, change the `rpcGRPC.service.loadBalance` to `false`.

### Genesis

Burrow initializes any single blockchain via use of a `genesis.json` which defines what validators and accounts are given access to the permissioned blockchain when it is booted. The chart imports the `genesis.json` file as a Kubernetes configmap and then mounts it in each node deployment.

### Validator Keys

**NOTE: The chart has not been security audited and as such one should use the validator keys functionality of the chart at one's own risk.**

Burrow blockchain nodes need to have a key available to them which has been properly registered within the `genesis.json` initial state. The registered key is what enables a blockchain node to participate in the P2P validation of the network. The chart imports the validator key files as Kubernetes secrets, so the security of the blockchain is only as strong as the cluster's integrity.
