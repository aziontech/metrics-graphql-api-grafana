# Runbook: metrics-graphql-api-grafana

## Service Overview

| Field | Value |
|-------|-------|
| Service | Grafana dashboards for Azion Metrics GraphQL API |
| Runtime | Docker (Grafana latest) |
| Port | 3000 |
| API endpoint | api.azionapi.net/metrics/graphql |
| Auth | Azion Personal Token |
| Owner | team-data-routing |

## Common Operations

### Start the Platform

1. Create a `.env` file with your Azion Personal Token:
   ```
   PERSONAL_TOKEN=<your_token>
   ```

2. Start the container:
   ```bash
   docker-compose up -d
   ```

3. Access Grafana at `http://localhost:3000` — anonymous admin access is enabled by default.

### Stop the Platform

```bash
docker-compose down
```

### Update Grafana Version

The service uses `grafana/grafana:latest`. To update:
```bash
docker-compose pull
docker-compose up -d
```

### Test GraphQL Queries

Import `Insomnia.json` into Insomnia REST client for manual query testing. Set the `PERSONAL_TOKEN` variable in the Production environment.

Required Insomnia plugin: `insomnia-plugin-customtimestamp`

## Troubleshooting

### Dashboards show no data

1. Verify the Personal Token is valid:
   ```bash
   curl -H "Authorization: Token <your_token>" \
        -H "Accept: application/json; version2;" \
        https://api.azionapi.net/metrics/graphql
   ```

2. Check the `.env` file contains `PERSONAL_TOKEN` with a valid token

3. Verify the container has the environment variable:
   ```bash
   docker exec azion-metrics-grafana env | grep TOKEN
   ```

4. Check Grafana logs:
   ```bash
   docker logs azion-metrics-grafana
   ```

### Infinity plugin not loading

The Infinity datasource plugin is installed at container startup from GitHub. If it fails:

1. Check Docker logs for plugin installation errors
2. Verify internet connectivity from the container
3. Manually install: access Grafana UI → Configuration → Plugins → search "Infinity"

### Container won't start

1. Check port 3000 is not already in use:
   ```bash
   lsof -i :3000
   ```

2. Verify Docker is running:
   ```bash
   docker info
   ```

### Dashboard provisioning errors

If dashboards don't appear:
1. Check the volume mounts in `docker-compose.yml`
2. Verify JSON dashboard files are valid:
   ```bash
   python3 -m json.tool grafana/dashboards/HttpMetrics/dashboard.json > /dev/null
   ```

## Monitoring

This is a local development/analytics tool. No production monitoring is required.

### Key API Metrics

| Metric | Dashboard Panel | Description |
|--------|----------------|-------------|
| Request count | HTTP Metrics → Time Series | Total edge requests over time |
| Request time | HTTP Metrics → Gauge/Time Series | Average latency (threshold: 250ms) |
| Bytes sent | HTTP Metrics → Gauge | Average response payload size |
| Cache status | HTTP Metrics → Table | Upstream cache hit/miss ratio |

## Dependencies

| Dependency | Purpose |
|------------|---------|
| Docker / Docker Compose | Container runtime |
| Grafana | Analytics and visualization platform |
| Yesoreyeram Infinity Plugin | GraphQL datasource support |
| Azion Metrics GraphQL API | Data source |
| Azion Personal Token | API authentication |
