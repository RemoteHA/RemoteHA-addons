# RemoteHA apps for Home Assistant

This is the Home Assistant app repository (formerly called an add-on repository) for [RemoteHA](https://github.com/RemoteHA). It gives you secure remote access to Home Assistant without port forwarding.

## Installation

[![Add this repository to your Home Assistant.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2FRemoteHA%2FRemoteHA-addons)

Or add it manually:

1. In Home Assistant, go to **Settings → Apps → Install app**.
2. Open **⋮ → Repositories** and add `https://github.com/RemoteHA/RemoteHA-addons`.
3. Install **RemoteHA** and follow its **Documentation** tab.

## Apps

| App | Description |
| --- | --- |
| [RemoteHA](remoteha/DOCS.md) | Status monitoring and remote access to Home Assistant through a private HTTPS URL. |

## Running without Home Assistant OS

If you run Home Assistant in Docker, without the Supervisor, run the agent next to it. The image is `ghcr.io/remoteha/remoteha-agent`. Your RemoteHA dashboard shows the exact command.

---

This repository is generated from the RemoteHA source on each release. Changes made directly here are overwritten.
