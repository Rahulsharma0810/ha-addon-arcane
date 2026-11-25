# Arcane

[Arcane](https://getarcane.app) is a modern web UI for managing Docker: containers, Compose projects, images, volumes and networks. This add-on runs it next to Home Assistant and gives it access to the host Docker daemon.

## Before you start

1. **Turn Protection mode OFF** on the add-on **Info** tab. Arcane needs the Docker socket, and without this it will start but show no containers.
2. **Port 3552 must be free** on the host. Stop any other Arcane container first.
3. The first start builds the image on your device, which takes a few minutes.

## Quick start

1. Install and start the add-on.
2. Open the Web UI at `http://<home-assistant-ip>:3552`.
3. Log in as `admin`. If you set no initial password, Arcane generates one and the add-on shows it in the **Log** tab on first start (it is also stored in `/data/initial-admin-password`).
4. Change the password when prompted.

## Configuration

| Option | Default | Environment variable |
|---|---|---|
| `encryption_key` | auto-generated | `ARCANE_ENCRYPTION_KEY` |
| `app_url` | empty | `ARCANE_APP_URL` |
| `timezone` | empty (uses Home Assistant) | `TZ` |
| `log_level` | `info` | `ARCANE_LOG_LEVEL` |
| `projects_directory` | `/share/arcane/projects` | `ARCANE_PROJECTS_DIRECTORY` |
| `default_admin_username` | empty (`admin`) | `ARCANE_DEFAULT_ADMIN_USERNAME` |
| `default_admin_password` | empty (generated) | `ARCANE_DEFAULT_ADMIN_PASSWORD` |
| `admin_static_api_key` | empty | `ARCANE_ADMIN_STATIC_API_KEY` |
| `trusted_proxies` | empty | `ARCANE_TRUSTED_PROXIES` |
| `env_vars` | empty list | any |

### Timezone

Leave `timezone` empty. The add-on reads the timezone from **Settings > System > General** in Home Assistant. Set it only to override, for example `Asia/Kolkata`.

### Encryption key

Encrypts credentials Arcane stores (registry logins, remote environment tokens).

```yaml
encryption_key: 9f2c4b7a1e8d03a56b7c9d1e2f40a3b8
```

- Leave empty to auto-generate and keep a key. This is the safest choice.
- To set your own, use 32 random characters. Generate with `openssl rand -hex 16`.
- Changing it later makes stored secrets unreadable, so keep a copy.

### Public URL

Set only when you reach Arcane through a domain or reverse proxy.

```yaml
app_url: https://arcane.example.com
```

For LAN access, leave empty or use `http://192.168.0.2:3552`.

### API key (Home Assistant integration)

Lets the [Arcane integration](https://github.com/ProfessorQuantumUniverse/Arcane-HA) connect without creating a key manually. It must start with `arc_`.

```yaml
admin_static_api_key: arc_homeassistant_9f2c4b7a1e8d03a56b7c9d1e2f40a3b8
```

Then add the integration in Home Assistant with URL `http://<home-assistant-ip>:3552` and this key. Alternatively leave it empty and create a key under **Settings > API Keys** in Arcane. The key has admin rights, so treat it like a password.

### Trusted proxies

Only needed behind a reverse proxy (Nginx Proxy Manager, Caddy, Traefik, Cloudflare Tunnel). Comma-separated IPs or CIDRs.

```yaml
trusted_proxies: 172.30.33.0/24,192.168.0.10/32
```

### Additional environment variables

```yaml
env_vars:
  - name: ARCANE_JWT_REFRESH_EXPIRY
    value: 72h
```

See the [Arcane documentation](https://getarcane.app) for the full list.

## Compose projects and bind mounts

Arcane talks to the **host** Docker daemon, so bind-mount paths in your Compose files are host paths. `/share` is available at `/mnt/data/supervisor/share`:

```yaml
volumes:
  - /mnt/data/supervisor/share/vault/Brain:/app/data/Brain
```

## Data and backups

Arcane's database and keys are stored in the add-on's persistent `/data`, which is included in Home Assistant backups. Compose projects under `/share/arcane/projects` are included in backups only if you select the Share folder.

## Migrating from Portainer

Containers started by Portainer keep running when Portainer is removed, but become unmanaged. Recreate each stack in Arcane under **Projects**, deploy, verify, then remove the Portainer add-on. Both can run side by side during the move.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Add-on fails to start with a port error | Port 3552 is taken. Stop the other Arcane container. |
| No containers shown / Docker errors | Protection mode is still on. |
| Locked out after changing the key | The encryption key changed. Restore the old one. |
| Login redirects fail behind a proxy | Set `app_url` and `trusted_proxies`. |

## Support

- Add-on issues: https://github.com/Rahulsharma0810/ha-addon-arcane/issues
- Arcane itself: https://github.com/getarcaneapp/arcane
