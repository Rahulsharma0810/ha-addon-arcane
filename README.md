<p align="center"><img src="arcane/logo.png" alt="Arcane" width="320"></p>

# Arcane add-on for Home Assistant

Run [Arcane](https://getarcane.app), a modern Docker management UI, directly on your Home Assistant OS or Supervised install. A lightweight alternative to Portainer.

[![Open your Home Assistant instance and show the add add-on repository dialog with this repository pre-filled.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2FRahulsharma0810%2Fha-addon-arcane)

## Features

- Manage containers, Compose projects, images, volumes and networks
- Settings configured from the add-on Configuration tab
- Timezone follows Home Assistant automatically
- Encryption key auto-generated and persisted
- Data kept in add-on storage and included in backups
- Works with the [Arcane Home Assistant integration](https://github.com/ProfessorQuantumUniverse/Arcane-HA)
- Supports `aarch64` and `amd64`

## Installation

1. Click the button above, then **Add** and **Close**. Or go to **Settings > Add-ons > Add-on Store > ⋮ > Repositories** and add:
   ```
   https://github.com/Rahulsharma0810/ha-addon-arcane
   ```
2. Install **Arcane**.
3. On the **Info** tab, turn **Protection mode** off (required for Docker access).
4. Start it and open `http://<home-assistant-ip>:3552`.

The first start builds the image locally and takes a few minutes. Stop any other Arcane container using port 3552 first.

## Documentation

Full options, examples (encryption key, public URL, API key, trusted proxies) and troubleshooting: [arcane/DOCS.md](arcane/DOCS.md).

## Security

This add-on needs Docker API access, which is effectively root on the host. Install it only if you accept that, and protect your admin password and API key.

## Credits and licence

Arcane is by [Kyle Mendell and contributors](https://github.com/getarcaneapp/arcane) under the BSD 3-Clause licence (see `NOTICE-ARCANE-LICENSE`); the name, icon and logo belong to them. This packaging is MIT licensed. It is an unofficial community add-on.
