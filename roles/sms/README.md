---
namespace: odem
collection: alloy
role: sms
---

# `odem.alloy.sms`

Clone the [sms-webapp](https://github.com/simple-metrics-server/sms-webapp)
metrics exporter, run it as a systemd service, and configure Alloy to scrape
it for custom metrics export.

- `install` — the sms-webapp git clone at the common repo location
  (`/srv/repo/github/simple-metrics-server/sms-webapp`), a local Python
  virtual environment (`pip install -e .`, so git pulls take effect on
  service restart), the `/etc/default/sms-webapp` env file and the
  `sms-webapp.service` systemd unit (plain http on localhost, root-owned).
- `scrape` — the `sms_exporter_scrape.alloy` fragment forwarding the
  exporter's `/metrics` endpoint to the configured Prometheus remote.

The metric handler configs (`data/config/*.json` inside the clone) are
shipped and hot-reloaded by the repo itself — the role does not manage them.

## Default variables

| Variable | Default | Description |
|---|---|---|
| `sms_enable_exporter` | `true` | Clone, install and configure the sms exporter service |
| `sms_enable_scrape` | `true` | Render the Alloy sms scrape fragment |
| `sms_repo_url` | `https://github.com/simple-metrics-server/sms-webapp.git` | Git repository of the sms exporter |
| `sms_repo_version` | `main` | Branch, tag or commit to check out |
| `sms_repo_dest` | `{{ common_default_repo_dir }}/github/simple-metrics-server/sms-webapp` | Clone location (common repo dir) |
| `sms_bind` | `127.0.0.1` | Address the exporter binds to (scraped by the local Alloy agent) |
| `sms_port` | `9555` | Port the exporter listens on |
| `sms_scrape_interval` | `10s` | Alloy scrape interval for the exporter endpoint |
| `sms_app_scrape_interval` | `60` | Exporter SCRAPE_INTERVAL in seconds (handler collection cycle) |
| `sms_service_name` | `sms-webapp` | Name of the systemd service |
| `sms_env_file` | `/etc/default/sms-webapp` | Path to the exporter default env file |

Shared `base_*` variables (including `base_config_dir`) are provided by
the `odem.alloy.base` dependency — see [`../base/README.md`](../base/README.md).

## Dependencies

- `odem.alloy.base`

## Example usage

```yaml
- hosts: all
  become: true
  vars:
    sms_app_scrape_interval: 15
  roles:
    - odem.alloy.install
    - odem.alloy.sms
```

> `odem.alloy.install` must run before this role (and before any other
> `odem.alloy.*` fragment role): it wipes stale `*.alloy` fragments from
> `base_config_dir` and deploys the shared remotes, so every fragment gets
> re-rendered afterwards.

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)
