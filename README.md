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
- **Verified status.** When you connect, VPNelec actively checks that everything is in place — tunnel up, your visible IP really changed, kill switch active, DNS through the tunnel, IPv6 blocked — and only then shows a green status summary. If something isn't right, it tells you exactly what.
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

**Protection starts when you connect, not at boot.** After a restart your device is online normally until you open VPNelec and choose Connect. If you have Kodi set to auto-resume playback on startup, connect first, then start playing.

---

## A note on security

The kill switch has been tested against several failure modes: the OpenVPN process being killed outright, being killed repeatedly, and the tunnel going dead while the process is still alive (interface down, traffic re-routed to the physical line). In each case nothing got through — not IPv4, IPv6, ICMP, or DNS. A direct attempt to reach the internet over the physical interface is blocked too. The kill switch works by interface, not by routing, so it holds even when the route falls back to the real connection.

DNS is handled so it can't leak after a tunnel crash: VPNelec connects openvpn to the server by its pre-resolved IP, so DNS can stay fully blocked the whole time (DNS through the tunnel keeps working while the tunnel is up).

This is reassuring, but it isn't a guarantee against every possible situation. If you depend on this for something serious, test your own setup.

On LibreELEC, the system component `connman` manages DNS. After certain network events (like a reconnect) it can briefly reset the DNS setting until VPNelec re-applies it. When you connect, VPNelec verifies that the tunnel is up, the external IP really changed, the kill switch is active on both IPv4 and IPv6, DNS goes through the tunnel, and IPv6 is blocked — and only then reports success.

---

## License

GPL-2.0-only.

**As-is. No warranty, no support, no pull requests.** This is a finished hobby project, shared publicly because it might be useful to someone. It isn't being actively developed, and there are no promises about how it works, whether it's maintained, or how secure it is. Use it at your own risk.
