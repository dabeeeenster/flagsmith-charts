# Flagsmith Helm chart

## Installation

```bash
helm repo add flagsmith https://flagsmith.github.io/flagsmith-charts/
helm install flagsmith flagsmith/flagsmith
```

This will install using default options. (Pods may error and restart
while waiting for other pods to become ready.).

### Ingress configuration

The above will start pods in the cluster, but to gain web access to
Flagsmith, do one of the following:

#### In a cluster that has an ingress controller

Set the following values for flagsmith, with changes as needed to
accommodate your ingress controller, and any associated DNS
changes. Also, set the `API_URL` env-var such that the URL is
reachable from a browser accessing the frontend.

Eg:

```yaml
ingress:
  frontend:
    enabled: true
    hosts:
      - host: flagsmith.[MYDOMAIN]
        paths:
          - /
  api:
    enabled: true
    hosts:
      - host: flagsmith.[MYDOMAIN]
        paths:
          - /api/
          - /health/

frontend:
  env:
    - name: API_URL
      value: 'https://flagsmith.[MYDOMAIN]/api/v1/'
```

Then, once any out-of-cluster DNS or CDN changes have been applied,
access `https://flagsmith.[MYDOMAIN]` in a browser.

#### Minikube ingress

(See https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/ for more details.)

If using minikube, enable ingress with `minikube addons enable ingress`.

Then set the following values for flagsmith:
```yaml
ingress:
  frontend:
    enabled: true
    hosts:
      - host: flagsmith.local
        paths:
          - /
  api:
    enabled: true
    hosts:
      - host: flagsmith.local
        paths:
          - /api/
```
and apply. This will create two ingress resources.

Run `minikube ip`. Set this ip and `flagsmith.local` in your `/etc/hosts`, eg:

```
192.168.99.99	flagsmith.local
```

Then access `http://flagsmith.local` in a browser.

#### Port forwarding

Set the following values for flagsmith:
```yaml
frontend:
  env:
    - name: API_URL
      value: 'http://localhost:8000/api/v1/'
```

In one terminal, run:
```bash
kubectl -n [flagsmith-namespace] port-forward svc/[flagsmith-release-name]-api 8000:8000
```
and in another, run:
```bash
kubectl -n [flagsmith-namespace] port-forward svc/[flagsmith-release-name]-frontend 8080:8080
```

Then access `http://localhost:8080` in a browser.

### Database configuration

By default, the chart creates its own PostgreSQL server within the cluster.

TODO: add configuration for (and document) using an external hosted database.

### Resource allocation

By default, no resource limits or requests are set.

TODO: recommend some defaults

### Replicas

By default, 1 replica of each of the frontend and api is used.

TODO: recommend some defaults.

TODO: consider some autoscaling options.

TODO: create a pod-disruption-budget

### InfluxDB

By default, uses InfluxDB to store additional data.

TODO: explain some options here, eg how to run in production, what it is for etc.

## Configuration

The following table lists the configurable parameters of the chart and
their default values.

| Parameter                                     | Description                                              | Default                        |
| ---------                                     | ------------                                             | -------                        |
| `api.image.repository`                        | docker image repository for flagsmith api                | `flagsmith/flagsmith-api`      |
| `api.image.tag`                               | docker image tag for flagsmith api                       | `latest`                       |
| `api.image.imagePullPolicy`                   |                                                          | `IfNotPresent`                 |
| `api.image.imagePullSecrets`                  |                                                          | `[]`                           |
| `api.replicacount`                            | number of replicas for the flagsmith api                 | 1                              |
| `api.resources`                               | resources per pod for the flagsmith api                  | `{}`                           |
| `api.podLabels`                               | additional labels to apply to pods for the flagsmith api | `{}`                           |
| `api.env`                                     | environment variables to set for the flagsmith api       |                                |
| `api.nodeSelector`                            |                                                          | `{}`                           |
| `api.tolerations`                             |                                                          | `[]`                           |
| `api.affinity`                                |                                                          | `{}`                           |
| `api.livenessProbe.failureThreshold`          |                                                          | 5                              |
| `api.livenessProbe.initialDelaySeconds`       |                                                          | 50                             |
| `api.livenessProbe.periodSeconds`             |                                                          | 10                             |
| `api.livenessProbe.successThreshold`          |                                                          | 1                              |
| `api.livenessProbe.timeoutSeconds`            |                                                          | 2                              |
| `api.readinessProbe.failureThreshold`         |                                                          | 10                             |
| `api.readinessProbe.initialDelaySeconds`      |                                                          | 50                             |
| `api.readinessProbe.periodSeconds`            |                                                          | 10                             |
| `api.readinessProbe.successThreshold`         |                                                          | 1                              |
| `api.readinessProbe.timeoutSeconds`           |                                                          | 2                              |
| `frontend.enabled`                            | Whether the flagsmith frontend is enabled                | `true`                         |
| `frontend.image.repository`                   | docker image repository for flagsmith frontend           | `flagsmith/flagsmith-frontend` |
| `frontend.image.tag`                          | docker image tag for flagsmith frontend                  | `test`                         |
| `frontend.image.imagePullPolicy`              |                                                          | `IfNotPresent`                 |
| `frontend.image.imagePullSecrets`             |                                                          | `[]`                           |
| `frontend.replicacount`                       | number of replicas for the flagsmith frontend            | 1                              |
| `frontend.resources`                          | resources per pod for the flagsmith frontend             | `{}`                           |
| `frontend.env`                                | environment variables to set for the flagsmith frontend  |                                |
| `frontend.nodeSelector`                       |                                                          | `{}`                           |
| `frontend.tolerations`                        |                                                          | `[]`                           |
| `frontend.affinity`                           |                                                          | `{}`                           |
| `frontend.livenessProbe.failureThreshold`     |                                                          | 20                             |
| `frontend.livenessProbe.initialDelaySeconds`  |                                                          | 20                             |
| `frontend.livenessProbe.periodSeconds`        |                                                          | 10                             |
| `frontend.livenessProbe.successThreshold`     |                                                          | 1                              |
| `frontend.livenessProbe.timeoutSeconds`       |                                                          | 10                             |
| `frontend.readinessProbe.failureThreshold`    |                                                          | 20                             |
| `frontend.readinessProbe.initialDelaySeconds` |                                                          | 20                             |
| `frontend.readinessProbe.periodSeconds`       |                                                          | 10                             |
| `frontend.readinessProbe.successThreshold`    |                                                          | 1                              |
| `frontend.readinessProbe.timeoutSeconds`      |                                                          | 10                             |
| `postgresql.enabled`                          | if `true`, creates in-cluster PostgreSQL database        | `true`                         |
| `postgresql.serviceAccount.enabled`           | creates a serviceaccount for the postgres pod            | `true`                         |
| `nameOverride`                                |                                                          | `flagsmith-postgres`           |
| `postgresqlDatabase`                          |                                                          | `flagsmith`                    |
| `postgresqlUsername`                          |                                                          | `postgres`                     |
| `postgresqlPassword`                          |                                                          | `flagsmith`                    |
| `influxdb.nameOverride`                       |                                                          | `influxdb`                     |
| `influxdb.image.repository`                   | docker image repository for influxdb                     | `quay.io/influxdb/influxdb`    |
| `influxdb.image.tag`                          | docker image tag for influxdb                            | `v2.0.2`                       |
| `influxdb.image.imagePullPolicy`              |                                                          | `IfNotPresent`                 |
| `influxdb.image.imagePullSecrets`             |                                                          | `[]`                           |
| `influxdb.adminUser.organization`             |                                                          | `influxdata`                   |
| `influxdb.adminUser.bucket`                   |                                                          | `default`                      |
| `influxdb.adminUser.user`                     |                                                          | `admin`                        |
| `influxdb.adminUser.password`                 |                                                          | randomly generated             |
| `influxdb.adminUser.token`                    |                                                          | randomly generated             |
| `influxdb.persistence.enabled`                |                                                          | `false`                        |
| `influxdb.resources`                          | resources per pod for the influxdb                       | `{}`                           |
| `influxdb.nodeSelector`                       |                                                          | `{}`                           |
| `influxdb.tolerations`                        |                                                          | `[]`                           |
| `influxdb.affinity`                           |                                                          | `{}`                           |
| `hooks.enabled`                               | Enables hooks (to migrate the db)                        | `false`                        |
| `hooks.removeOnSuccess`                       |                                                          | `true`                         |
| `service.influxdb.externalPort`               |                                                          | `8080`                         |
| `service.api.type`                            |                                                          | `ClusterIP`                    |
| `service.api.port`                            |                                                          | `8000`                         |
| `service.frontend.type`                       |                                                          | `ClusterIP`                    |
| `service.frontend.port`                       |                                                          | `8080`                         |
| `ingress.frontend.enabled`                    |                                                          | `false`                        |
| `ingress.frontend.annotations`                |                                                          | `{}`                           |
| `ingress.frontend.hosts[].host`               |                                                          | `chart-example.local`          |
| `ingress.frontend.hosts[].paths`              |                                                          | `[]`                           |
| `ingress.frontend.tls`                        |                                                          | `[]`                           |
| `ingress.api.enabled`                         |                                                          | `false`                        |
| `ingress.api.annotations`                     |                                                          | `{}`                           |
| `ingress.api.hosts[].host`                    |                                                          | `chart-example.local`          |
| `ingress.api.hosts[].paths`                   |                                                          | `[]`                           |
| `ingress.api.tls`                             |                                                          | `[]`                           |


---

## Development and contributing

### Requirements

helm version > 3.0.2

### To run locally

You can test and run the application locally on OSX using `minikube` like this:

```bash
# Install Docker for Desktop and then:

brew install minikube
minikube start --memory 8192 --cpus 4
helm install flagsmith --debug ./flagsmith
minikube dashboard
```

### Test install Chart

Install Chart without building a package:

```bash
helm install flagsmith --debug ./flagsmith
```

Run template and check kubernetes resouces are made:

```bash
helm template flagsmith flagsmith --debug -f flagsmith/values.yaml
```

### build chart package

To build chart package run:

```bash
helm package ./flagsmith
```