# AGENTS.md: maintainer notes for AI agents

Home Assistant add-on repository packaging [Arcane](https://github.com/getarcaneapp/arcane) (Docker management UI). Unofficial community add-on. Repo: https://github.com/Rahulsharma0810/ha-addon-arcane

## Layout
- `repository.yaml`: add-on repository metadata
- `arcane/config.yaml`: add-on manifest (bump `version` for every release)
- `arcane/Dockerfile`, `arcane/build.yaml`: copies `/app/arcane` from `getarcaneapp/manager:<ARCANE_VERSION>` onto `ghcr.io/hassio-addons/base:21.0.12`; built locally on the device (no `image:` key, no registry)
- `arcane/rootfs/etc/services.d/arcane/run`: bashio script mapping add-on options to `ARCANE_*` env vars
- `arcane/translations/en.yaml`: Configuration tab labels and examples. Keys must exactly match `schema` in `config.yaml`
- `arcane/DOCS.md`, `arcane/CHANGELOG.md`, `README.md`: user docs
- `arcane/icon.png`, `arcane/logo.png`: official Arcane assets (BSD-3, see `NOTICE-ARCANE-LICENSE`)

## Design decisions
- `docker_api: true` is required (Docker socket), so users must turn Protection mode off.
- No ingress. Arcane expects to be served from the root path, so access is via port 3552 plus `webui`.
- Data: `/app/data` is symlinked to the add-on's `/data` (included in HA backups).
- Encryption key: auto-generated once into `/data/.encryption_key` unless the user sets one.
- Timezone: empty option means use the Home Assistant timezone (`bashio::info.timezone`).
- `/share` is symlinked to `/mnt/data/supervisor/share` inside the container so Compose bind mounts, which are host paths, resolve identically.
- Initial admin password: Arcane writes it to `/data/initial-admin-password`; the run script prints it to the add-on log unless `default_admin_password` is set.

## Verified facts (from Arcane source)
- API keys must start with `arc_` and have at least 8 more characters (`backend/internal/apikey/service.go`).
- `ARCANE_ADMIN_STATIC_API_KEY` bootstraps a static admin key.
- Arcane has no MCP server. It has a CLI (`cli/`) and a REST API with OpenAPI docs.
- The Arcane image has no `cat` or shell utilities, so use `docker cp` to read files from it.

## Not verified
- Exact `X-API-Key` header name and API doc URL.
- Whether non-`arc_` static keys are rejected at startup.
- Behaviour on `armv7` (not built; only `aarch64` and `amd64` are declared).
- The password-printing background loop in `run` was added in 1.1.0 and has had only a `bash -n` syntax check.

## Release process
1. Edit files; bump `version` in `arcane/config.yaml`; add a `CHANGELOG.md` entry.
2. Validate YAML (needs pyyaml) and confirm `translations/en.yaml` keys equal `schema` keys. Run `bash -n` on the run script.
3. Commit, `git tag vX.Y.Z`, push `main` and the tag, then create a GitHub release.
4. To bump Arcane itself, change `ARCANE_VERSION` in the `Dockerfile` and `version` in `config.yaml`, and check the new image still has `/app/arcane`.
5. Users update from the Add-on Store; the image rebuilds locally.

## History
- 1.0.0: initial release (Arcane v3.1.1)
- 1.1.0: icon/logo, Configuration tab docs, HA timezone default, README with add-repository button, admin password logged on first start

## Rules for agents working here
- Never print, store or commit tokens, API keys or encryption keys.
- Get explicit user approval before pushing, tagging or releasing.
- Don't claim something works unless it was built and run; state what was only syntax-checked.
