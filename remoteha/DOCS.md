# RemoteHA

Connects this Home Assistant to the RemoteHA service. It reports status for monitoring (free plan) and, with an Individual or Family subscription, makes Home Assistant reachable at a private HTTPS URL. All connections are outbound, so you don't need port forwarding.

## Setup

1. In the RemoteHA dashboard, add an instance and copy the **pairing code**.
2. Paste it into this app's `pairing_code` option on the **Configuration** tab and start the app.
3. Let Home Assistant trust the app. Without this, Home Assistant rejects proxied requests with HTTP 400.
   1. Go to **Settings → System → Network** and expand **HTTP server → Reverse proxy**.
   2. Turn on **Trust X-Forwarded-For** and add `172.30.33.0/24` under **Trusted proxies**.
   3. Click **Save**, then **Save and restart**.
   4. After the restart, click **Confirm** in the *Confirm new HTTP server configuration* dialog. If you don't confirm within 5 minutes, Home Assistant reverts the change.

   On Home Assistant versions older than 2026.10, add this to `configuration.yaml` instead and restart:

   ```yaml
   http:
     use_x_forwarded_for: true
     trusted_proxies:
       - 172.30.33.0/24
   ```

Pairing codes can only be used once. After pairing, the app stores its credentials and reconnects automatically. To move the instance to another account, generate a new code and enter it here.

The credentials aren't included in Home Assistant backups, so a backup can't be used to connect as your instance. After restoring one, pair the app again with a new code. Pairing again also disconnects any other copy of the app that was connected for this instance.

## Options

| Option | Description |
| --- | --- |
| `pairing_code` | One-time code from the dashboard. |
| `ha_url` | **Local Home Assistant URL.** Where the app reaches Home Assistant on your network. Leave it empty and the app uses Home Assistant's own port and SSL setting, e.g. `http://homeassistant:8123`. Set it only if that address doesn't work for your setup. |
| `share_logs` | **Share log errors.** On by default. Turn it off to keep Home Assistant's log private: the app then doesn't send log errors, and the other health checks keep working. |
| `api_url` | **Service URL (advanced).** Hidden unless you turn on **Show unused optional configuration options**. Leave it unset; change it only for development or testing. |

## Restarting or updating over remote access

Remote access runs through this app, so restarting, stopping or updating it cuts your connection while you're using Home Assistant from your RemoteHA URL. Home Assistant may then show *Failed to restart app: Unknown error, see supervisor logs*, because the reply never reaches your browser. The restart itself still goes through: the page reconnects within a minute once the app is running again. To avoid the message, restart the app from your local network.

## Pending updates

The dashboard shows which updates Home Assistant has waiting: the operating system, Core, the Supervisor, apps, HACS, device firmware and other integrations. It's the same list as **Settings → Updates**, so updates you skipped aren't shown. The app reads it through the Home Assistant API every few minutes and sends each update's name and versions to RemoteHA. It doesn't install anything. Install updates in Home Assistant as usual.

## Health checks

The dashboard also shows problems that are easy to miss until something stops working: integrations that failed to load or can't connect, recovery and safe mode, open Repairs (and which ones the next Core release breaks), errors in the log, backups that failed or are missing, apps that are set to start at boot but aren't running, low disk space, issues the Supervisor found, low batteries and unavailable devices. When it starts and every 15 minutes after that, the app reads them from Home Assistant and the Supervisor and sends RemoteHA what it needs to show them: the names of the integrations, apps and devices involved (and, for devices that are unavailable, their Home Assistant ID and whether they have a battery or are tracked on your network), Repairs' titles, battery levels, disk space, your backup schedule and locations (never the backup password), and the error messages Home Assistant gives.

Log errors are sent without IP addresses, URLs, email addresses or anything that looks like a token or key, and cut to 300 characters. Only errors and critical messages are sent, at most 20. To keep the log private, turn off `share_logs`. The app doesn't change anything in Home Assistant.
