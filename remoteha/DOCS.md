# RemoteHA

Connects this Home Assistant to the RemoteHA service. It reports status for monitoring (free plan) and, with a Pro subscription, makes Home Assistant reachable at a private HTTPS URL. All connections are outbound, so you don't need port forwarding.

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
| `api_url` | RemoteHA service URL. Change this only for development. |
| `ha_url` | How the app reaches Home Assistant. The default `http://homeassistant:8123` works for most setups. Use `https://` if HA has SSL configured. |
