# Typesense Helm Chart

[Typesense](https://typesense.org/) is an open source, typo tolerant search engine that delivers fast and relevant results out-of-the-box.

## TL;DR;

```console
$ helm repo add springboard https://helm-charts.springboardvr.com
$ helm install my-release springboard/typesense -n [namespace]
```

## Introduction

This chart bootstraps a [typesense](https://github.com/bitnami/bitnami-docker-typesense) deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

It deploys a typesense server application and by default will create a persistent volume claim with the default storage class. Optionally, you can set up an Ingress resource to access your application.

This chart is heavily influenced by Bitnami charts best practices.

## Prerequisites

- Kubernetes 1.12+
- Helm 3.0+
- PV provisioner support in the underlying infrastructure
- ReadWriteMany volumes for deployment scaling

## Installing the Chart

To install the chart with the release name `my-release`:

```console
$ helm repo add springboard https://helm-charts.springboardvr.com
$ helm install my-release springboard/typesense -n [namespace]
```

These commands deploy typesense on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm ls -n [namespace]`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
$ helm del my-release -n [namespace]
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

The following table lists the configurable parameters of the typesense chart and their default values.

| Parameter                               | Description                                                                 | Default                                                 |
|-----------------------------------------|-----------------------------------------------------------------------------|---------------------------------------------------------|
| `image.registry`                        | typesenseJS image registry                                                  | `docker.io`                                             |
| `image.repository`                      | typesenseJS image name                                                      | `typesense/typesense`                                   |
| `image.tag`                             | typesenseJS image tag                                                       | `0.13.0`                                                |
| `image.pullPolicy`                      | typesenseJS image pull policy                                               | `IfNotPresent`                                          |
| `image.pullSecrets`                     | Specify docker-registry secret names as an array                            | `[]` (does not add image pull secrets to deployed pods) |
| `nameOverride`                          | String to partially override typesense.fullname template                    | `nil`                                                   |
| `fullnameOverride`                      | String to fully override typesense.fullname template                        | `nil`                                                   |
| `typesense.apiKey`                      | Api key for Typesense (other options can be passed as extraEnv)             | `apikey`                                                |
| `replicas`                              | Number of replicas for the application                                      | `1`                                                     |
| `applicationPort`                       | Port where the application will be running                                  | `8108`                                                  |
| `extraEnv`                              | Any extra environment variables to be pass to the pods                      | `{}`                                                    |
| `affinity`                              | Map of node/pod affinities                                                  | `{}` (The value is evaluated as a template)             |
| `nodeSelector`                          | node labels for pod assignment                                              | `{}` (The value is evaluated as a template)             |
| `tolerations`                           | Tolerations for pod assignment                                              | `[]` (The value is evaluated as a template)             |
| `securityContext.enabled`               | Enable security context                                                     | `true`                                                  |
| `securityContext.fsGroup`               | Group ID for the container                                                  | `1001`                                                  |
| `securityContext.runAsUser`             | User ID for the container                                                   | `1001`                                                  |
| `resources`                             | Resource requests and limits                                                | `{}`                                                    |
| `persistence.enabled`                   | Enable persistence using PVC                                                | `false`                                                 |
| `persistence.path`                      | Path to persisted directory                                                 | `/app/data`                                             |
| `persistence.accessMode`                | PVC Access Mode                                                             | `ReadWriteOnce`                                         |
| `persistence.storageClass`              | Storage class for dynamic provisioning                                      | `nil`                                                   |
| `persistence.size`                      | PVC Storage Request                                                         | `1Gi`                                                   |
| `service.type`                          | Kubernetes Service type                                                     | `ClusterIP`                                             |
| `service.port`                          | Kubernetes Service port                                                     | `80`                                                    |
| `service.annotations`                   | Annotations for the Service                                                 | {}                                                      |
| `service.loadBalancerIP`                | LoadBalancer IP if Service type is `LoadBalancer`                           | `nil`                                                   |
| `service.nodePort`                      | nodePort if Service type is `LoadBalancer` or `nodePort`                    | `nil`                                                   |
| `ingress.enabled`                       | Enable ingress controller resource                                          | `false`                                                 |
| `ingress.hosts[0].name`                 | Hostname to your typesense installation                                     | `typesense.local`                                       |
| `ingress.hosts[0].path`                 | Path within the url structure                                               | `/`                                                     |
| `ingress.hosts[0].tls`                  | Utilize TLS backend in ingress                                              | `false`                                                 |
| `ingress.hosts[0].certManager`          | Add annotations for cert-manager                                            | `false`                                                 |
| `ingress.hosts[0].tlsSecret`            | TLS Secret (certificates)                                                   | `typesense.local-tls-secret`                            |
| `ingress.hosts[0].annotations`          | Annotations for this host's ingress record                                  | `[]`                                                    |
| `ingress.secrets[0].name`               | TLS Secret Name                                                             | `nil`                                                   |
| `ingress.secrets[0].certificate`        | TLS Secret Certificate                                                      | `nil`                                                   |
| `ingress.secrets[0].key`                | TLS Secret Key                                                              | `nil`                                                   |

The above parameters map to the env variables defined in [spittal/typesense](https://github.com/Spittal/typesense-helm).

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
$ helm install my-release \
  --set repository=https://github.com/jbianquetti-nami/simple-typesense-app.git,replicas=2 \
    springboard/typesense
```

The above command clones the remote git repository to the `/app/` directory  of the container. Additionally it sets the number of `replicas` to `2`.

Alternatively, a YAML file that specifies the values for the above parameters can be provided while installing the chart. For example,

```console
$ helm install my-release -f values.yaml springboard/typesense
```

> **Tip**: You can use the default [values.yaml](values.yaml)

## Configuration and installation details

### Typesense settings

You can configure any typesense setting using the `extraArgs` parameter like so.

```yaml
extraArgs:
  - name: TYPESENSE_API_PORT
    value: 8080
```

A list of the available options can be found on the [typesense website](https://typesense.org/docs/0.13.0/guide/).

### Set up an Ingress controller

First install the nginx-ingress controller and then deploy the typesense helm chart with the following parameters:

```console
ingress.enabled=true
ingress.host=example.com
service.type=ClusterIP
```

### Configure TLS termination for your ingress controller

You must manually create a secret containing the certificate and key for your domain. Then ensure you deploy the Helm chart with the following ingress configuration:

```yaml
ingress:
  enabled: false
  path: /
  host: example.com
  annotations:
    kubernetes.io/ingress.class: nginx
  tls:
      hosts:
        - example.com
```
