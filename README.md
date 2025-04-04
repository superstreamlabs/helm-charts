<div align="center">

<img src="https://github.com/user-attachments/assets/35899c78-24eb-4507-97ed-e87e84c49fea#gh-dark-mode-only" width="500">
<img src="https://github.com/user-attachments/assets/8a7bca49-c362-4a8c-945e-a331fb26d8eb#gh-light-mode-only" width="500">

<p>
  Superstream is an autonomous platform designed<br>to optimize data infrastructure for cost reduction and reduced operations.<br>
  <a href="https://superstream.ai">Website</a> | <a href="https://docs.superstream.ai">Docs</a>
</p>

</div>

#### This chart is for Superstream customers who prefer to host a local agent

## Configure Environment Tokens

For easiness, create `custom_values.yaml` file and edit the following values:
```yaml
############################################################
# GLOBAL configuration for Superstream Agent
############################################################
global:
  agentName: ""                    # Define the superstream engine name within 32 characters, excluding '.', and using only lowercase letters, numbers, '-', and '_'.
  superstreamAccountId: ""         # Provide the account ID associated with the deployment, which could be used for identifying resources or configurations tied to a specific account.
  superstreamActivationToken: ""   # Enter the activation token required for services or resources that need an initial token for activation or authentication.
```

To deploy it, run the following:
```bash
helm repo add superstream https://superstream-agent.k8s.superstream.ai/ --force-update && helm upgrade --install superstream superstream/superstream-agent -f custom_values.yaml --create-namespace --namespace superstream --wait
```

## Parameters
The following table lists the configurable parameters of the SuperStream chart and their default values:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `global.agentName` | Define the superstream agent name within 32 characters, excluding '.', and using only lowercase letters, numbers, '-', and '_'. | `""` |
| `global.superstreamAccountId` | Provide the account ID associated with the deployment, which could be used for identifying resources or configurations tied to a specific account. | `""` |
| `global.superstreamActivationToken` | Enter the activation token required for services or resources that need an initial token for activation or authentication. | `""` |
| `global.image.pullPolicy` | Global image pull policy to use for all container images in the chart. Can be overridden by individual image pullPolicy. | `""` |
| `global.image.pullSecretNames` | Global list of secret names to use as image pull secrets for all pod specs in the chart. Secrets must exist in the same namespace. | `[]` |
| `global.image.registry` | Global registry to use for all container images in the chart. Can be overridden by individual image registry. | `""` |
| `global.labels` | Global labels to use for all container images in the chart. | `{}` |
| `superstreamAgent.releaseDate` | Release date for the backend component. | `""` |
| `superstreamAgent.replicaCount` | Number of replicas for the backend deployment. | `2` |
| `superstreamAgent.image.repository` | Docker image repository for the backend service. | `superstreamlabs/superstream-data-plane-be` |
| `superstreamAgent.image.pullPolicy` | Policy for pulling the image. | `Always` |
| `superstreamAgent.image.tag` | Overrides the image tag. | `latest` |
| `superstreamAgent.image.registry` | Image registry override for agent image. | `""` |
| `superstreamAgent.imagePullSecrets` | Image pull secrets. | `[]` |
| `superstreamAgent.nameOverride` | Overrides for Helm's default naming conventions. | `""` |
| `superstreamAgent.fullnameOverride` | Full name override for Helm's default naming conventions. | `superstream` |
| `superstreamAgent.secret.name` | Secret configuration for sensitive information. | `superstream-creds` |
| `superstreamAgent.secret.encryptionSecretKey` | Encryption secret key used for sensitive information. | `""` |
| `superstreamAgent.secret.superstreamActivationToken` | Activation token for services or resources. | `""` |
| `superstreamAgent.secret.useExisting` | Specifies whether to use an existing secret. | `false` |
| `superstreamAgent.podAnnotations.prometheus.io/path` | Path for Prometheus to scrape metrics from the pod. | `"/monitoring/metrics"` |
| `superstreamAgent.podAnnotations.prometheus.io/scrape` | Specifies whether Prometheus should scrape metrics from the pod. | `'true'` |
| `superstreamAgent.podAnnotations.prometheus.io/port` | Port for Prometheus to scrape metrics from the pod. | `"7777"` |
| `superstreamAgent.podSecurityContext` | Security context settings for the pod. | `{}` |
| `superstreamAgent.securityContext` | Security context for containers within the pod. | `{}` |
| `superstreamAgent.serviceAccount.create` | Specifies whether a service account should be created. | `true` |
| `superstreamAgent.serviceAccount.annotations` | Annotations to add to the service account. | `{}` |
| `superstreamAgent.serviceAccount.name` | The name of the service account to use. | `""` |
| `superstreamAgent.extraEnv` | A map of additional environment variables for the application. | `{}` |
| `superstreamAgent.service.enabled` | Enable service for the backend. | `true` |
| `superstreamAgent.service.type` | Type of service for the backend. | `ClusterIP` |
| `superstreamAgent.service.port` | Port for the backend service. | `7777` |
| `superstreamAgent.resources.limits.cpu` | CPU limit for the backend pod. | `8` |
| `superstreamAgent.resources.limits.memory` | Memory limit for the backend pod. | `8Gi` |
| `superstreamAgent.resources.requests.cpu` | CPU request for the backend pod. | `1` |
| `superstreamAgent.resources.requests.memory` | Memory request for the backend pod. | `1Gi` |
| `superstreamAgent.autoscaling.enabled` | Enable autoscaling for the backend. | `true` |
| `superstreamAgent.autoscaling.minReplicas` | Minimum number of replicas for autoscaling. | `2` |
| `superstreamAgent.autoscaling.maxReplicas` | Maximum number of replicas for autoscaling. | `5` |
| `superstreamAgent.autoscaling.targetCPUUtilizationPercentage` | CPU utilization percentage for autoscaling. | `75` |
| `superstreamAgent.autoscaling.targetMemoryUtilizationPercentage` | Memory utilization percentage for autoscaling. | `75` |
| `superstreamAgent.nodeSelector` | Node selectors to control the placement of pods. | `{}` |
| `superstreamAgent.tolerations` | Tolerations for pods to tolerate certain node conditions or taints. | `[]` |
| `superstreamAgent.affinity` | Affinity rules for pod scheduling. | `{}` |
| `superstreamAgent.controlPlane.host` | Host for the control plane connection. | `"broker.superstream.ai"` |
| `superstreamAgent.controlPlane.port` | Port for the control plane connection. | `4222` |
| `superstreamAgent.syslog.enabled` | Determines whether the syslog is enabled for the superstream agent. | `true` |
| `superstreamAgent.syslog.remoteSyslog` | Remote syslog server to send logs to. | `telegraf` |
| `superstreamAgent.syslog.port` | Port for the remote syslog. | `6514` |
| `superstreamAgent.syslog.protocol` | Protocol (e.g., UDP) for the remote syslog. | `udp` |
| `autoScaler.enabled` | Enables the Kafka auto-scaler. | `true` |
| `autoScaler.releaseDate` | Release date for the autoscaler. | `""` |
| `autoScaler.replicaCount` | Number of replicas for the autoscaler deployment. | `2` |
| `autoScaler.image.repository` | Docker image repository for the Kafka auto-scaler. | `superstreamlabs/superstream-kafka-auto-scaler` |
| `autoScaler.image.pullPolicy` | Policy for pulling the Docker image. | `Always` |
| `autoScaler.image.tag` | Docker image tag (Overrides the image). | `latest` |
| `autoScaler.imagePullSecrets` | Image pull secrets. | `[]` |
| `autoScaler.podAnnotations.prometheus.io/path` | Path for Prometheus to scrape metrics from the pod. | `"/monitoring/metrics"` |
| `autoScaler.podAnnotations.prometheus.io/scrape` | Specifies whether Prometheus should scrape metrics from the pod. | `'true'` |
| `autoScaler.podAnnotations.prometheus.io/port` | Port for Prometheus to scrape metrics from the pod. | `"7777"` |
| `autoScaler.podSecurityContext` | Security context settings for the pod. | `{}` |
| `autoScaler.securityContext` | Security context for containers within the pod. | `{}` |
| `autoScaler.extraEnv` | A map of additional environment variables for the application. | `{}` |
| `autoScaler.service.enabled` | Enable service for the autoscaler. | `true` |
| `autoScaler.service.type` | Type of service for the autoscaler. | `ClusterIP` |
| `autoScaler.service.port` | Port for the autoscaler service. | `7777` |
| `autoScaler.resources.limits.cpu` | CPU limit for the autoscaler pod. | `2` |
| `autoScaler.resources.limits.memory` | Memory limit for the autoscaler pod. | `2Gi` |
| `autoScaler.resources.requests.cpu` | CPU request for the autoscaler pod. | `500m` |
| `autoScaler.resources.requests.memory` | Memory request for the autoscaler pod. | `500Mi` |
| `autoScaler.autoscaling.enabled` | Enable autoscaling to manage the replicas of the autoscaler. | `true` |
| `autoScaler.autoscaling.minReplicas` | Minimum number of replicas for autoscaling. | `2` |
| `autoScaler.autoscaling.maxReplicas` | Maximum number of replicas for autoscaling. | `5` |
| `autoScaler.autoscaling.targetCPUUtilizationPercentage` | CPU utilization percentage for autoscaling. | `75` |
| `autoScaler.autoscaling.targetMemoryUtilizationPercentage` | Memory utilization percentage for autoscaling. | `75` |
| `autoScaler.nodeSelector` | Node selectors to control the placement of pods. | `{}` |
| `autoScaler.tolerations` | Tolerations for pods to tolerate certain node conditions or taints. | `[]` |
| `autoScaler.affinity` | Affinity rules for pod scheduling. | `{}` |