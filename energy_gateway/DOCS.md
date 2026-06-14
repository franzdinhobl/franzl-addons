# Energy Gateway

Intelligentes Energiemanagement für dein Zuhause — optimiert PV-Anlage, Batterie, Wallbox und Wärmepumpe herstellerübergreifend.

## Voraussetzungen

- Home Assistant OS 2024.1 oder neuer
- Franzl App (iOS / Android)

Eine **PostgreSQL-Datenbank ist im Add-on enthalten** — kein separates DB-Add-on
und keine DB-Konfiguration nötig.

## Installation

1. Füge dieses Repository zu deinen Add-on-Quellen hinzu:
   `https://github.com/franzdinhobl/franzl-addons`
   (am einfachsten über den „Add to my Home Assistant"-Button auf
   [getfranzl.com](https://getfranzl.com))
2. Installiere das **Franzl Gateway** Add-on und **starte** es. Das Add-on wird
   als vorgebautes Image geladen (kein lokales Kompilieren); die eingebaute
   Datenbank wird beim ersten Start automatisch angelegt
3. Im Seitenmenü erscheint "Energy Gateway" — öffne das Panel
4. Dort wird ein **9-stelliger Aktivierungscode** angezeigt
5. Lade die **Franzl App** aus dem App Store / Play Store
6. Erstelle einen Account und gib den Aktivierungscode ein
7. Die App erkennt automatisch deine Geräte — fertig!

## Konfiguration

| Option | Beschreibung | Standard |
|--------|-------------|----------|
| `log_level` | Logging-Level (debug, info, warning, error) | `info` |
| `db_url` | Nur für eine **externe** PostgreSQL setzen (SQLAlchemy/asyncpg-URL). Leer = eingebaute DB. | leer |
| `activation_url` | Cloud Function URL für Box-Aktivierung | Voreingestellt |

## Unterstützte Geräte

Das Gateway erkennt automatisch alle Geräte, die in Home Assistant eingerichtet sind:

- **PV-Wechselrichter**: Fronius, SMA, Huawei, Kostal, RCT, Victron, u.v.m.
- **Batteriespeicher**: BYD, Fronius, RCT, Victron, Sonnen, u.v.m.
- **Wallboxen**: OCPP 1.6, go-eCharger, Easee, Heidelberg, openWB
- **Wärmepumpen**: SG-Ready (alle Hersteller), Modbus
- **Stromzähler**: Shelly 3EM, Fronius Smart Meter, sonstige HA-Sensoren
- **Klimaanlagen & Lüftung**: Über HA Climate-Entities

## So funktioniert's

Das Energy Gateway läuft lokal auf deinem Home Assistant — keine Cloud für die Steuerung nötig. Es liest die Messwerte deiner Geräte, berechnet den optimalen Energiefluss und steuert Batterie, Wallbox und Wärmepumpe automatisch.

Die App zeigt dir in Echtzeit:
- Aktuelle Erzeugung, Verbrauch und Netzeinspeisung
- Batterie-Ladestand und Prognose
- Optimierungs-Entscheidungen mit Begründung
- Tages- und Monatsberichte (Pro)

## Backups & Wiederherstellung

### Automatische Home-Assistant-Backups

Das Add-on ist vollständig in die Home-Assistant-Backups integriert
(Einstellungen → System → Backups). Bei jedem Backup passiert automatisch:

1. Unmittelbar vor dem Backup erstellt das Add-on einen **konsistenten
   Datenbank-Dump** (`pg_dump`) unter `/data/backup/gateway_db.dump`
2. Der Dump und alle Einstellungen (Aktivierung, Firebase, Tunnel) landen im
   HA-Backup — die rohen PostgreSQL-Clusterdateien werden bewusst
   ausgeschlossen, da sie im laufenden Betrieb nicht konsistent kopierbar sind

Das Add-on läuft während des Backups normal weiter (Hot Backup).

### Tägliche lokale Sicherung

Zusätzlich erstellt das Add-on **jede Nacht um 03:30 Uhr** automatisch einen
lokalen Datenbank-Dump unter `/data/backups/gateway-JJJJMMTT.dump`.
Es werden die **letzten 7 Tage** aufbewahrt, ältere Dumps werden automatisch
gelöscht. So gibt es auch ohne regelmäßige HA-Backups immer einen aktuellen
Wiederherstellungspunkt.

### Wiederherstellung (automatisch)

Wird ein HA-Backup wiederhergestellt (z.B. auf einer neuen SD-Karte oder einer
neuen Box), erkennt das Add-on beim Start automatisch, dass die Datenbank leer
ist, und spielt den Dump aus dem Backup ein — im Log erscheint
`Restoring database from backup`. Es ist **kein manueller Schritt nötig**.

### Wiederherstellung (manuell)

Falls du gezielt einen der täglichen Dumps einspielen willst (Add-on muss
laufen), im Add-on-Terminal bzw. via `docker exec`:

```sh
/usr/libexec/postgresql16/pg_restore -h 127.0.0.1 -U gateway -d gateway \
    --clean --if-exists --no-owner /data/backups/gateway-JJJJMMTT.dump
```

Danach das Add-on neu starten.

### Hinweis: SD-Karten-Ausfall

SD-Karten sind das häufigste Ausfallrisiko. Empfehlung: In Home Assistant
**automatische Backups** aktivieren und an einen externen Ort sichern
(Network Storage / Home Assistant Cloud / USB). Stirbt die SD-Karte, genügt:
neues HAOS flashen → Backup wiederherstellen → das Add-on stellt die
Datenbank beim ersten Start selbst wieder her.

Bei einer **externen Datenbank** (`db_url` gesetzt) sichert das Add-on nichts —
dann liegt die Datensicherung beim externen Datenbankserver.

## Fehlerbehebung

### Keine Geräte gefunden
- Prüfe, ob Integrationen in Home Assistant eingerichtet sind
  (Einstellungen → Geräte & Dienste) — das Gateway findet nur Geräte,
  die Home Assistant bereits kennt (z.B. Fronius-, SMA-, Shelly-Integration)
- Das Gateway erkennt nur Geräte mit `device_class` (Sensoren ohne Klasse werden ignoriert)
- Starte einen erneuten Scan in der App

### Add-on startet nicht
- **Zuerst das Add-on-Log lesen** (Einstellungen → Add-ons → Energy Gateway →
  Protokoll) — dort steht die konkrete Fehlerursache
- **Port 8099 belegt**: Läuft ein anderes Add-on oder ein anderer Dienst auf
  Port 8099? Dann diesen Dienst stoppen oder umkonfigurieren und das Add-on
  neu starten
- "Could not connect to the database": Die DB ist eingebaut und startet
  automatisch — prüfe im Add-on-Log den `postgres`-Service (erster Start legt
  die DB unter `/data/postgres` an)
- Nur falls du eine **externe** DB via `db_url` gesetzt hast: prüfe, ob Server,
  Datenbank und Zugangsdaten stimmen und erreichbar sind

### Aktivierungscode erscheint nicht
- Öffne das Energy-Gateway-Panel im HA-Seitenmenü und **lade es neu** —
  das Panel aktualisiert sich auch selbst alle paar Sekunden
- Der Code ist 15 Minuten gültig; nach Ablauf wird **automatisch** ein neuer
  Code generiert — alternativ "Neuen Code generieren" klicken
- Erscheint statt des Codes das normale Dashboard, ist die Box bereits
  aktiviert — dann ist kein Code mehr nötig
- Erscheint gar nichts: Add-on-Log prüfen (siehe "Add-on startet nicht")

### Aktivierungscode abgelaufen
- Öffne das Energy Gateway Panel in Home Assistant
- Klicke auf "Neuen Code generieren"
- Gib den neuen Code in der App ein

### App findet Box nicht
- Stelle sicher, dass dein Handy im **selben WLAN/Netzwerk** ist wie die Box —
  Gast-WLANs und getrennte VLANs verhindern die automatische Erkennung (mDNS)
- Manche Router/Firewalls blockieren mDNS (Multicast) — dann die IP-Adresse
  manuell in der App eingeben (z.B. `http://192.168.1.100:8099`)
- Prüfe, ob das Add-on läuft (Einstellungen → Add-ons → Energy Gateway)

### Datenbank-Probleme
- Bei einer beschädigten oder leeren Datenbank stellt das Add-on beim Start
  automatisch den letzten Dump wieder her — siehe Abschnitt
  **"Backups & Wiederherstellung"** weiter oben
- Für eine gezielte manuelle Wiederherstellung eines täglichen Dumps:
  Anleitung unter "Wiederherstellung (manuell)" im Backup-Abschnitt
- Bei einer **externen** Datenbank (`db_url`) liegt Sicherung und
  Wiederherstellung beim externen Datenbankserver

## Support

Bei Fragen oder Problemen: [GitHub Issues](https://github.com/franzdinhobl/franzl-addons/issues)
