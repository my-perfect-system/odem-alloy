# AGENTS.md — odem-alloy

Grafana Alloy agent deployment and configuration — system-level roles
for installing and configuring the Alloy observability agent.

## Galaxy

- **namespace**: `odem`
- **name**: `alloy`
- **version**: `0.1.0`
- **dependencies**: `odem.base >=0.1.0`, `ansible.posix >=1.0.0`

## Roles

| Role | Description | Complexity |
|---|---|---|
| `odem.alloy.base` | Shared base role — no tasks. Carries the common `base_*` agent variables (user, config dir, ports, labels, remotes) and the shared `Restart alloy` handler. Every other role depends on it. | 1 |
| `odem.alloy.install` | Install Alloy from the Grafana apt repo, deploy `/etc/default/alloy` + `logging.alloy` + `remotes.alloy`, wipe stale `*.alloy` fragments, assign journal/adm/docker group membership. | 2 |
| `odem.alloy.host` | Deploy host telemetry fragments — `metrics_system.alloy` (unix node_exporter via `alloy_host_collectors`) and `logs_system.alloy` (journald + syslog files via `alloy_syslog_paths`). Each gated by `host_enable_metrics` / `host_enable_logs`. | 1 |
| `odem.alloy.docker` | Deploy Docker telemetry fragments — `docker_metrics.alloy` (discovery + scrape + relabel drop list) and `docker_logs.alloy` (container logs to Loki). Each gated by `docker_enable_metrics` / `docker_enable_logs`. | 1 |
| `odem.alloy.cadvisor` | Deploy `cadvisor.alloy` — embedded cAdvisor exporter over the Docker socket, scraped into Mimir. | 1 |
| `odem.alloy.blackbox` | Install prometheus-blackbox-exporter (default env + systemd unit + `blackbox.yml` modules) and render `blackbox_exporter_scrape.alloy` per `blackbox_probe_targets`. Gated by `blackbox_enable_exporter` / `blackbox_enable_scrape`. | 2 |

## Conventions

- Follows the cross-collection conventions documented in the root
  `AGENTS.md` / `manage/AGENTS.md` (task naming, toggle pattern,
  install/facts/configure sub-steps).
- `odem.alloy.base` is the single source of truth for shared `base_*`
  variables and the `Restart alloy` handler. Child roles declare it as a
  meta dependency and do **not** re-declare `base_*` vars or the handler.
- Roles will be system-level unless noted; per-user roles opt in via
  `user_roles.alloy_<x>` keys.
- `changelogs/` intentionally absent — added at first release.
