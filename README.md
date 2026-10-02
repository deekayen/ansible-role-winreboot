# deekayen.win_reboot

[![CI](https://github.com/deekayen/ansible-role-winreboot/actions/workflows/ci.yml/badge.svg)](https://github.com/deekayen/ansible-role-winreboot/actions/workflows/ci.yml) [![Ansible Galaxy](https://img.shields.io/badge/galaxy-deekayen.win__reboot-blue.svg)](https://galaxy.ansible.com/ui/standalone/roles/deekayen/win_reboot/) [![Project Status: Inactive – The project has reached a stable, usable state but is no longer being actively developed; support/maintenance will be provided as time allows.](https://www.repostatus.org/badges/latest/inactive.svg)](https://www.repostatus.org/#inactive) ![BSD 3-Clause license](https://img.shields.io/badge/license-BSD%203--Clause-blue) ![Windows platform](https://img.shields.io/badge/platform-windows-lightgrey)

An Ansible role that reboots Windows hosts never, always, or only when Windows reports a pending reboot, with an option to run the reboot in check mode.

The role reads the `reboot_pending` fact that `ansible.windows.setup` gathers, combines it with `winreboot_reboot_behavior`, and calls the [`ansible.windows.win_reboot`](https://docs.ansible.com/ansible/latest/collections/ansible/windows/win_reboot_module.html) module when a reboot is due. The Galaxy name is `deekayen.win_reboot`, not `deekayen.winreboot`. It is a role, separate from the `ansible.windows.win_reboot` module it calls.

## Requirements

- ansible-core 2.15 or newer on the controller.
- The `ansible.windows` collection: `ansible-galaxy collection install ansible.windows`.
- A WinRM or SSH connection to the target with rights to restart it.
- Fact gathering left on for `if_required`. The decision reads `ansible_facts.reboot_pending`.

## Supported platforms

`meta/main.yml` declares Windows, all versions. CI lints the role and runs `ansible-playbook --syntax-check`; it does not apply the role to a Windows host.

## Installation

From Ansible Galaxy:

```bash
ansible-galaxy role install deekayen.win_reboot
ansible-galaxy collection install ansible.windows
```

Or pin it in `requirements.yml`:

```yaml
---
roles:
  - name: deekayen.win_reboot
    src: https://github.com/deekayen/ansible-role-winreboot.git
    scm: git
    version: main

collections:
  - name: ansible.windows
```

```bash
ansible-galaxy install -r requirements.yml
```

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `winreboot_simulate` | `false` | Run the `win_reboot` task in check mode, so the task reports a reboot but the host does not restart. |
| `winreboot_reboot_behavior` | `if_required` | When to reboot: `never`, `if_required` (only when `reboot_pending` is true), or `always`. |
| `winreboot_connect_timeout` | `5` | Seconds `win_reboot` waits for a single successful connection to the host before trying again. Must be at least 1. |
| `winreboot_reboot_timeout` | `3600` | Seconds `win_reboot` waits for the host to come back and respond to a test command. Must be at least `winreboot_connect_timeout`. |

## Behavior

- `if_required` uses `reboot_pending` from the last fact gathering, with a default of `false`. With `gather_facts: false`, or when an earlier task in the same play queued a reboot after facts were gathered, the role skips the reboot. Run `ansible.windows.setup` before the role if earlier tasks may have queued one.
- The role sets a `winreboot_do_reboot` fact on each host.

## Dependencies

None.

## Example playbook

Reboot only hosts with a pending reboot:

```yaml
---
- name: Reboot Windows hosts that need it.
  hosts: windows_patch_group_a

  pre_tasks:
    - name: Refresh facts so reboot_pending is current.
      ansible.windows.setup:

  roles:
    - deekayen.win_reboot
```

Preview which hosts would reboot, without restarting any:

```yaml
---
- name: Report which Windows hosts would reboot.
  hosts: windows_patch_group_a

  vars:
    winreboot_simulate: true

  roles:
    - deekayen.win_reboot
```

## Development

CI runs on every push to `main` and every pull request (see `.github/workflows/ci.yml`). It installs `ansible.windows`, runs `ansible-lint --profile production`, and syntax-checks `tests/test.yml`. To run the same checks locally:

```bash
pip3 install ansible-lint
ansible-galaxy collection install ansible.windows
ansible-lint --profile production
mkdir -p .ansible/roles && ln -sfn "$PWD" .ansible/roles/deekayen.win_reboot
ANSIBLE_ROLES_PATH=.ansible/roles:~/.ansible/roles ansible-playbook --syntax-check tests/test.yml -i tests/inventory
```

The repository also has a `.pre-commit-config.yaml`; run `pre-commit run --all-files` before pushing.

### Repository layout

| Path | Purpose |
| --- | --- |
| `tasks/main.yml` | Reboot decision and the `win_reboot` call. |
| `tasks/assert.yml` | Checks the two timeouts, tagged `always`. |
| `defaults/main.yml` | Every user-facing variable. |
| `meta/argument_specs.yml` | Argument types and the `winreboot_reboot_behavior` choices. |
| `requirements.txt` | Python packages for local linting (`ansible`, `ansible-lint`, `yamllint`). CI installs `ansible-lint` directly instead. |
| `tests/` | Syntax-check playbook and inventory used by CI. |

## Releases

Pushing a git tag runs `.github/workflows/release.yml`, which imports the tagged commit into Ansible Galaxy as `deekayen.win_reboot`. The import needs a `GALAXY_API_KEY` repository or organization secret.

## License

BSD 3-Clause. See [LICENSE](LICENSE).

## Authors

[Trond Hindenes](https://github.com/trondhindenes) wrote the original [ansibleroles-winreboot](https://github.com/trondhindenes/ansibleroles-winreboot). [David Norman](https://github.com/deekayen) forked and maintains this copy. Sponsorship links are in [.github/FUNDING.yml](.github/FUNDING.yml).
