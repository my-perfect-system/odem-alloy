# `odem.alloy` Ansible Collection

Grafana Alloy agent deployment and configuration for Debian 13.

## Galaxy metadata

- **namespace**: `odem`
- **name**: `alloy`
- **version**: `0.2.0`
- **dependencies**: `odem.base`, `ansible.posix`

See [`galaxy.yml`](galaxy.yml) for the canonical values.

## Roles

| Role | Purpose |
|---|---|
| [`odem.alloy.base`](roles/base/README.md) | Shared base role — common `base_*` variables and the `Restart alloy` handler. |
| [`odem.alloy.install`](roles/install/README.md) | Install the Grafana Alloy agent and deploy the base River config (logging + remotes + self metrics). |
| [`odem.alloy.host`](roles/host/README.md) | Deploy host telemetry fragments (metrics_system + logs_system). |
| [`odem.alloy.docker`](roles/docker/README.md) | Deploy Docker telemetry fragments (docker_metrics + docker_logs). |
| [`odem.alloy.blackbox`](roles/blackbox/README.md) | Install the blackbox exporter and its Alloy scrape config. |
| [`odem.alloy.sms`](roles/sms/README.md) | Clone and install the sms-webapp exporter service and scrape it for custom metrics export. |
| [`odem.alloy.monitoring`](roles/monitoring/README.md) | Scrape the Grafana, Loki and Prometheus `/metrics` endpoints (monitoring-stack self-metrics). |
| [`odem.alloy.fail2ban`](roles/fail2ban/README.md) | Ship fail2ban log files into Loki and add the `alloy` user to `adm` (fail2ban itself is installed by `odem.hardening.fail2ban`). |
| [`odem.alloy.proxy`](roles/proxy/README.md) | Render one scrape fragment per configured metric target, forwarding to the Prometheus remote. |

## Installation

```bash
ansible-galaxy collection install odem.alloy
```

## Documentation

- [`AGENTS.md`](AGENTS.md) — developer conventions

## License

GPL-3.0-or-later
