<div align="center">

![full logo superstream](https://github.com/superstreamlabs/superstream-engine/assets/107035359/19dc2e40-a907-49ee-9faf-d6d707633d53)

<b>Improve And Optimize Your Kafka In Literally Minutes.<br>
Reduce Costs and Boost Performance by 75% Without Changing a Single Component or Your Existing Kafka!</b>

</div>

## Configure Environment Variables

The docker-compose.yaml uses **YAML anchors** to manage environment variables efficiently - common variables are defined once at the top and reused across services.

### Required Environment Variables

Before deploying, set these environment variables:

**Option 1: Using Environment Variables**

```bash
export ACTIVATION_TOKEN="your-activation-token-here"
export AGENT_NAME="your-agent-name"
export ACCOUNT_ID="your-account-id"
export DD_SITE="us5.datadoghq.com"  # Optional for Datadog
```

**Option 2: Using .env File (Recommended)**

Create a `.env` file in the same directory as `docker-compose.yaml`:

```bash
ACTIVATION_TOKEN=your-activation-token-here
AGENT_NAME=my-superstream-agent
ACCOUNT_ID=your-account-id
DD_SITE=us5.datadoghq.com
```

Docker Compose will automatically load these variables from the `.env` file.

### Customizing Configuration

To change control plane or syslog settings, edit the YAML anchors at the top of `docker-compose.yaml`:

```yaml
x-control-plane-env: &control-plane-env
  CONTROL_PLANE_HOST: broker.superstream.ai
  CONTROL_PLANE_PORT: 4222
  CONTROL_PLANE_PROTOCOL: nats

x-syslog-env: &syslog-env
  SYSLOG_HOST: telegraf
  SYSLOG_PORT: 6514
  SYSLOG_PROTOCOL: udp
```

Changes here automatically apply to all services that use these anchors.

### Environment Variables Description

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `ACTIVATION_TOKEN` | Yes | - | Superstream activation token for authentication |
| `AGENT_NAME` | Yes | - | Unique name for your Superstream agent instance |
| `ACCOUNT_ID` | Yes | - | Your Superstream account identifier |
| `DD_SITE` | No | us5.datadoghq.com | Datadog site URL (e.g., datadoghq.com, us5.datadoghq.com, datadoghq.eu) |

## Services Overview

The docker-compose deployment includes the following services:

- **data-plane**: Main Superstream data plane service (integrated with Datadog)
- **auto-scaler**: Kafka auto-scaling service
- **telegraf**: Metrics and logs collection
- **datadog-agent**: Datadog monitoring and APM (DogStatsD on port 8125, APM on port 8126)
- **datadog-init**: Initialization service that fetches Datadog API key and generates configuration
- **datadog-config-monitor**: Monitors configuration changes and automatically restarts datadog-agent when needed
- **watchtower**: Automatic container updates

## Deploy

To deploy the stack, run the following command in the folder where the `docker-compose.yaml` is located:

```bash
docker compose -f docker-compose.yaml -p superstream up -d
```

## Datadog Integration

The Datadog agent is automatically configured and integrated with the Superstream data-plane service. It provides:

- **DogStatsD**: Metrics collection on port 8125 (UDP)
- **APM Tracing**: Application Performance Monitoring on port 8126 (TCP)
- **Log Collection**: Container logs forwarding to Datadog
- **Container Monitoring**: Docker container metrics
- **Dynamic Configuration**: Automatic detection and application of configuration changes

The implementation mirrors the Helm chart pattern:
1. **datadog-init**: Runs `dd-key-fetcher` to fetch the API key and `yaml-generator` to create the initial configuration
2. **datadog-agent**: Main Datadog agent service that reads the API key from the shared volume
3. **datadog-config-monitor**: Continuously monitors for configuration updates from the control plane and automatically restarts the datadog-agent container when configuration changes are detected

The Datadog API key is automatically fetched from Superstream's control plane during initialization and securely stored in a Docker volume.

### Dynamic Configuration Updates

The `datadog-config-monitor` service provides automatic configuration updates:

**How it works:**
1. Runs `yaml-generator` to fetch latest configuration from Superstream's control plane
2. Monitors `/etc/datadog-agent/conf.d` directory for configuration changes (every 300 seconds / 5 minutes)
3. Sends all log events to Telegraf via syslog (UDP port 6514)
4. When changes are detected:
   - **Step 1**: Re-runs `datadog-init` container to fetch the updated configuration (dd-key-fetcher and yaml-generator)
   - **Step 2**: Waits for initialization to complete (up to 60 seconds)
   - **Step 3**: Restarts `datadog-agent` container to apply the new configuration

**Requirements:**
- Write access to Docker socket (`/var/run/docker.sock`) to manage containers
- Docker CLI installed in the container (automatically installed if missing)
- Netcat for syslog communication (automatically installed if missing)

**Logging:**
All configuration monitoring events are sent to Telegraf for centralized logging with appropriate severity levels (info, warning, error).

This mirrors the Helm chart behavior where configuration changes trigger a pod restart, but adapted for Docker Compose using container management.

### Using Datadog with Your Applications

If you want your applications to send metrics to Datadog, you can configure them with these environment variables:
- `DD_AGENT_HOST=datadog-agent`
- `DD_DOGSTATSD_PORT=8125`
- `DD_TRACE_AGENT_HOSTNAME=datadog-agent`
- `DD_TRACE_AGENT_PORT=8126`

Add these to any service in the docker-compose.yaml that needs Datadog integration.

## Stopping the Services

To stop all services:

```bash
docker compose -f docker-compose.yaml -p superstream down
```

To stop and remove volumes:

```bash
docker compose -f docker-compose.yaml -p superstream down -v
```
