# Changelog

## 0.3.4

- The app keeps reporting to RemoteHA when Home Assistant is slow to answer, instead of showing as offline. A report that fails is sent again within a minute, rather than at the next scheduled one.
- Remote access: fixed a rare case where the connector could stop responding after a very long log line. It now also finishes requests in flight when the app stops, and its error messages show on your dashboard and in the app log.
- Log errors that are shared with RemoteHA now have IPv6 addresses removed in every form.

## 0.3.3

- Backups of Home Assistant no longer contain this app's connection to RemoteHA, so anyone who gets hold of a backup can't use it to connect as your instance. After restoring a backup, pair the app again with a new pairing code from your dashboard.
- Updated the remote access connector (cloudflared) to 2026.10.5, which includes Cloudflare's latest security fixes.

## 0.3.2

- Updated the remote access connector (cloudflared) to 2026.10.4, which includes Cloudflare's latest security fixes.

## 0.3.1

- Your RemoteHA dashboard now tells unavailable devices apart. Devices that are just away from your network, such as phones and laptops tracked by your router, are no longer listed. Battery-powered devices that stop responding are flagged as a problem, because their battery may be empty. Devices that have been gone for over a week are grouped in one note, and you can hide the ones you don't care about. For this, the app now also sends each unavailable device's Home Assistant ID, and whether it has a battery or is tracked on your network.

## 0.3.0

- Your RemoteHA dashboard now checks this Home Assistant's health, and shows problems that are easy to miss until something stops working: integrations that failed to load or can't connect, recovery and safe mode, apps that stopped although they start at boot, low disk space, issues the Supervisor found, low batteries and unavailable devices. It also shows open Repairs, with a warning before updating Core when the new release breaks one of them, errors in Home Assistant's log, and backups that failed, are missing, or are only stored on this device. The app reads these when it starts and every 15 minutes after that.
- New option **Share log errors** (`share_logs`, on by default). Turn it off to keep Home Assistant's log private. Messages that are sent have IP addresses, URLs and tokens removed.

## 0.2.3

- The app now tells RemoteHA whether it runs as a Home Assistant app or in Docker, so your dashboard shows only the instructions that apply to it.

## 0.2.2

- Fixed remote access when another Cloudflare tunnel (cloudflared) runs on the same machine with host networking, as on a Synology NAS. The app's connector could fail to start while RemoteHA showed it as running, and the address returned Cloudflare error 1033.

## 0.2.1

- The app now tells RemoteHA whether the Supervisor manages Home Assistant. Without it (Home Assistant Container), Home Assistant never lists Core updates, so RemoteHA checks for new releases itself and shows them in your dashboard.

## 0.2.0

- Your RemoteHA dashboard now shows pending updates for this Home Assistant: the operating system, Core, the Supervisor, apps, HACS, device firmware and other integrations, the same list as **Settings → Updates**. Updates you skipped in Home Assistant aren't shown. The app reports each update's name and versions every few minutes.
- The app now asks for access to the Home Assistant API, which it uses to read that list.

## 0.1.1

- The Home Assistant URL is now detected from Home Assistant's port and SSL setting, so setups that don't use port 8123 work without configuration. The option is now called **Local Home Assistant URL** and is empty by default; if you left it at the old default, detection takes over automatically.
- **Service URL** is now an advanced option, hidden unless you turn on **Show unused optional configuration options**. A value you already set is kept.

## 0.1.0

- First release: pair with your RemoteHA account using a one-time code, get status monitoring, and, with RemoteHA Pro, remote access through a private HTTPS URL.
