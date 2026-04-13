# linux-env

Personal Arch Linux environment bootstrap built around `ansible-pull`.

The repo is structured so you can:

- bootstrap a machine with one command
- keep machines converged to the repo over time
- test each setup area in isolation

## Layout

- `bin/bootstrap`: minimal shell bootstrap that installs prerequisites and runs `ansible-pull`
- `playbooks/`: one top-level playbook per setup area
- `roles/`: reusable task groups
- `group_vars/all/`: shared settings

## First-run flow

The bootstrap script keeps its job small:

1. install the minimum prerequisites needed for `ansible-pull`
2. run the chosen playbook from this repo against `localhost`

## Intended usage

Default bootstrap command:

```bash
curl -fsSL https://raw.githubusercontent.com/harryhosepipe/ansible-pull-arch/main/bin/bootstrap | bash
```

Safer inspectable variant:

```bash
curl -fsSLo /tmp/linux-env-bootstrap.sh https://raw.githubusercontent.com/harryhosepipe/ansible-pull-arch/main/bin/bootstrap
bash /tmp/linux-env-bootstrap.sh
```

Optional playbook override:

```bash
curl -fsSL https://raw.githubusercontent.com/harryhosepipe/ansible-pull-arch/main/bin/bootstrap | bash -s -- --playbook playbooks/git.yml
```

## Current scope

The initial scaffold includes:

- a base playbook
- a git playbook
- Arch package installation
- conservative, idempotent defaults
- a separate secrets playbook for Doppler-based Git and SSH setup

## Next additions

As you grow the repo, add isolated playbooks such as:

- `playbooks/shell.yml`
- `playbooks/neovim.yml`
- `playbooks/desktop.yml`
- `playbooks/secrets.yml`

## Current playbooks

- `playbooks/base.yml`: base packages and common machine setup
- `playbooks/git.yml`: global Git identity and editor
- `playbooks/github.yml`: installs GitHub CLI
- `playbooks/secrets.yml`: installs Doppler, sets Git identity from Doppler, and installs SSH keys

## Secrets Playbook

`playbooks/secrets.yml` currently expects these Doppler settings:

- project: `base`
- config: `dev_personal`
- secrets: `GIT_USER_NAME`, `GIT_USER_EMAIL`, `SSH_PRIVATE_KEY`, `SSH_PUBLIC_KEY`

Run it with:

```bash
curl -fsSL https://raw.githubusercontent.com/harryhosepipe/ansible-pull-arch/main/bin/bootstrap | bash -s -- --playbook playbooks/secrets.yml
```
