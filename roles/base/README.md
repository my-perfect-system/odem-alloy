---
namespace: odem
collection: alloy
role: base
---

# `odem.alloy.base`

Shared base role for the `odem.alloy` collection. Carries the common
`base_*` agent variables and the shared `Restart alloy` handler. Every
other role declares it as a meta dependency.

It performs no tasks — it exists purely to provide shared defaults and
the restart handler through role-dependency propagation.

## Default variables

| Variable | Default | Description |
|---|---|---|
| `base_user` | `alloy` | System user the Alloy service runs as |
| `base_config_dir` | `/etc/alloy` | Directory holding the River config fragments |
| `base_env_file` | `/etc/default/alloy` | Path to the Alloy default env file |
| `base_web_ui_port` | `12345` | Port the Alloy web UI listens on |
| `base_metrics_port` | `9400` | Port Alloy publishes its own metrics on |
| `base_custom_labels` | `os=debian13,systype=hw,physloc=de` | Comma-separated `k=v` labels for remote_write |
| `base_remote_prometheus_url` | `http://10.10.100.180:9090/api/v1/write` | Prometheus remote_write endpoint |
| `base_remote_loki_url` | `http://10.10.100.180:3100/loki/api/v1/push` | Loki push endpoint |

## Dependencies

- `odem.base.identity`

## Example usage

```yaml
- hosts: all
  become: true
  roles:
    - role: odem.alloy.install
    - role: odem.alloy.host
```

(`base` is pulled in automatically as a dependency — never listed directly.)

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)