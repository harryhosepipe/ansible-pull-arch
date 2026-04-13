# Secrets Plan

The first scaffold keeps secrets separate from the base machine convergence.

## Why

- the base playbook should still succeed on a fresh machine
- secrets failures should not block package/bootstrap validation
- Doppler integration can grow without reshaping the repo

## Initial approach

- keep secret-aware tasks in `playbooks/secrets.yml`
- install and configure Doppler only when `doppler_install=true`
- pass secret material into later playbooks through Doppler after login succeeds

## Expected future flow

1. base bootstrap converges the machine
2. secrets playbook installs/configures Doppler
3. the user authenticates once
4. secret-backed tasks consume env vars or generated files as needed

## Notes

For a public repo, avoid hardcoding any secret values or machine-specific tokens in Git.
