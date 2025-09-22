# Cassandra Helm Chart

{{ template "chart.description" . }}

{{ template "chart.versionBadge" . }}{{ template "chart.typeBadge" . }}{{ template "chart.appVersionBadge" . }}

A bare minimum Helm chart for deploying Apache Cassandra with configurable authentication and persistent storage.

## Features

- **Configurable Authentication**: Enable/disable authentication as needed
- **Persistent Storage**: PVC support with configurable storage classes
- **StatefulSet Deployment**: Ensures stable network identity and persistent storage
- **Resource Management**: Configurable CPU and memory limits
- **Health Checks**: Readiness probes for cluster health monitoring
- **JVM Configuration**: Customizable heap size and JVM settings

## Installation

### Add Helm Repository

```bash
helm repo add db https://tvon90.github.io/helm-repo
helm repo update
```

### Install Chart

```bash
# Install with default values
helm install my-cassandra db/cassandra

# Install with authentication disabled
helm install my-cassandra db/cassandra --set auth.enabled=false

# Install with custom password and larger storage
helm install my-cassandra db/cassandra \
  --set auth.superuser.password=mySecurePassword \
  --set persistence.size=10Gi \
  --set persistence.storageClass=fast-ssd
```

## Configuration

{{ template "chart.valuesSection" . }}

## Usage Examples

### Connecting with Authentication Enabled

```bash
# Get the password
kubectl get secret my-cassandra-auth -o jsonpath="{.data.cassandra-password}" | base64 --decode

# Connect via kubectl exec
kubectl exec -it my-cassandra-0 -- cqlsh -u cassandra -p <password>

# Port-forward and connect locally
kubectl port-forward svc/my-cassandra 9042:9042
cqlsh localhost 9042 -u cassandra -p <password>
```

### Connecting with Authentication Disabled

```bash
# Connect directly without credentials
kubectl exec -it my-cassandra-0 -- cqlsh

# Port-forward and connect locally
kubectl port-forward svc/my-cassandra 9042:9042
cqlsh localhost 9042
```

### Scaling

```bash
# Scale to 3 replicas
helm upgrade my-cassandra my-charts/cassandra --set replicaCount=3
```

### Upgrading

```bash
# Upgrade to newer image version
helm upgrade my-cassandra my-charts/cassandra --set image.tag=4.1.5
```

## Authentication

Authentication is **enabled by default** for security. The chart uses:

- **PasswordAuthenticator**: For username/password authentication
- **CassandraAuthorizer**: For role-based access control
- **CassandraRoleManager**: For user management

### Disabling Authentication

Set `auth.enabled=false` to disable authentication:

```bash
helm install my-cassandra my-charts/cassandra --set auth.enabled=false
```

**⚠️ Security Warning**: Only disable authentication in development or isolated environments.

## Persistence

The chart uses StatefulSet with VolumeClaimTemplates for persistent storage:

- **Default**: 10Gi storage with default storage class
- **Configurable**: Storage size, class, and access mode
- **Automatic**: PVC creation per pod replica
- **Persistent**: Data survives pod restarts and rescheduling

## Requirements

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure

## Troubleshooting

### Common Issues

1. **Pod stuck in Pending**: Check if PVC can be provisioned
2. **Authentication failures**: Verify password and wait for init script completion
3. **Connection timeouts**: Ensure service ports are correctly exposed
4. **Storage issues**: Verify storage class exists and has available capacity

### Debug Commands

```bash
# Check pod status
kubectl get pods -l app.kubernetes.io/name=cassandra

# View logs
kubectl logs my-cassandra-0

# Check PVC status
kubectl get pvc

# Test connectivity
kubectl exec -it my-cassandra-0 -- nodetool status
```

## License

This chart is licensed under the MIT License.