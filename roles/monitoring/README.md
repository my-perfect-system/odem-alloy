---
namespace: odem
collection: alloy
role: monitoring
---

# `odem.alloy.monitoring`

Deploy the monitoring-stack self-metrics River fragments for Alloy:

- `grafana_metrics.alloy` — `prometheus.scrape` of the Grafana `/metrics`
  endpoint
- `loki_metrics.alloy` — `prometheus.scrape` of the Loki `/metrics`
  endpoint
- `prometheus_metrics.alloy` — `prometheus.scrape` of the Prometheus
  `/metrics` endpoint

Each fragment is gated by its own toggle and its endpoint URL is fully
configurable (remote hosts, reverse-proxy paths, and https are supported).

## Default variables

| Variable | Default | Description |
|---|---|---|
| `monitoring_enable_grafana` | `true` | Deploy the grafana_metrics.alloy fragment |
| `monitoring_enable_loki` | `true` | Deploy the loki_metrics.alloy fragment |
| `monitoring_enable_prometheus` | `true` | Deploy the prometheus_metrics.alloy fragment |
| `monitoring_grafana_url` | `http://localhost:3000/metrics` | Full scrape URL of the Grafana metrics endpoint |
| `monitoring_loki_url` | `http://localhost:3100/metrics` | Full scrape URL of the Loki metrics endpoint |
| `monitoring_prometheus_url` | `http://localhost:9090/metrics` | Full scrape URL of the Prometheus metrics endpoint |
| `monitoring_grafana_scrape_interval` | `30s` | Alloy scrape interval for the Grafana metrics endpoint |
| `monitoring_loki_scrape_interval` | `30s` | Alloy scrape interval for the Loki metrics endpoint |
| `monitoring_prometheus_scrape_interval` | `30s` | Alloy scrape interval for the Prometheus metrics endpoint |

Shared `base_*` variables (including `base_config_dir`) are provided by
the `odem.alloy.base` dependency — see [`../base/README.md`](../base/README.md).

## Dependencies

- `odem.alloy.base`

## Example usage

```yaml
- hosts: all
  become: true
  vars:
    monitoring_loki_url: http://10.10.100.180:3100/metrics
  roles:
    - odem.alloy.install
    - odem.alloy.monitoring
```

> `odem.alloy.install` must run before this role (and before any other
> `odem.alloy.*` fragment role): it wipes stale `*.alloy` fragments from
> `base_config_dir` and deploys the shared remotes (including the
> `prometheus.remote_write.mimir` receiver these fragments forward to).

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)
