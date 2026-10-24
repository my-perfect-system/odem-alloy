# AGENTS.md — odem-alloy

Grafana Alloy agent deployment and configuration — system-level roles
for installing and configuring the Alloy observability agent.

## Galaxy

- **namespace**: `odem`
- **name**: `alloy`
- **version**: `0.2.0`
- **dependencies**: `odem.base >=0.1.0`, `ansible.posix >=1.0.0`

## Roles

| Role | Description | Complexity |
|---|---|---|
| `odem.alloy.base` | Shared base role — no tasks. Carries the common `base_*` agent variables (user, config dir, ports, labels, remotes) and the shared `Restart alloy` handler (systemd restart + daemon-reload so unit/file changes are re-read). Every other role depends on it. | 1 |
| `odem.alloy.install` | Install Alloy from the Grafana apt repo, deploy `/etc/default/alloy` + `alloy_logging.alloy` + `alloy_remotes.alloy`, purge all `*.alloy` fragments (`install_enable_purge_config`, default true), and optionally change the service user (`install_enable_change_user` / `install_service_user`, default root — edits the `User=` line in `/usr/lib/systemd/system/alloy.service`). `alloy_livedebugging.alloy` always rendered with `enabled` from `install_enable_livedebugging` (web UI live debugging toggle). `alloy_self.alloy` scrapes Alloy's own `prometheus.exporter.self` metrics, gated by `install_enable_self_collector` (default true). Web UI network exposure gated by `install_enable_web_ui` (env-file `CUSTOM_ARGS` bind; false omits it so Alloy stays loopback-only). | 2 |
| `odem.alloy.host` | Deploy host telemetry fragments — `metrics_system.alloy` (unix node_exporter via `alloy_host_collectors`) and `logs_system.alloy` (journald + syslog files via `alloy_syslog_paths`). Each gated by `host_enable_metrics` / `host_enable_logs`. | 1 |
| `odem.alloy.docker` | Deploy Docker telemetry fragments — `docker_metrics.alloy` (embedded `prometheus.exporter.cadvisor` plus `discovery.docker` + scrape of a cAdvisor container into Mimir) and `docker_logs.alloy` (container logs to Loki), plus an optional `gcr.io/cadvisor/cadvisor` container service (host PID + privileged) deployed via docker compose. Gated by `docker_enable_metrics` / `docker_enable_logs` / `docker_enable_cadvisor_service`. | 2 |
| `odem.alloy.blackbox` | Install prometheus-blackbox-exporter (default env + systemd unit + `blackbox.yml` modules) and render `blackbox_exporter_scrape.alloy` per `blackbox_probe_targets`. Gated by `blackbox_enable_exporter` / `blackbox_enable_scrape`. | 2 |
| `odem.alloy.sms` | Clone the sms-webapp exporter (simple-metrics-server/sms-webapp) to the common repo location, install it into a local venv (`pip install -e .`), run it as a root systemd service on :9555 (plain http, localhost) and render `sms_exporter_scrape.alloy` for custom metrics export to Mimir. Handler configs (`data/config/*.json`) stay repo-managed. OpenVPN inputs are supplied via `sms_openvpn_config_paths` / `sms_openvpn_status_paths`, rendered as `OPENVPN_CONFIG_PATH` / `OPENVPN_STATUS_PATH` (paired by position; configs need `status-version 3`). Gated by `sms_enable_exporter` / `sms_enable_scrape`. | 2 |
| `odem.alloy.monitoring` | Deploy monitoring-stack self-metrics fragments — `grafana_metrics.alloy`, `loki_metrics.alloy`, `prometheus_metrics.alloy` (one `prometheus.scrape` per service, each URL fully configurable via `urlsplit` so remote hosts / reverse-proxy paths / https work). Each gated by `monitoring_enable_grafana` / `monitoring_enable_loki` / `monitoring_enable_prometheus`. | 1 |
| `odem.alloy.fail2ban` | Deploy the fail2ban log fragment — `fail2ban_logs.alloy` (`loki.source.file` tailing `fail2ban_log_paths` into Loki). Adds the `alloy` user to `adm` so it can read `/var/log/fail2ban.log`. Gated by `fail2ban_enable_logs`. fail2ban itself is installed by `odem.hardening.fail2ban`. | 1 |
| `odem.alloy.proxy` | Render one `proxy_metrics_<name>.alloy` fragment per `proxy_scrape_targets` entry (`{name, url, scrape_interval}` — interval optional, falls back to `proxy_default_scrape_interval`). Each is a `prometheus.scrape` of that endpoint, URL parsed via `urlsplit` (remote hosts / https / reverse-proxy paths / custom ports work), forwarding to the Mimir remote. Gated by `proxy_enable`. | 1 |

## Conventions

- Follows the cross-collection conventions documented in the root
  `AGENTS.md` / `manage/AGENTS.md` (task naming, toggle pattern,
  install/facts/configure sub-steps).
- `odem.alloy.base` is the single source of truth for shared `base_*`
  variables and the `Restart alloy` handler. Child roles declare it as a
  meta dependency and do **not** re-declare `base_*` vars or the handler.
- Roles will be system-level unless noted; per-user roles opt in via
  `user_roles.alloy_<x>` keys.
- **cAdvisor**: `docker_metrics.alloy` scrapes both the embedded
  `prometheus.exporter.cadvisor` and a cAdvisor container (via
  `discovery.docker`), because the embedded exporter cannot map per-container
  network namespaces under cgroup v2 (empty `container_network_*`). The
  discovery relabel matches `__meta_docker_container_name` — note Alloy's
  `discovery.docker` reports names with a leading slash (`/cadvisor`) and
  relabel regexes are fully anchored, so use `/?cadvisor`.
- `changelogs/` intentionally absent — added at first release.
