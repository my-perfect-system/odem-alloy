---
namespace: odem
collection: alloy
role: host
---

# `odem.alloy.host`

Deploy the base host-telemetry River fragments for Alloy:

- `metrics_system.alloy` — `prometheus.exporter.unix` node_exporter-style
  metrics (collector set driven by `alloy_host_collectors`)
- `logs_system.alloy` — `loki.source.journal` + `loki.source.file` for
  journald and syslog files (`alloy_syslog_paths`)

## Default variables

| Variable | Default | Description |
|---|---|---|
| `host_enable_logs` | `true` | Deploy the logs_system.alloy fragment |
| `host_enable_metrics` | `true` | Deploy the metrics_system.alloy fragment |
| `alloy_syslog_paths` | (see defaults) | Syslog files tailed into Loki |
| `alloy_host_collectors` | (see defaults) | unix exporter collectors enabled |

Shared `base_*` variables (including `base_config_dir`) are provided by
the `odem.alloy.base` dependency — see [`../base/README.md`](../base/README.md).

## Dependencies

- `odem.alloy.base`

## Example usage

```yaml
- hosts: all
  become: true
  roles:
    - odem.alloy.install
    - odem.alloy.host
```

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)