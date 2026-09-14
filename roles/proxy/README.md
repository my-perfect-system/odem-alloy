---
namespace: odem
collection: alloy
role: proxy
---

# `odem.alloy.proxy`

Deploy arbitrary metric-scrape River fragments for Alloy:

- one `proxy_metrics_<name>.alloy` fragment per `proxy_scrape_targets` entry,
  each scraping the entry's metric endpoint and forwarding it to the
  Prometheus remote configured by `odem.alloy.install`.

Endpoint URLs are parsed with `urlsplit`, so remote hosts, https, custom
ports, and reverse-proxy paths are supported.

## Default variables

| Variable | Default | Description |
|---|---|---|
| `proxy_enable` | `true` | Render the `proxy_metrics_<name>.alloy` fragments |
| `proxy_scrape_targets` | `[]` | List of `{name, url, scrape_interval}` metric endpoints to scrape (`scrape_interval` optional) |
| `proxy_default_scrape_interval` | `30s` | Alloy scrape interval used when a target does not set its own `scrape_interval` |

Shared `base_*` variables (including `base_config_dir`) are provided by
the `odem.alloy.base` dependency — see [`../base/README.md`](../base/README.md).

## Dependencies

- `odem.alloy.base`

## Example usage

```yaml
- hosts: all
  become: true
  vars:
    proxy_scrape_targets:
      - name: frontend
        url: https://frontend.example.com/metrics
      - name: api
        url: http://10.0.0.5:8080/metrics
        scrape_interval: 15s
  roles:
    - odem.alloy.install
    - odem.alloy.proxy
```

> `odem.alloy.install` must run before this role (and before any other
> `odem.alloy.*` fragment role): it wipes stale `*.alloy` fragments from
> `base_config_dir` and deploys the shared remotes (including the
> `prometheus.remote_write.mimir` receiver these fragments forward to).

## Role metadata

- **Min Ansible version**: `2.16.0`
- **License**: GPL-3.0-or-later
- **Platforms**: Debian (trixie)

## Related files

- [`meta/main.yml`](meta/main.yml) — galaxy_info + role dependencies
- [`meta/argument_specs.yml`](meta/argument_specs.yml) — variable spec (the source of the variable table above)
- [`defaults/main.yml`](defaults/main.yml) — variable defaults (the source of the default values above)