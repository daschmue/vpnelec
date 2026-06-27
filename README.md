# VPNelec

Schlanker OpenVPN-Schalter mit echtem Killswitch für  LibreELEC, gemacht mit claude.ai.

Verbindet und trennt eine OpenVPN-Verbindung direkt aus Kodi – ohne SSH oder Kommandozeile. Der eingebaute iptables-Killswitch blockiert bei einem Tunnel-Abbruch sämtlichen ungetunnelten Verkehr, damit die echte IP nicht durchsickert.

## Funktionen

- Verbinden / Trennen per Klick aus einem einfachen Menü
- **Killswitch (iptables):** Bricht der Tunnel weg, wird aller Verkehr außerhalb des Tunnels blockiert. Erlaubt bleiben nur: der Tunnel selbst, der direkte Weg zum VPN-Server (für den Verbindungsaufbau), das lokale Netz (LAN) und DNS. IPv4 **und** IPv6 werden abgedeckt.
- Mehrere Profile: beliebig viele `.ovpn`-Konfigurationen importieren und per Menü umschalten
- Zugangsdaten pro Profil, lokal im Kodi-userdata-Verzeichnis gespeichert (nicht im Addon)
- Statusanzeige: Verbindungszustand, aktives Profil, Tunnel-Interface, aktuell nach außen sichtbare IP
- Notaus: löscht alle Firewall-Regeln und beendet OpenVPN, stellt die normale Verbindung wieder her

## Voraussetzungen

- Kodi 21 (Omega) oder kompatibel
- `openvpn` und `iptables` auf dem System vorhanden (bei LibreELEC der Fall)
- Das Setzen von iptables-Regeln und das Starten von OpenVPN erfordert entsprechende Systemrechte. Auf LibreELEC läuft Kodi mit den nötigen Rechten; unter einem normalen Linux-Desktop mit eingeschränkten Benutzerrechten greift der Killswitch unter Umständen nicht.

## Installation

1. Die Datei `service.vpnelec-x.y.z.zip` aus den [Releases](../../releases) herunterladen.
2. In Kodi: Add-ons → **Aus ZIP-Datei installieren** → die heruntergeladene ZIP auswählen.
   (Dafür muss unter Einstellungen → System → Add-ons die Option **Unbekannte Quellen** aktiviert sein.)
3. Addon öffnen, **Profil importieren (.ovpn)** wählen, `.ovpn`-Datei auswählen, Zugangsdaten eingeben.
4. **Verbinden** wählen.

Es sind keinerlei VPN-Daten im Addon enthalten. Jeder trägt seine eigene `.ovpn` und seine eigenen Zugangsdaten ein.

## Hinweis zur Sicherheit

Der Killswitch wurde gegen den harten Abbruch des OpenVPN-Prozesses getestet (das häufigste Ausfall-Szenario): nach `killall -9 openvpn` kam über keinen der geprüften Wege (IPv4, IPv6, ICMP, direkte IP ohne DNS) Verkehr durch. Eine erschöpfende Prüfung gegen alle denkbaren Sonderfälle ist das nicht. Wer maximale Sicherheit braucht, prüft seinen eigenen Aufbau selbst.

## Lizenz

GPL-2.0-only.

---

**As-is. Keine Garantie, kein Support, keine Pull Requests.** Dies ist ein abgeschlossenes Hobbyprojekt, das öffentlich bereitgestellt wird, weil es jemandem nützen könnte. Es wird nicht weiterentwickelt, und es gibt keine Zusagen zu Funktion, Wartung oder Sicherheit. Nutzung auf eigenes Risiko.
