---
namespace: odem
collection: alloy
role: docker
---

# `odem.alloy.docker`

Deploy the Docker telemetry River fragments for Alloy:

- `docker_metrics.alloy` — `discovery.docker` + `prometheus.scrape`
  with a relabel drop list (`docker_metrics_exclude_containers`)
- `docker_logs.alloy` — `discovery.docker` + `loki.source.docker` for
  container logs

## Default variables

| Variable | Default | Description |
|---|---|---|
| `docker_enable_logs` | `true` | Deploy the docker_logs.alloy fragment |
| `docker_enable_metrics` | `true` | Deploy the docker_metrics.alloy fragment |
| `docker_metrics_exclude_containers` | `[]` | Container names dropped via relabel |

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