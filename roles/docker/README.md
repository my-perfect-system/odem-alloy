---
namespace: odem
collection: alloy
role: docker
---

# `odem.alloy.docker`

Deploy the Docker telemetry River fragments for Alloy:

- `docker_metrics.alloy` — `prometheus.exporter.cadvisor` +
  `prometheus.scrape` for system-wide per-container metrics. No
  per-container `prometheus.io/*` labels required.
- `docker_logs.alloy` — `discovery.docker` + `loki.source.docker` for
  container logs

To let the unprivileged `alloy` user inspect `/var/lib/docker`, the
metrics task installs the `acl` package and grants the `alloy` user
read+execute access (including a default ACL for newly created entries)
via `setfacl`.

## Default variables

| Variable | Default | Description |
|---|---|---|
| `docker_enable_logs` | `true` | Deploy the docker_logs.alloy fragment |
| `docker_enable_metrics` | `true` | Deploy the docker_metrics.alloy fragment |
| `docker_cadvisor_storage_duration` | `"5m"` | cAdvisor storage_duration (window of per-container metrics kept in memory) |
| `docker_cadvisor_scrape_interval` | `"60s"` | Scrape interval for the cAdvisor prometheus.scrape component |
| `docker_cadvisor_enabled_metrics` | `[app, cpu, cpuLoad, disk, diskIO, memory, network, oom_event, percpu, perf_event, sched]` | cAdvisor metric kinds to enable (overrides the default disabled list) |

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
    - odem.alloy.docker
```

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)
