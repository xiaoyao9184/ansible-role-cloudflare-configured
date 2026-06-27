# cloudflare-configured

`cloudflare-configured` is an Ansible role for installing, configuring, and
controlling `cloudflared` across a mixed fleet. It supports native package or
binary installation on Linux, macOS, Windows, and OpenWrt hosts, plus a Docker
delivery mode for hosts that should run `cloudflared` as a container.

The role runs in five stages:

1. `setupping`: gather or normalize platform facts and load OS-specific vars.
2. `validating`: validate role inputs before changing the host.
3. `installing`: install or update `cloudflared`.
4. `configuring`: optionally create tunnels, credentials files, and config
   files.
5. `controlling`: optionally install/register and control the runtime service.

## Requirements

Install the following on the control node or target hosts as needed:

- Ansible 2.9 or newer.
- `community.docker >= 3.6.0` when `cloudflared_installation_mode: docker`.
- `community.openwrt >= 1.1.0` when managing OpenWrt hosts.
- Docker on Docker-mode target hosts.
- Homebrew on macOS hosts when using `cloudflared_macos_source: brew`.
- winget on Windows hosts when using `cloudflared_windows_source: winget`.

Tunnel creation runs on the controller through delegated localhost tasks. When
you use `cloudflared_account_cert_file` and `cloudflared_tunnel_name`, the role
checks for a controller-side `cloudflared` binary and automatically downloads a
usable release binary when it is missing.

## Supported Platforms

The role includes platform-specific installation and service paths for:

- Debian and Ubuntu.
- Red Hat family hosts, including Fedora, Enterprise Linux, Rocky Linux, and
  Amazon Linux 2023.
- macOS.
- Windows.
- OpenWrt with `opkg` or `apk`.
- Docker.

## Role Variables

The defaults live in `defaults/main.yml`. The most common variables are listed
below.

## Example Playbooks

For complete runnable examples, use the Molecule scenarios under
`molecule/core220/` as the primary reference. The `converge.*.yml` files in
those scenarios demonstrate the supported installation sources, config file
modes, credentials file modes, tunnel creation workflow, Docker mode, and
service-control behavior.

## Testing

Users who want validation evidence should review the GitHub Actions workflow
results for the role. The workflow definitions live under `.github/workflows/`
and include all-scenario and per-scenario tox and tox-ansible runs.

Developers who want to run or debug the same checks locally in VS Code should
start with `.vscode/tasks.json` and `.vscode/launch.json`.

Pixi defines Python and Ansible Core environments, and Molecule scenarios are
organized under `molecule/core*/<scenario>`.

Run all tox environments for one Python version:

```bash
pixi run -e py312 tox
```

Run one Molecule environment:

```bash
pixi run -e py312 tox -e molecule-py3.12-2.20
```

Run a specific scenario:

```bash
MOLECULE_SCENARIO_NAME=docker-debian-13 pixi run -e py312 tox -e molecule-py3.12-2.20
```

Run tox-ansible:

```bash
pixi run -e py312 tox-ansible
```

Generate helper files and matrix data:

```bash
python3 scripts/generate_tox_matrix.py discover-molecule-scenarios
python3 scripts/generate_tox_matrix.py filter-tox-envs --scenario-name docker-debian-13
python3 scripts/generate_tox_ansible_matrix.py filter-tox-envs --scenario-name docker-debian-13
pixi run -e default python scripts/generate_tox_deps.py --pyproject-output tox/py312/pyproject.toml
```

Generated `pyproject.toml` and `galaxy.yml` files are test artifacts and should
not be edited manually.

## License

Apache-2.0
