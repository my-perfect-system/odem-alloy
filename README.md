# `odem.alloy` Ansible Collection

Grafana Alloy agent deployment and configuration for Debian 13.

## Galaxy metadata

- **namespace**: `odem`
- **name**: `alloy`
- **version**: `0.1.0`
- **dependencies**: `odem.base`, `ansible.posix`

See [`galaxy.yml`](galaxy.yml) for the canonical values.

## Roles

| Role | Purpose |
|---|---|
| [`odem.alloy.base`](roles/base/README.md) | Shared base role — common `base_*` variables and the `Restart alloy` handler. |
| [`odem.alloy.install`](roles/install/README.md) | Install the Grafana Alloy agent and deploy the base River config (logging + remotes). |
| [`odem.alloy.host`](roles/host/README.md) | Deploy host telemetry fragments (metrics_system + logs_system). |
| [`odem.alloy.docker`](roles/docker/README.md) | Deploy Docker telemetry fragments (docker_metrics + docker_logs). |
| [`odem.alloy.cadvisor`](roles/cadvisor/README.md) | Deploy the cadvisor fragment (container cAdvisor metrics). |
| [`odem.alloy.blackbox`](roles/blackbox/README.md) | Install the blackbox exporter and its Alloy scrape config. |
| [`odem.alloy.monitoring`](roles/monitoring/README.md) | Scrape the Grafana, Loki and Prometheus `/metrics` endpoints (monitoring-stack self-metrics). |

## Installation

```bash
ansible-galaxy collection install odem.alloy
```

## Documentation

- [`AGENTS.md`](AGENTS.md) — developer conventions

## License

GPL-3.0-or-later
