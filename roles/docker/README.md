---
namespace: odem
collection: alloy
role: docker
---

# `odem.alloy.docker`

Deploy the Docker telemetry River fragments for Alloy plus an optional
cAdvisor container service:

- `docker_metrics.alloy` — two cAdvisor sources:
  - embedded `prometheus.exporter.cadvisor` over the Docker socket
  - `discovery.docker` + `prometheus.scrape` of a cAdvisor container
- `docker_logs.alloy` — `discovery.docker` + `loki.source.docker` for
  container logs
- `cadvisor-compose.yml` — a `gcr.io/cadvisor/cadvisor` container
  (host PID + privileged, read-only rootfs mounts) exposing its metrics
  on `127.0.0.1:{{ docker_cadvisor_port }}`

The cAdvisor container is added alongside the embedded exporter because
the embedded exporter cannot map per-container network namespaces back to
cgroups under cgroup v2, leaving `container_network_*` metrics empty.

## Default variables

| Variable | Default | Description |
|---|---|---|
| `docker_enable_logs` | `true` | Deploy the docker_logs.alloy fragment |
| `docker_enable_metrics` | `true` | Deploy the docker_metrics.alloy fragment |
| `docker_enable_cadvisor_service` | `true` | Deploy and start the cAdvisor container service |
| `docker_cadvisor_image` | `gcr.io/cadvisor/cadvisor:v0.51.0` | cAdvisor container image |
| `docker_cadvisor_compose_dir` | `/etc/alloy/cadvisor` | Directory holding the cAdvisor docker-compose.yml |
| `docker_cadvisor_port` | `8080` | Port cAdvisor exposes its metrics on (bound to 127.0.0.1) |
| `docker_cadvisor_scrape_interval` | `60s` | Scrape interval for the cAdvisor prometheus.scrape components |
| `docker_cadvisor_storage_duration` | `5m` | Embedded cAdvisor storage_duration (window of per-container metrics kept in memory) |
| `docker_cadvisor_enabled_metrics` | `[app, cpu, cpuLoad, disk, diskIO, memory, network, oom_event, percpu, perf_event, sched]` | Embedded cAdvisor metric kinds to enable |

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
