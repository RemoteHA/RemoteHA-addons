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

If you run Home Assistant in Docker, without the Supervisor, run the agent as a container on the same Docker network as Home Assistant:

```sh
docker run -d --name remoteha --restart unless-stopped \
  --network <your-home-assistant-network> \
  -e PAIRING_CODE=<code from your RemoteHA dashboard> \
  -e HA_URL=http://homeassistant:8123 \
  -v remoteha-data:/data \
  ghcr.io/remoteha/remoteha-agent:latest
```

Your RemoteHA dashboard shows the exact command with your pairing code. Then let Home Assistant trust the agent: under **Settings → System → Network → HTTP server → Reverse proxy**, turn on **Trust X-Forwarded-For** and add your Docker network's subnet under **Trusted proxies**.

### Optional: show Home Assistant's version and pending updates

The agent needs an access token from Home Assistant to read these. Without one, everything else works. The Home Assistant app doesn't need this step.

1. In Home Assistant, sign in as an **administrator**. Click your name at the bottom of the sidebar to open your profile, then open the **Security** tab.
2. Scroll to **Long-lived access tokens**, click **Create token** and name it `RemoteHA`. Copy the token right away: Home Assistant shows it only once.
3. Give it to the agent as `HA_TOKEN`. Environment variables can't be added to a running container, so recreate it:

   ```sh
   docker rm -f remoteha
   ```

   Then run the `docker run` command above again with `-e HA_TOKEN=<token> \` added below the `HA_URL` line. The agent stays paired, because its credentials are on the `remoteha-data` volume. With Docker Compose, add `HA_TOKEN: <token>` under the agent's `environment:` and run `docker compose up -d`.

The token can do anything your account can. The agent only uses it to read Home Assistant's version and update list. You can delete it on the same **Security** tab at any time.

## Images

| Image | Used by |
| --- | --- |
| `ghcr.io/remoteha/{amd64,aarch64,armv7}-addon-remoteha` | The Home Assistant app (pulled by the Supervisor) |
| `ghcr.io/remoteha/remoteha-agent` | Docker installs (multi-arch) |

---

This repository is generated from the RemoteHA source on each release. Changes made directly here are overwritten.
