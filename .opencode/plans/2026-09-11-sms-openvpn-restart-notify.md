# Plan: sms-webapp OpenVPN metrics — odem-alloy follow-up

Status: sms-webapp fix already pushed by user and verified working on
`justaname` (client status file with ISO 8601 `Updated` timestamp now
parses; metrics present). Remaining approved work is one change in
this collection.

## Approved change (this workspace)

Add restart notification to the git-clone task so code pulled by role
re-runs takes effect (editable install requires a service restart to
re-import modules):

`roles/sms/tasks/install.yml` — task "Clone sms-webapp repository":

```yaml
- name: Clone sms-webapp repository
  ansible.builtin.git:
    repo: "{{ sms_repo_url }}"
    dest: "{{ sms_repo_dest }}"
    version: "{{ sms_repo_version }}"
    force: true
  become: true
  notify: Restart sms-webapp
```

Only addition: the `notify: Restart sms-webapp` line. The handler
exists in `roles/sms/handlers/main.yml` (systemd restart of
`{{ sms_service_name }}`); repeated notifications from the env/unit
template tasks collapse into one restart.

Notes:
- `ansible.builtin.git` reports changed when the checkout moves (new
  upstream commits) or when `force: true` discards local modifications
  — exactly the cases needing a restart. No-op runs stay `ok`.
- No README/argument_specs changes needed: the sms role README already
  documents that git pulls take effect on service restart.

## Open item (unanswered by user)

The host's `data/config/openvpn.json` was hand-edited to
`/var/log/openvpn-status.log`. The role clones with `force: true`, so
the next role run reverts it to the upstream default
(`/etc/openvpn/openvpn-status.log`) and OpenVPN metrics break again.
Proposed durable fix (not yet approved): add a `sms_openvpn_status_paths`
role variable, rendered as `OPENVPN_STATUS_PATH` into
`/etc/default/sms-webapp` (env override is supported by the exporter,
including the previously running version). Would touch:
`roles/sms/defaults/main.yml`, `roles/sms/meta/argument_specs.yml`,
`roles/sms/templates/sms-webapp.default.j2`, `roles/sms/README.md`.
