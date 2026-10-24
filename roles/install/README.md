---
namespace: odem
collection: alloy
role: install
---

# `odem.alloy.install`

Install the Grafana Alloy observability agent (official Grafana apt
repo) and deploy the base River config: `alloy_logging.alloy` +
`alloy_remotes.alloy` + `alloy_self.alloy` fragments, plus the
`/etc/default/alloy` env file.

All `*.alloy` fragments in the Alloy config dir are purged before
re-rendering, so stale or disabled features are cleaned up on the next
run. This is gated by `install_enable_purge_config`.

## Default variables

Shared `base_*` variables (`base_user`, `base_config_dir`, `base_env_file`,
ports, labels, remotes) are provided by the `odem.alloy.base` dependency —
see [`../base/README.md`](../base/README.md).

| Variable | Default | Description |
|---|---|---|
| `install_enable_change_user` | `false` | When true, change the `User=` line in `/usr/lib/systemd/system/alloy.service` to `install_service_user` (default `root`), so the agent runs with full privileges instead of the packaged `alloy` user |
| `install_service_user` | `root` | User the alloy systemd service runs as when `install_enable_change_user` is enabled |
| `install_enable_livedebugging` | `true` | Value written to the always-rendered `alloy_livedebugging.alloy` fragment — `true` enables live debugging in the Alloy web UI, `false` (Alloy's own default) disables it |
| `install_enable_purge_config` | `true` | When true, remove every `*.alloy` file in the Alloy config dir before rendering this role's fragments, so stale or disabled fragments are cleaned up; when false, existing files are left in place |
| `install_enable_self_collector` | `true` | When true, renders `alloy_self.alloy`, scraping Alloy's own `prometheus.exporter.self` metrics into the Mimir remote_write; when false the fragment is skipped and cleaned up on the next run |
| `install_enable_web_ui` | `true` | When true, writes `CUSTOM_ARGS=--server.http.listen-addr=0.0.0.0:<base_web_ui_port>` to the env file so the web UI is network-reachable; when false the line is omitted and Alloy keeps its packaged loopback-only default (`127.0.0.1:12345`) |

## Dependencies

- `odem.alloy.base`

## Example usage

```yaml
- hosts: all
  become: true
  roles:
    - odem.alloy.install
```

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)