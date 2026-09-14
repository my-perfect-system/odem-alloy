---
namespace: odem
collection: alloy
role: fail2ban
---

# `odem.alloy.fail2ban`

Deploy the fail2ban log-telemetry River fragment for Alloy:

- `fail2ban_logs.alloy` — `loki.source.file` tailing the fail2ban log
  file(s) (`fail2ban_log_paths`) and forwarding them to Loki.

The role also adds the `alloy` user (`base_user`) to the `adm` group so
it can read `/var/log/fail2ban.log`.

fail2ban itself is installed by `odem.hardening.fail2ban`; this role only
ships its logs to the Alloy endpoint.

## Default variables

| Variable | Default | Description |
|---|---|---|
| `fail2ban_enable_logs` | `true` | Deploy the fail2ban_logs.alloy fragment |
| `fail2ban_log_paths` | `[/var/log/fail2ban.log]` | Fail2ban log files tailed into Loki |

Shared `base_*` variables (including `base_config_dir`) are provided by
the `odem.alloy.base` dependency — see [`../base/README.md`](../base/README.md).

## Dependencies

- `odem.alloy.base`

## Example usage

```yaml
- hosts: all
  become: true
  roles:
    - odem.hardening.fail2ban
    - odem.alloy.install
    - odem.alloy.fail2ban
```

> `odem.alloy.install` must run before this role (and before any other
> `odem.alloy.*` fragment role): it wipes stale `*.alloy` fragments from
> `base_config_dir` and deploys the shared remotes (including the
> `loki.write.loki` receiver this fragment forwards to).

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)
