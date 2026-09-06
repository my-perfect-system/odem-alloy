---
namespace: odem
collection: alloy
role: install
---

# `odem.alloy.install`

Install the Grafana Alloy observability agent (official Grafana apt
repo) and deploy the base River config: `logging.alloy` +
`remotes.alloy` fragments, plus the `/etc/default/alloy` env file.

Stale `*.alloy` fragments are removed before re-rendering, so disabled
features are cleaned up on the next run.

## Default variables

Shared `base_*` variables (`base_user`, `base_config_dir`, `base_env_file`,
ports, labels, remotes) are provided by the `odem.alloy.base` dependency —
see [`../base/README.md`](../base/README.md).

| Variable | Default | Description |
|---|---|---|
| `install_enable_change_user` | `false` | When true, change the `User=` line in `/usr/lib/systemd/system/alloy.service` to `install_service_user` (default `root`), so the agent runs with full privileges instead of the packaged `alloy` user |
| `install_service_user` | `root` | User the alloy systemd service runs as when `install_enable_change_user` is enabled |
| `install_enable_livedebugging` | `false` | Value written to the always-rendered `livedebugging.alloy` fragment — `true` enables live debugging in the Alloy web UI, `false` (the Alloy default) disables it |

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