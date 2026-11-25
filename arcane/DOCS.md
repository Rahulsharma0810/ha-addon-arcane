# Arcane

Docker management UI ([getarcane.app](https://getarcane.app)).

## Requirements
- **Protection mode must be OFF** (add-on Info tab). Arcane needs the Docker socket.
- Host port 3552 must be free.

## First login
Username `admin`. The generated password is written to `/data/initial-admin-password` inside the add-on; or set `default_admin_username` / `default_admin_password` below before first start. You are asked to change it on first login.

## Options
| Option | Env var | Notes |
|---|---|---|
| encryption_key | ARCANE_ENCRYPTION_KEY | Blank = auto-generated once and kept. Changing it breaks stored secrets. |
| app_url | ARCANE_APP_URL | Public URL if behind a reverse proxy. |
| timezone | TZ | Blank = Home Assistant timezone. |
| log_level | ARCANE_LOG_LEVEL | debug, info, warn, error. |
| projects_directory | ARCANE_PROJECTS_DIRECTORY | Compose projects. Keep under `/share`. |
| default_admin_username / password | ARCANE_DEFAULT_ADMIN_* | Initial admin. |
| admin_static_api_key | ARCANE_ADMIN_STATIC_API_KEY | Bootstrap API key (for the HA integration). |
| trusted_proxies | ARCANE_TRUSTED_PROXIES | CIDRs of your reverse proxy. |
| env_vars | any | Extra environment variables. |

`/share` is also visible to the Docker daemon at `/mnt/data/supervisor/share`, so compose bind mounts under `/mnt/data/supervisor/share/...` work as written.
