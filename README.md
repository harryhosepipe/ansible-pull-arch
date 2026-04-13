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
- a neovim playbook
- Arch package installation
- XDG base directories and environment exports
- conservative, idempotent defaults
- Doppler-based Git and SSH setup as part of the base convergence flow

## Next additions

As you grow the repo, add isolated playbooks such as:

- `playbooks/shell.yml`
- `playbooks/neovim.yml`
- `playbooks/desktop.yml`
- `playbooks/secrets.yml`

## Current playbooks

- `playbooks/base.yml`: base packages, XDG directories, Doppler install, Git identity from Doppler, and SSH key setup
- `playbooks/git.yml`: global Git identity and editor
- `playbooks/github.yml`: installs GitHub CLI
- `playbooks/neovim.yml`: installs Neovim plus LazyVim starter and backs up existing Neovim state once
- `playbooks/secrets.yml`: manual rerun path for Doppler, Git identity, and SSH key setup

## Doppler Flow

The default bootstrap command stays the same:

```bash
curl -fsSL https://raw.githubusercontent.com/harryhosepipe/ansible-pull-arch/main/bin/bootstrap | bash
```

`playbooks/base.yml` now expects these Doppler settings:

- project: `base`
- config: `dev_personal`
- secrets: `GIT_USER_NAME`, `GIT_USER_EMAIL`, `SSH_PRIVATE_KEY`, `SSH_PUBLIC_KEY`

On a brand new machine:

1. run the bootstrap command
2. if Doppler is not authenticated yet, the playbook will stop with a clear message
3. run `doppler login`
4. rerun the same bootstrap command

You can still rerun the secrets portion manually with:

```bash
curl -fsSL https://raw.githubusercontent.com/harryhosepipe/ansible-pull-arch/main/bin/bootstrap | bash -s -- --playbook playbooks/secrets.yml
```

## XDG Defaults

The base playbook creates and exports:

- `XDG_CONFIG_HOME=~/.config`
- `XDG_CACHE_HOME=~/.cache`
- `XDG_DATA_HOME=~/.local/share`
- `XDG_STATE_HOME=~/.local/state`

Current app behavior:

- Doppler config is stored under `~/.config/doppler`
- Git global config is written to `~/.config/git/config`
- SSH remains under `~/.ssh`, since OpenSSH does not use XDG paths by default
