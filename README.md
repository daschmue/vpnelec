# VPNelec

*[Deutsch](#deutsch) · [English](#english)*

---

## Deutsch

Schlanker OpenVPN-Schalter mit echtem Killswitch für LibreELEC, gemacht mit claude.ai.

Verbindet und trennt eine OpenVPN-Verbindung direkt aus Kodi – ohne SSH oder Kommandozeile. Der eingebaute iptables-Killswitch blockiert bei einem Tunnel-Abbruch sämtlichen ungetunnelten Verkehr, damit die echte IP nicht durchsickert.

### Funktionen

- Verbinden / Trennen per Klick aus einem einfachen Menü
- **Killswitch (iptables):** Bricht der Tunnel weg, wird aller Verkehr außerhalb des Tunnels blockiert. Erlaubt bleiben nur: der Tunnel selbst, der direkte Weg zum VPN-Server (für den Verbindungsaufbau), das lokale Netz (LAN) und DNS. IPv4 und IPv6 werden abgedeckt.
- **DNS-Modus pro Profil:** Für jedes Profil lässt sich wählen, wie DNS aufgelöst wird – über den in der `.ovpn` hinterlegten DNS-Server (falls vorhanden), über Quad9 (9.9.9.9) durch den Tunnel, oder über das lokale Netz (Router). Bei den getunnelten Modi werden die DNS-Anfragen wirklich durch den Tunnel geleitet, sodass kein DNS-Leak entsteht. Die Einstellung gilt ab dem nächsten Verbinden; bei bestehender Verbindung wird sie sofort angewendet.
- Mehrere Profile: beliebig viele `.ovpn`-Konfigurationen importieren und per Menü umschalten
- Zugangsdaten pro Profil, lokal im Kodi-userdata-Verzeichnis gespeichert (nicht im Addon)
- Statusanzeige: Verbindungszustand, aktives Profil, Tunnel-Interface, aktuell nach außen sichtbare IP, aktiver DNS-Modus
- Notaus: löscht alle Firewall-Regeln und beendet OpenVPN, stellt die normale Verbindung wieder her

### Voraussetzungen

- Kodi 21 (Omega) oder kompatibel
- `openvpn` und `iptables` auf dem System vorhanden (bei LibreELEC der Fall)
- Das Setzen von iptables-Regeln und das Starten von OpenVPN erfordert entsprechende Systemrechte. Auf LibreELEC läuft Kodi mit den nötigen Rechten; unter einem normalen Linux-Desktop mit eingeschränkten Benutzerrechten greift der Killswitch unter Umständen nicht.

### Installation

1. Die Datei `service.vpnelec-x.y.z.zip` aus den [Releases](https://github.com/daschmue/vpnelec/releases) herunterladen.
2. In Kodi: Add-ons → Aus ZIP-Datei installieren → die heruntergeladene ZIP auswählen. (Dafür muss unter Einstellungen → System → Add-ons die Option *Unbekannte Quellen* aktiviert sein.)
3. Addon öffnen, *Profil importieren (.ovpn)* wählen, `.ovpn`-Datei auswählen, Zugangsdaten eingeben.
4. *Verbinden* wählen.

Es sind keinerlei VPN-Daten im Addon enthalten. Jeder trägt seine eigene `.ovpn` und seine eigenen Zugangsdaten ein.

Den DNS-Modus eines Profils stellt man unter *Profile verwalten → Profil wählen → DNS-Modus* ein.

### Hinweis zur Sicherheit

Der Killswitch wurde gegen den harten Abbruch des OpenVPN-Prozesses getestet (das häufigste Ausfall-Szenario): nach `killall -9 openvpn` kam über keinen der geprüften Wege (IPv4, IPv6, ICMP, direkte IP ohne DNS) Verkehr durch. Eine erschöpfende Prüfung gegen alle denkbaren Sonderfälle ist das nicht. Wer maximale Sicherheit braucht, prüft seinen eigenen Aufbau selbst.

**DNS-Hinweis:** Im Modus „lokal" werden DNS-Anfragen über den Router aufgelöst und verlassen den Tunnel – das kann ein DNS-Leak sein, hält aber die lokale Namensauflösung (z. B. Samba-Freigaben per Name) am Leben. In den getunnelten Modi (Quad9 oder `.ovpn`-DNS) laufen die Anfragen durch den Tunnel; dabei kann die Auflösung lokaler Gerätenamen wegfallen (Geräte per IP-Adresse funktionieren weiter). Auf LibreELEC verwaltet connman die DNS-Einstellung; bei manchen Netzwerkereignissen (z. B. Reconnect) kann sie kurzzeitig zurückgesetzt werden, bis der Modus erneut greift.

### Lizenz

GPL-2.0-only.

**As-is. Keine Garantie, kein Support, keine Pull Requests.** Dies ist ein abgeschlossenes Hobbyprojekt, das öffentlich bereitgestellt wird, weil es jemandem nützen könnte. Es wird nicht weiterentwickelt, und es gibt keine Zusagen zu Funktion, Wartung oder Sicherheit. Nutzung auf eigenes Risiko.

---

## English

A lightweight OpenVPN switch with a real kill switch for LibreELEC, built with claude.ai.

Connects and disconnects an OpenVPN connection directly from within Kodi – no SSH or command line required. The built-in iptables kill switch blocks all untunneled traffic if the tunnel drops, so your real IP cannot leak.

### Features

- Connect / disconnect with a click from a simple menu
- **Kill switch (iptables):** If the tunnel drops, all traffic outside the tunnel is blocked. Only the following remain permitted: the tunnel itself, the direct route to the VPN server (for connection setup), the local network (LAN) and DNS. IPv4 and IPv6 are both covered.
- **Per-profile DNS mode:** For each profile you can choose how DNS is resolved – via the DNS server defined in the `.ovpn` (if present), via Quad9 (9.9.9.9) through the tunnel, or via the local network (router). In the tunneled modes, DNS queries are actually routed through the tunnel, so no DNS leak occurs. The setting takes effect on the next connection; if a connection is already active, it is applied immediately.
- Multiple profiles: import any number of `.ovpn` configurations and switch between them via the menu
- Credentials per profile, stored locally in Kodi's userdata directory (not in the addon)
- Status view: connection state, active profile, tunnel interface, currently externally visible IP, active DNS mode
- Panic button: removes all firewall rules and stops OpenVPN, restoring normal connectivity

### Requirements

- Kodi 21 (Omega) or compatible
- `openvpn` and `iptables` present on the system (this is the case on LibreELEC)
- Setting iptables rules and starting OpenVPN requires appropriate system privileges. On LibreELEC, Kodi runs with the necessary privileges; on a regular Linux desktop with restricted user rights, the kill switch may not take effect.

### Installation

1. Download the file `service.vpnelec-x.y.z.zip` from the [Releases](https://github.com/daschmue/vpnelec/releases).
2. In Kodi: Add-ons → Install from ZIP file → select the downloaded ZIP. (For this, the option *Unknown sources* must be enabled under Settings → System → Add-ons.)
3. Open the addon, choose *Import profile (.ovpn)*, select your `.ovpn` file, and enter your credentials.
4. Choose *Connect*.

No VPN data whatsoever is included in the addon. Everyone supplies their own `.ovpn` and their own credentials.

The DNS mode of a profile is set under *Manage profiles → select profile → DNS mode*.

### Security Note

The kill switch has been tested against a hard termination of the OpenVPN process (the most common failure scenario): after `killall -9 openvpn`, no traffic got through via any of the tested paths (IPv4, IPv6, ICMP, direct IP without DNS). This is not an exhaustive test against every conceivable edge case. Anyone who needs maximum security should verify their own setup.

**DNS note:** In "local" mode, DNS queries are resolved via the router and leave the tunnel – this can be a DNS leak, but keeps local name resolution working (e.g. Samba shares by name). In the tunneled modes (Quad9 or `.ovpn` DNS), queries are routed through the tunnel; local device-name resolution may then stop working (devices addressed by IP continue to work). On LibreELEC, connman manages the DNS setting; on some network events (e.g. reconnect) it may be reset briefly until the mode is re-applied.

### License

GPL-2.0-only.

**As-is. No warranty, no support, no pull requests.** This is a finished hobby project, made public because it might be useful to someone. It is not under active development, and there are no guarantees regarding functionality, maintenance, or security. Use at your own risk.
