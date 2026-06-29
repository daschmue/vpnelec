# VPNelec

A lightweight OpenVPN switch with a real kill switch for Kodi / LibreELEC. Built with claude.ai.

Connect and disconnect a VPN straight from the Kodi menu — no SSH, no command line. If the tunnel ever drops, a built-in kill switch blocks everything that isn't going through the tunnel, so your real IP address can't slip out.

> **Made for LibreELEC.** That's where it's built and tested: OpenVPN, iptables and the right privileges are all there out of the box. On other Kodi setups it needs manual work to run at all — and may not run at all (see Requirements).

---

## What it does

- **Connect / disconnect from a simple menu.** No typing, just pick an option.
- **Kill switch.** If the VPN tunnel drops unexpectedly, all traffic that isn't inside the tunnel is blocked instantly. Your real IP stays hidden. Both IPv4 and IPv6 are covered (IPv6 is blocked entirely, since it's a common leak path).
- **DNS that doesn't leak.** By default, DNS lookups are sent through the tunnel using Quad9, so the sites you visit can't be seen by your internet provider. You can change this per profile (see below).
- **Multiple VPN profiles.** Import as many `.ovpn` files as you like and switch between them from the menu.
- **Stops playback on disconnect.** When you disconnect, VPNelec stops any video that's playing first — otherwise the player could quietly reload the stream over your normal connection.
- **Self-healing tunnel.** If the connection stalls, OpenVPN automatically rebuilds it instead of sitting there dead.
- **Status view.** After connecting, a summary pops up: where you appear to be, your visible IP, whether the kill switch is active, and whether DNS is going through the tunnel.
- **Panic button.** One option clears every firewall rule and stops the VPN, putting your connection back to normal — for when you just want out.

Your credentials are stored locally on your own device (in Kodi's user data folder), never inside the addon.

---

## DNS modes (per profile)

Set under *Manage profiles -> pick a profile -> DNS mode*. Three choices:

- **Quad9 through the tunnel** *(default)* — private, leak-free. DNS lookups go through the VPN. Best for privacy.
- **DNS from the .ovpn** — uses the DNS server your provider put in the `.ovpn` file, if there is one. Also goes through the tunnel.
- **Local (router)** — uses your home router for DNS. This can leak which sites you look up, **but** it keeps local names working (for example reaching a NAS or Samba share by its name instead of its IP).

Short version: leave it on Quad9 for privacy. Switch to Local only if you need to reach devices on your home network by name.

---

## Requirements

- Kodi 21 (Omega) or compatible
- **LibreELEC is the supported platform.** There, `openvpn` and `iptables` are already installed, and Kodi runs with the privileges needed to set firewall rules and start OpenVPN. Nothing extra to do.

On any other system, expect manual setup before it can work — and on some systems it can't work at all:

- **Regular Linux (desktop Kodi, Raspberry Pi OS, a NAS, etc.):** `openvpn` usually has to be installed first, and the Kodi user normally runs without root. You'd have to grant that user passwordless `sudo` rights for `openvpn` and `iptables` (and possibly `ip`/`connmanctl`) so the addon can do its job. This isn't tested and isn't documented step by step here.
- **Windows and Android:** not supported. The addon relies on Linux tools (`iptables`, `connman`), which aren't there.

In short: on LibreELEC it just works; anywhere else is on your own.

---

## Installing

1. Download `service.vpnelec-x.y.z.zip` from the [Releases page](https://github.com/daschmue/vpnelec/releases).
2. In Kodi: **Add-ons -> Install from ZIP file ->** pick the downloaded ZIP. (You'll need *Unknown sources* turned on under **Settings -> System -> Add-ons**.)
3. **Restart Kodi once** after installing, so the background service starts fresh.
4. Open the addon, choose **Import profile (.ovpn)**, pick your `.ovpn` file, and enter your username and password.
5. Choose **Connect**.

No VPN data is bundled with the addon — everyone brings their own `.ovpn` and their own login.

A tip on setup: import profiles and enter credentials **on the device itself** (with a keyboard/remote). The Kodi web interface (Chorus) handles text-entry dialogs poorly, so first-time setup is smoother on-screen. Day-to-day connecting and disconnecting works fine from the web interface.

---

## A note on security

The kill switch has been tested against the most common failure — the OpenVPN process being killed outright. After forcing it to crash, nothing got through on any path that was checked (IPv4, IPv6, ICMP, or a direct IP with no DNS). That's reassuring, but it isn't a guarantee against every possible situation. If you depend on this for something serious, test your own setup.

On LibreELEC, the system component `connman` manages DNS. After certain network events (like a reconnect) it can briefly reset the DNS setting until VPNelec re-applies it. The status view tells you whether DNS is currently going through the tunnel.

---

## License

GPL-2.0-only.

**As-is. No warranty, no support, no pull requests.** This is a finished hobby project, shared publicly because it might be useful to someone. It isn't being actively developed, and there are no promises about how it works, whether it's maintained, or how secure it is. Use it at your own risk.
