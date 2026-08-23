# AGENTS.md — odem-alloy

Grafana Alloy agent deployment and configuration — system-level roles
for installing and configuring the Alloy observability agent. The first
role is not yet created.

## Galaxy

- **namespace**: `odem`
- **name**: `alloy`
- **version**: `0.1.0`
- **dependencies**: `odem.base >=0.1.0`, `ansible.posix >=1.0.0`

## Roles

None yet.

## Conventions

- Follows the cross-collection conventions documented in the root
  `AGENTS.md` / `manage/AGENTS.md` (task naming, toggle pattern,
  install/facts/configure sub-steps).
- Roles will be system-level unless noted; per-user roles opt in via
  `user_roles.alloy_<x>` keys.
- `changelogs/` intentionally absent — added at first release.
