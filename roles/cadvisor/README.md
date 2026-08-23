---
namespace: odem
collection: alloy
role: cadvisor
---

# `odem.alloy.cadvisor`

Deploy the `cadvisor.alloy` River fragment for Alloy — the embedded
`prometheus.exporter.cadvisor` exporter over the Docker socket, scraped
into the Mimir remote_write receiver.

## Default variables

Shared `base_*` variables (including `base_config_dir`) are provided by
the `odem.alloy.base` dependency — see [`../base/README.md`](../base/README.md).
This role declares no additional variables.

## Dependencies

- `odem.alloy.base`

## Example usage

```yaml
- hosts: all
  become: true
  roles:
    - odem.alloy.install
    - odem.alloy.cadvisor
```

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)