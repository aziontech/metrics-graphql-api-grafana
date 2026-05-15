# Architecture: metrics-graphql-api-grafana

## Overview

metrics-graphql-api-grafana is a containerized Grafana analytics platform that visualizes Azion edge computing metrics via the Metrics GraphQL API. It provides pre-built dashboards for HTTP request analytics, performance monitoring, and dataset exploration using the Yesoreyeram Infinity datasource plugin for direct GraphQL queries.

## System Context

```
Azion Metrics GraphQL API
  (api.azionapi.net/metrics/graphql)
        ^
        | GraphQL queries + Personal Token auth
        |
Grafana (Docker, port 3000)
  |
  +-- Infinity Datasource Plugin (GraphQL-capable)
  |
  +-- Provisioned Dashboards
       +-- Home: Navigation and documentation links
       +-- HTTP Metrics: Request count, latency, bytes, cache status
       +-- Datasets: Schema introspection and dataset discovery
```

## Components

### Docker Compose Service

Single service `azion-metrics-grafana`:
- **Image**: `grafana/grafana:latest`
- **Port**: 3000 (Grafana UI)
- **Auth**: Anonymous admin access enabled (login form disabled)
- **Plugin**: Yesoreyeram Infinity Datasource v1.0.1 (installed from GitHub at startup)
- **Token**: Azion Personal Token passed via `PERSONAL_TOKEN` environment variable from `.env` file

### Datasource: Infinity

Configured in `grafana/datasources/datasource.yml`:
- **Type**: Yesoreyeram Infinity (GraphQL-capable REST datasource)
- **Auth**: Token-based via `Authorization: Token ${TOKEN}` header
- **Accept header**: `application/json; version2;`
- **Target**: Azion Metrics GraphQL API endpoint

### Dashboards

#### Home Dashboard
- Welcome page with documentation links to Azion GraphQL API docs
- Dashboard list panel showing all dashboards in "Data Platform" folder

#### HTTP Metrics Dashboard
Main analytics dashboard with:

| Panel | Type | Metric | Description |
|-------|------|--------|-------------|
| Average Request Time | Gauge | `requestTime` avg | Latency gauge (0-500ms, green/red threshold at 250ms) |
| Average Bytes Sent | Gauge | `bytesSent` avg | Response size gauge (0-50KB) |
| Request Count | Time Series | SUM of `requests` | Request volume over time |
| Request Time Average | Time Series | AVG of `requestTime` | Latency trend over time |
| Request Time Distribution | Histogram | COUNT by `requestTime` buckets | Response time distribution |
| Bytes Sent Average | Time Series | AVG of `bytesSent` | Payload size trend |
| Requests Table | Table | Raw request data | Individual requests with host, method, status, cache status |

#### Datasets Dashboard
- GraphQL introspection query to discover available metrics datasets
- Table displaying query types and their fields

## GraphQL API

**Endpoint**: `https://api.azionapi.net/metrics/graphql`

### httpMetrics Query

Primary query supporting:
- **Aggregations**: SUM, AVG, COUNT
- **Group by**: timestamp, requestTime, configurationId, host, etc.
- **Filters**: Time range (`tsRange`), numeric (`requestTimeGt`, `bytesSentGt`)
- **Limit**: Up to 2000 records

### Available Fields

| Field | Description |
|-------|-------------|
| `ts` | Timestamp |
| `requests` | Request count |
| `requestTime` | Response time (ms) |
| `bytesSent` | Bytes in response |
| `configurationId` | Edge application ID |
| `host` | Request hostname |
| `requestMethod` | HTTP method |
| `upstreamCacheStatus` | Cache hit/miss |
| `status` | HTTP status code |
| `upstreamStatus` | Origin status code |

## File Structure

```
metrics-graphql-api-grafana/
├── docker-compose.yml                 # Single Grafana service
├── Insomnia.json                      # GraphQL query collection (9 queries)
├── grafana/
│   ├── datasources/
│   │   └── datasource.yml            # Infinity datasource config
│   └── dashboards/
│       ├── dashboard.yml             # Dashboard provisioning
│       ├── home/dashboard.json       # Welcome/navigation dashboard
│       ├── HttpMetrics/dashboard.json # HTTP analytics dashboard
│       └── datasets/dashboard.json   # Schema exploration dashboard
├── screenshots/
│   ├── http_metrics.png              # HTTP dashboard screenshot
│   └── datasets.png                  # Datasets dashboard screenshot
├── CLA.md                            # Contributor License Agreement
├── CODE_OF_CONDUCT.md                # Contributor Covenant
└── .github/workflows/CLA.yml        # CLA automation
```

## Technology Stack

- **Analytics**: Grafana (latest)
- **Datasource plugin**: Yesoreyeram Infinity Datasource v1.0.1
- **API**: Azion Metrics GraphQL API
- **Runtime**: Docker / Docker Compose
- **Auth**: Azion Personal Tokens
- **License**: MIT (public repository)
