---
namespace: odem
collection: alloy
role: blackbox
---

# `odem.alloy.blackbox`

Install the Prometheus blackbox exporter and configure Alloy to scrape
its probe targets.

- `install` — the `prometheus-blackbox-exporter` package, default env
  file, systemd unit, and `blackbox.yml` modules config.
- `scrape` — the `blackbox_exporter_scrape.alloy` fragment, one probe
  target per `blackbox_probe_targets` entry.

## Default variables

| Variable | Default | Description |
|---|---|---|
| `blackbox_enable_exporter` | `true` | Install and configure the blackbox exporter |
| `blackbox_enable_scrape` | `true` | Render the Alloy blackbox scrape fragment |
| `blackbox_exporter_port` | `9115` | Port the blackbox exporter listens on |
| `blackbox_config_file` | `/etc/prometheus/blackbox.yml` | blackbox.yml modules config path |
| `blackbox_exporter_extra_args` | `""` | Extra blackbox exporter CLI args |
| `blackbox_modules` | `{}` | Blackbox module definitions (required when exporter enabled) |
| `blackbox_probe_targets` | `[]` | List of `{name, target, module}` probes (required when scrape enabled) |

Shared `base_*` variables (including `base_config_dir`) are provided by
the `odem.alloy.base` dependency — see [`../base/README.md`](../base/README.md).

## Dependencies

- `odem.alloy.base`

## Example usage

```yaml
- hosts: all
  become: true
  vars:
    blackbox_modules:
      http_2xx:
        prober: http
        timeout: 5s
        http:
          preferred_ip_protocol: ip4
          method: GET
          fail_if_not_ssl: true
    blackbox_probe_targets:
      - name: example
        target: https://example.com
        module: http_2xx
  roles:
    - odem.alloy.install
    - odem.alloy.blackbox
```

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)