# Plan: sms role — two OpenVPN env vars (upstream sms-webapp refactor)

Upstream sms-webapp (main, a5bb130 "Refactor openvpn handler") changed the
OpenVPN handler contract: it now takes **two** env vars, arrays paired by
position — `OPENVPN_CONFIG_PATH` (OpenVPN config files, one per network)
and `OPENVPN_STATUS_PATH` (status files). Configs must set
`status-version 3` (else source skipped with warning); `mode server` marks
a server, anything else a client; the `status` directive inside the config
is no longer consulted. `env_list` accepts JSON arrays, CSV, bracketed
lists or space-separated strings. The role currently renders only
`OPENVPN_STATUS_PATH` from a singular-named list var.

## Decisions (user-approved)

- Variable naming: **plural rename** — `sms_openvpn_config_paths` +
  `sms_openvpn_status_paths` (drop `sms_openvpn_status_path`)
- Env format: **JSON via `to_json`**
- **Fail early** on length mismatch between the two lists
- **Include** the earlier-approved `notify: Restart sms-webapp` on the
  git-clone task
- Paths come from **host inventory**; role defaults stay `[]`

## Changes (all in odem-alloy `roles/sms`)

### 1. `defaults/main.yml`

Replace line 18 (`sms_openvpn_status_path: [/etc/openvpn/openvpn-status.log]`) with:

```yaml
sms_openvpn_config_paths: []
sms_openvpn_status_paths: []
```

(comment: paired by position with sms_openvpn_config_paths; configs must
set `status-version 3`)

### 2. `templates/sms-webapp.default.j2`

```
SCRAPE_INTERVAL={{ sms_app_scrape_interval }}
OPENVPN_CONFIG_PATH={{ sms_openvpn_config_paths | to_json }}
OPENVPN_STATUS_PATH={{ sms_openvpn_status_paths | to_json }}
```

(Empty list renders as `[]` → `env_list` returns `[]` → handler yields no
openvpn sources; safe default.)

### 3. `tasks/install.yml`

a. Add near the top (before the env-file render), after the apt task:

```yaml
- name: Validate openvpn path pairs
  ansible.builtin.assert:
    that: >-
      sms_openvpn_config_paths | length
      == sms_openvpn_status_paths | length
    fail_msg: >-
      sms_openvpn_config_paths and sms_openvpn_status_paths must have
      the same number of entries (paired by position)
  when: >-
    sms_openvpn_config_paths | length > 0
    or sms_openvpn_status_paths | length > 0
```

b. Git-clone task ("Clone sms-webapp repository", line ~13): add
`notify: Restart sms-webapp` (handler exists in `handlers/main.yml`;
editable install needs a restart to re-import modules).

### 4. `meta/argument_specs.yml`

Add after `sms_app_scrape_interval` (list of str, default `[]`, required
false, descriptions noting positional pairing and the status-version 3 /
`mode server` requirements). Remove nothing (spec had no openvpn entry
yet — none existed).

### 5. `README.md` (roles/sms)

- Variable table: add both new vars (defaults `[]`).
- Add a short note: the exporter's OpenVPN handler reads
  `OPENVPN_CONFIG_PATH`/`OPENVPN_STATUS_PATH` (paired by position) —
  provide them via host inventory; the OpenVPN config must contain
  `status-version 3`.

### 6. Repo root docs (light touch)

`AGENTS.md` sms role row: append that openvpn paths are supplied via the
two env vars. (Optional — keep minimal.)

## Not changed

- `data/config/openvpn.json` stays repo-managed (upstream now uses
  `openvpn.*` commands in `cmd`); the force-clone wipe of the host's
  hand-edit is no longer an issue since paths now come from the env file.
- `sms_exporter_scrape.alloy.j2`, service unit, handlers — unaffected.
- Entry point `.venv/bin/webapp-serve` still exists upstream
  (`pyproject.toml [project.scripts]`), so install task's `creates` check
  stays valid.

## Verification

- `ansible-playbook --syntax-check` on the playbook using the role (or
  `ansible-lint` if configured).
- Optional manual: `ANSIBLE_INVENTORY=... ansible -m ...` dry run or run
  against a test host with mismatched lists to confirm the assert fires.

## Follow-up (outside this repo)

- justaname inventory: set `sms_openvpn_config_paths:
  [/etc/openvpn/client.conf]`, `sms_openvpn_status_paths:
  [/var/log/openvpn-status.log]`; ensure the client config contains
  `status-version 3` and a `status /var/log/openvpn-status.log` directive
  (OpenVPN itself still needs the directive to write the file).
