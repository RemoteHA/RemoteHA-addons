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
3. Give it to the agent as `HA_TOKEN`. Environment variables can't be added to a running container, so recreate it. Print the command that starts the agent with its current settings (the same one as in [Updating the agent](#updating-the-agent)), and copy what it prints. Then remove the container:

   ```sh
   docker rm -f remoteha
   ```

   Run the command you copied with `-e HA_TOKEN=<token>` added before `ghcr.io/remoteha/remoteha-agent:latest`. The agent stays paired, because its credentials are on the `remoteha-data` volume. With Docker Compose, add `HA_TOKEN: <token>` under the agent's `environment:` and run `docker compose up -d`.

The token can do anything your account can. The agent only uses it to read Home Assistant's version and update list. You can delete it on the same **Security** tab at any time.

## Synology NAS

The agent runs as a container on the NAS, using the NAS's own network. The `HA_URL` below assumes Home Assistant runs in a container on the same NAS. If it runs on another device, use that device's address instead, for example `http://192.168.1.20:8123`. If you run Home Assistant OS in **Virtual Machine Manager**, install the RemoteHA app in Home Assistant instead (see [Installation](#installation)).

Get a pairing code from your RemoteHA dashboard first. It's valid for 15 minutes. The dashboard's **Synology NAS** tab shows these steps with your code filled in.

### DSM 7.2 and later (Container Manager)

1. Install **Container Manager** from the Package Center if you haven't already.
2. In Container Manager, open **Project** and click **Create**. Name it `remoteha`, set the path to a new folder such as `docker/remoteha`, and choose **Create docker-compose.yml** as the source.
3. Paste this, with your pairing code, then click **Next** until you reach **Done**. Container Manager downloads the agent and starts it.

   ```yaml
   services:
     remoteha:
       image: ghcr.io/remoteha/remoteha-agent:latest
       container_name: remoteha
       network_mode: host
       restart: unless-stopped
       environment:
         PAIRING_CODE: <code from your RemoteHA dashboard>
         HA_URL: http://127.0.0.1:8123
       volumes:
         - ./data:/data
   ```

### DSM 7.0 and 7.1 (Docker package)

1. Install **Docker** from the Package Center if you haven't already.
2. Go to **Control Panel → Terminal & SNMP** and turn on **Enable SSH service**.
3. On your computer, open a terminal and sign in with an administrator account: `ssh <your-dsm-user>@<your-nas-ip>`.
4. Run this, with your pairing code, and enter your DSM password when asked. The agent then also appears in the Docker package.

   ```sh
   sudo docker run -d --name remoteha --restart unless-stopped \
     --network host \
     -e PAIRING_CODE=<code from your RemoteHA dashboard> \
     -e HA_URL=http://127.0.0.1:8123 \
     -v remoteha-data:/data \
     ghcr.io/remoteha/remoteha-agent:latest
   ```

5. You can turn the SSH service off again afterwards.

### Then let Home Assistant trust the agent

Under **Settings → System → Network → HTTP server → Reverse proxy**, turn on **Trust X-Forwarded-For** and add `127.0.0.1` under **Trusted proxies** if Home Assistant runs on the NAS, or the NAS's own IP address if it runs on another device.

To show Home Assistant's version and pending updates, create a token as described [above](#optional-show-home-assistants-version-and-pending-updates). In Container Manager, stop the `remoteha` project, add `HA_TOKEN: <token>` under `environment:` in its YAML, then build it again. With the Docker package, follow the `docker run` steps above over SSH, with `sudo` before each command.

The agent works on Synology models that can run Container Manager or the Docker package.

## Updating the agent

Your RemoteHA dashboard says when a new version of the agent is available. The agent stays paired when you update it, because its credentials are on its data volume.

- **Home Assistant app:** in Home Assistant, go to **Settings → Apps**, open **RemoteHA** and click **Update**. If there's no Update button yet, open **Settings → Apps → Install app → ⋮ → Check for updates** first.
- **Docker Compose:** in the folder with your `docker-compose.yml`, run `docker compose pull && docker compose up -d`.
- **`docker run`:**
  1. Print the command that starts the agent with its current settings, and copy what it prints:

     ```sh
     docker inspect remoteha --format 'docker run -d --name remoteha --restart unless-stopped --network {{.HostConfig.NetworkMode}}{{range .Config.Env}}{{if eq (index (split . `=`) 0) `HA_URL` `HA_TOKEN` `API_URL` `LOG_LEVEL`}} -e {{.}}{{end}}{{end}}{{range .Mounts}} -v {{if .Name}}{{.Name}}{{else}}{{.Source}}{{end}}:{{.Destination}}{{end}} ghcr.io/remoteha/remoteha-agent:latest'
     ```

  2. Download the new version and remove the old container:

     ```sh
     docker pull ghcr.io/remoteha/remoteha-agent:latest
     docker rm -f remoteha
     ```

  3. Run the command you copied. It includes your Home Assistant token if you set one, so don't share it.

- **Synology, Container Manager:** open **Project**, select **remoteha** and choose **Action → Clean**. Then open **Image**, select `ghcr.io/remoteha/remoteha-agent` and click **Delete**. Back in **Project**, select **remoteha** and choose **Action → Build**: Container Manager downloads the new version and starts it. Or, over SSH in the project's folder: `sudo docker compose pull && sudo docker compose up -d`.
- **Synology, Docker package:** follow the `docker run` steps above over SSH, with `sudo` before each `docker`, including the one inside the quotes.

## Images

| Image | Used by |
| --- | --- |
| `ghcr.io/remoteha/{amd64,aarch64,armv7}-addon-remoteha` | The Home Assistant app (pulled by the Supervisor) |
| `ghcr.io/remoteha/remoteha-agent` | Docker installs (multi-arch) |

---

This repository is generated from the RemoteHA source on each release. Changes made directly here are overwritten.
