# Contributing to metrics-graphql-api-grafana

## Development Setup

1. Clone the repository:
   ```bash
   git clone git@github.com:aziontech/metrics-graphql-api-grafana.git
   cd metrics-graphql-api-grafana
   ```

2. Create a `.env` file with your Azion Personal Token:
   ```
   PERSONAL_TOKEN=<your_token>
   ```
   Get a token from the [Azion Console](https://www.azion.com/en/documentation/products/accounts/personal-tokens).

3. Start Grafana:
   ```bash
   docker-compose up -d
   ```

4. Open `http://localhost:3000` in your browser.

## Making Changes

### Adding New Dashboards

1. Create a new directory under `grafana/dashboards/` (e.g., `grafana/dashboards/MyDashboard/`)
2. Add your `dashboard.json` file
3. Register the new dashboard provider in `grafana/dashboards/dashboard.yml`:
   ```yaml
   - name: 'My Dashboard'
     folder: 'Data Platform'
     type: file
     options:
       path: /etc/grafana/provisioning/dashboards/MyDashboard
   ```
4. Restart the container: `docker-compose restart`

### Modifying Existing Dashboards

1. Edit the dashboard in Grafana UI first (changes are allowed via `updateIntervalSeconds`)
2. Export the modified dashboard as JSON (Dashboard Settings → JSON Model)
3. Replace the corresponding `dashboard.json` file

### Adding GraphQL Queries

Update `Insomnia.json` with new query definitions for testing. The Insomnia collection serves as documentation for available API queries.

### Datasource Configuration

The Infinity datasource is configured in `grafana/datasources/datasource.yml`. Modify headers or authentication settings there.

## Contributor License Agreement

This is a public repository. All contributors must sign the CLA (automated via GitHub Actions workflow `CLA.yml`).

## Pull Request Process

1. Create a branch from `main`
2. Make changes and test locally with `docker-compose up`
3. Open a PR — CLA, compliance, and security checks will run
4. Ensure all CI checks pass before requesting review

## CI Checks

| Check | Description |
|-------|-------------|
| CLA | Contributor License Agreement signature |
| Compliance | Engineering standards verification |
| Security | SAST, secret detection, dependency scanning |

## Code Review

- All changes require peer review before merging
- Changes to `.github/` require `@aziontech/team-delivery-engineering` approval
- Changes to security configs require `@aziontech/security-office` approval
