# ANOW Agent Helm Chart

This Helm chart deploys the ANOW Agent application to Kubernetes.  
Below are the configurable parameters available in the `values.yaml` file.

## 📦Docker Image

This chart requires a custom Docker image of the AutomateNOW! agent.  
You can find the base image on [Docker Hub](https://hub.docker.com/r/anowautomate/agent).  

>⚠️ You need to build your own image based on the AutomateNOW agent and push it to your own registry.

## Parameters

### `customName`

- **Description:**  
  Allows setting a custom resource name for Kubernetes resources in the chart.  
  If not set, defaults to `{{ .Release.Name }}-{{ .Chart.Name }}`.

- **Type:** `string`  
- **Default:** `""` (empty string)

---

### `image`

Settings for the ANOW agent container image.

| Name          | Description                                  | Type   | Default             |
| ------------- | ---------------------------------------------|--------|---------------------|
| `repository`  | Full image repository path for the container | string | `""` (empty string) |
| `tag`         | Image tag/version to use                     | string | `""` (empty string) |
| `pullPolicy`  | Kubernetes image pull policy                 | string | `IfNotPresent`      |

---

### `agent`

Configuration parameters for the ANOW agent.

| Name                           | Description                                                                                                                                                | Type   | Default             |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ | --------------------|
| `AGENT_ID`                     | Unique identifier for the ANOW agent.                                                                                                                      | string | `""` (empty string) |
| `AGENT_DOMAIN`                 | The domain that the ANOW agent connects to.                                                                                                                | string | `""` (empty string) |
| `BROKER_HOST`                  | Hostname and port of the message broker (e.g., RabbitMQ).                                                                                                  | string | `""` (empty string) |
| `BROKER_USER`                  | (Optional) Username for authenticating with the message broker.                                                                                            | string | `""` (empty string) |
| `BROKER_PASSWORD`              | (Optional) Broker password in plain text. Used only if `BROKER_PASSWORD_SECRET_NAME` is unset.                                                             | string | `""` (empty string) |
| `BROKER_PASSWORD_SECRET_NAME`  | (Optional) Name of a Kubernetes Secret containing the broker password. Overrides `BROKER_PASSWORD`. The Secret must include a key named `BROKER_PASSWORD`. | string | `""` (emptystring)  |
| `JAVA_JOPTS`                   | (Optional) JVM parameters/options passed to the Java Virtual Machine.                                                                                      | string | `"-Xms128m -Xmx256m -DRWD=true -Djna.tmpdir=/anow/agent/tmp"` |

---

### `resources`

Resource requests and limits for the ANOW agent container.

| Name                   | Description                                              | Type   | Default    |
| ---------------------- | ---------------------------------------------------------|--------|------------|
| `requests.memory`      | Minimum amount of memory guaranteed (e.g., "100Mi")      | string | `"100Mi"`  |
| `requests.cpu`         | Minimum amount of CPU guaranteed (e.g., "0.5" or "500m") | string | `"0.5"`    |
| `limits.memory`        | Maximum amount of memory allowed (e.g., "300Mi")         | string | `"300Mi"`  |
| `limits.cpu`           | Maximum amount of CPU allowed (e.g., "1")                | string | `"1"`      |

---

### `volumes`

Persistent volume sizes for the ANOW agent.

| Name         | Description                                               | Type   | Default   |
| ------------ | --------------------------------------------------------- |--------|-----------|
| `log`        | Size of the persistent volume claim for log storage       | string | `"10Gi"`  |
| `sysout`     | Size of the persistent volume claim for sysout storage    | string | `"10Gi"`  |

---

## Install
```sh
helm install anow-agent https://gitlab.com/vasyl.pashko/anow-agent-helm-chart/-/raw/main/anow-agent-0.1.0.tgz \
  --set customName="my-anow-agent" \
  --set image.repository="agent" \
  --set image.tag="java17" \
  --set agent.AGENT_ID="k8s" \
  --set agent.AGENT_DOMAIN="Test" \
  --set agent.BROKER_HOST="rabbitmq.anow.com:5672" \
  --set agent.BROKER_USER="user" \
  --set agent.BROKER_PASSWORD="password123"
```

## 🐞 Quick Troubleshooting
If the Helm release does not start correctly or resources behave unexpectedly, try these commands:
```sh
helm list -n <namespace>
```
- Shows all Helm releases in the namespace and their status.

```sh
helm status <release_name> -n <namespace>
```
- Displays the release details, including deployed resources and events.

```sh
kubectl get pods -n <namespace>
```
- Lists pods with their status (Running, Pending, CrashLoopBackOff, etc.).

```sh
kubectl describe pod <pod_name> -n <namespace>
```
- Provides detailed information about the pod, events, and possible issues.

```sh
kubectl logs <pod_name> -n <namespace>
```
- Displays logs from the container to help diagnose startup or runtime errors.
