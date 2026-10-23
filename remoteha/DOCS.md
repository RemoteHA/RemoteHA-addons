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

## Options

| Option | Description |
| --- | --- |
| `pairing_code` | One-time code from the dashboard. |
| `ha_url` | **Local Home Assistant URL.** Where the app reaches Home Assistant on your network. Leave it empty and the app uses Home Assistant's own port and SSL setting, e.g. `http://homeassistant:8123`. Set it only if that address doesn't work for your setup. |
| `api_url` | **Service URL (advanced).** Hidden unless you turn on **Show unused optional configuration options**. Leave it unset; change it only for development or testing. |

## Pending updates

The dashboard shows which updates Home Assistant has waiting: the operating system, Core, the Supervisor, apps, HACS, device firmware and other integrations. It's the same list as **Settings → Updates**, so updates you skipped aren't shown. The app reads it through the Home Assistant API every few minutes and sends each update's name and versions to RemoteHA. It doesn't install anything. Install updates in Home Assistant as usual.
