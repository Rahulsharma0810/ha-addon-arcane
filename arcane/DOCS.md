# Arcane

[Arcane](https://getarcane.app) is a modern web UI for managing Docker: containers, Compose projects, images, volumes and networks. This add-on runs it next to Home Assistant and gives it access to the host Docker daemon.

## Before you start

1. **Turn Protection mode OFF** on the add-on **Info** tab. Arcane needs the Docker socket, and without this it will start but show no containers.
2. **Port 3552 must be free** on the host. Stop any other Arcane container first.
3. The first start builds the image on your device, which takes a few minutes.

## Quick start

1. Install and start the add-on.
2. Open the Web UI at `http://<home-assistant-ip>:3552`.
3. Log in as `arcane` (or the `default_admin_username` you set). If you set no initial password, Arcane generates one and the add-on shows it in the **Log** tab on first start (it is also stored in `/data/initial-admin-password`).
4. Change the password when prompted.

## Configuration

| Option | Default | Environment variable |
|---|---|---|
| `encryption_key` | auto-generated | `ARCANE_ENCRYPTION_KEY` |
| `app_url` | empty | `ARCANE_APP_URL` |
| `timezone` | empty (uses Home Assistant) | `TZ` |
| `log_level` | `info` | `ARCANE_LOG_LEVEL` |
| `projects_directory` | `/share/arcane/projects` | `ARCANE_PROJECTS_DIRECTORY` |
| `default_admin_username` | empty (`arcane`) | `ARCANE_DEFAULT_ADMIN_USERNAME` |
| `default_admin_password` | empty (generated) | `ARCANE_DEFAULT_ADMIN_PASSWORD` |
| `admin_static_api_key` | empty | `ARCANE_ADMIN_STATIC_API_KEY` |
| `trusted_proxies` | empty | `ARCANE_TRUSTED_PROXIES` |
| `env_vars` | empty list | any |

### Timezone

Leave `timezone` empty. The add-on reads the timezone from **Settings > System > General** in Home Assistant. Set it only to override, for example `Asia/Kolkata`.

### Encryption key

Encrypts data Arcane stores, including the login-signing key, registry logins and remote environment tokens.

```yaml
encryption_key: 9f2c4b7a1e8d03a56b7c9d1e2f40a3b8
```

- **Leave empty (recommended).** A random key is generated on first start and kept in `/data/.encryption_key`.
- To set your own, use at least 32 random characters, for example from `openssl rand -hex 16`. This is not the same thing as an API key, so do not use an `arc_` value here.
- **Never change it after the first start.** If it differs from the key the database was created with, logins fail with a `500` error and the web UI shows a misleading "Invalid username or password". The add-on refuses to start in that case. Restore the old value, or clear the option to use the saved key.

### Public URL

Set only when you reach Arcane through a domain or reverse proxy.

```yaml
app_url: https://arcane.example.com
```

For LAN access, leave empty or use `http://192.168.0.2:3552`.

### API key (Home Assistant integration)

Lets the [Arcane integration](https://github.com/ProfessorQuantumUniverse/Arcane-HA) connect without creating a key by hand.

```yaml
default_admin_username: rahul
admin_static_api_key: arc_homeassistant_9f2c4b7a1e8d03a56b7c9d1e2f40a3b8
```

- It must start with `arc_` followed by at least 8 more characters. Generate one with `echo "arc_$(openssl rand -hex 16)"`.
- It is attached to the admin user named in `default_admin_username`. That must be **your actual global admin username**. If the option is empty Arcane looks for `arcane`, and if no such admin exists the key is not created.
- Clearing the option deletes the key from Arcane. Changing the value replaces the old key.
- The key has admin rights, so treat it like a password.
- Send it as the `X-Api-Key` header. Test it with:
  ```bash
  curl -H "X-Api-Key: arc_..." http://<home-assistant-ip>:3552/api/environments
  ```
- In Home Assistant, add the Arcane integration with URL `http://<home-assistant-ip>:3552` and this key.

Alternatively leave the option empty and create a key under **Settings > API Keys** in Arcane.

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

## Initial admin account

`default_admin_username` and `default_admin_password` are used **only on the very first start**, when Arcane has no users. Changing them later does not change an existing account. To change a password, use the Arcane UI, or reset it:

```bash
docker exec -it -e ARCANE_ALLOW_CLI_PASSWORD_RESET=true addon_container_name /app/arcane admin reset-password --username <user>
```

Find the container name with `docker ps --format '{{.Names}}' | grep -i arcane` (for this add-on it looks like `app_xxxxxxxx_arcane`). The variable must be passed with `-e`; it is not inherited from the add-on options. You are prompted for the new password twice.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Add-on fails to start with a port error | Port 3552 is taken. Stop the other Arcane container. |
| No containers shown / Docker errors | Protection mode is still on. |
| Login shows "Invalid username or password" but the log shows a `500` | The encryption key changed. Clear `encryption_key` or restore the original value. |
| API key does not work | Check `default_admin_username` matches your admin user, and the key starts with `arc_` plus 8 or more characters. Check the add-on **Log** tab for warnings about the default admin API key. |
| Login redirects fail behind a proxy | Set `app_url` and `trusted_proxies`. |

## Support

- Add-on issues: https://github.com/Rahulsharma0810/ha-addon-arcane/issues
- Arcane itself: https://github.com/getarcaneapp/arcane
