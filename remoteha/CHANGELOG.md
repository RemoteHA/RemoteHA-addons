# Changelog

## 0.2.0

- Your RemoteHA dashboard now shows pending updates for this Home Assistant: the operating system, Core, the Supervisor, apps, HACS, device firmware and other integrations, the same list as **Settings → Updates**. Updates you skipped in Home Assistant aren't shown. The app reports each update's name and versions every few minutes.
- The app now asks for access to the Home Assistant API, which it uses to read that list.

## 0.1.1

- The Home Assistant URL is now detected from Home Assistant's port and SSL setting, so setups that don't use port 8123 work without configuration. The option is now called **Local Home Assistant URL** and is empty by default; if you left it at the old default, detection takes over automatically.
- **Service URL** is now an advanced option, hidden unless you turn on **Show unused optional configuration options**. A value you already set is kept.

## 0.1.0

- First release: pair with your RemoteHA account using a one-time code, get status monitoring, and, with RemoteHA Pro, remote access through a private HTTPS URL.
