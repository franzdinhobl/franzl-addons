## 1.1.7

- **Neu:** Code-only Aktivierung — du gibst nur den 9-stelligen Code ein, der
  Rest (Haushalt-Anlage + Fernzugriff via Cloudflare-Tunnel) passiert
  automatisch. Box spiegelt den Haushalt lokal und startet den Tunnel selbst.

## 1.1.6

- **Fix:** Ingress-Panel war ungestylt — CSS/Logo/Formular nutzten absolute
  Pfade, die unter dem HA-Ingress-Pfad ins Leere liefen. Assets + Formular-Action
  werden jetzt mit `X-Ingress-Path` korrekt verlinkt.

## 1.1.5

- **Fix:** Setup-Mode startete nicht — das Ingress-Panel (Aktivierungscode) und
  der `/setup/status`-Endpoint waren mit der aktuellen FastAPI/Starlette-Version
  inkompatibel (TemplateResponse-Signatur + Response-Model). Lokal im echten
  Container end-to-end verifiziert (Boot → Migrationen → Setup-Panel rendert).

## 1.1.4

- **Fix:** Datenbank-Migrationen liefen beim Erststart nicht durch — mehrere
  Tabellen (u. a. Fahrzeuge, Abos, Geräte-Befehle) und Spalten (Entity-Health,
  OTA, Installer-Rolle) fehlten in der Migrations-Kette, ein veralteter Import
  brach den Start ab. Die Migrationen wurden auf ein sauberes, vollständiges
  Initial-Schema konsolidiert und end-to-end gegen PostgreSQL 16 verifiziert.

## 1.1.3

- **Fix:** PostgreSQL startete beim ersten Boot nicht (`/run/postgresql`
  Socket-Verzeichnis fehlte auf dem tmpfs). Wird jetzt beim Start angelegt.

## 1.1.2

- **Fix:** Interne Init-Schritte (Konfiguration + Datenbank-Migrationen) wurden
  unter s6-overlay v3 nicht korrekt als Shell-Skript ausgeführt — der Start
  brach ab. Init-Logik in eigene Skripte ausgelagert.

## 1.1.1

- **Fix:** Add-on startete nicht (`s6-overlay-suexec: can only run as pid 1`).
  `init: false` gesetzt, wie es die Home-Assistant-Basis-Images verlangen.

## 1.1.0

- **Neu:** Mathematischer Optimierer (MILP/HiGHS) für PV-Eigenverbrauch und
  Preis-Arbitrage — ersetzt die regelbasierte Kaskade (Fallback bleibt erhalten).
- **Neu:** Erweitertes Tarif-Modell — zeitvariables Netzentgelt, Gaspreis,
  Behandlung negativer Strompreise, Länder-Defaults (AT/DE/CH) mit Plausibilitäts-Guards.
- **Neu:** Geräte-Flexibilität — Batterie-Modus (Eigenverbrauch/Arbitrage) +
  Notstrom-Reserve, Wallbox Floor + Wunschladung, „günstig-genug"-Schwelle,
  sortierbare Überschuss-Reihenfolge, Heizstab als reiner Überschuss-Verbraucher,
  verschiebbare Lasten (N h/Tag in beste Fenster).
- **Verbessert:** Solar-Prognose-Korrektur läuft jetzt auf allen Architekturen.

## 0.1.0

- Erste Version
- Automatische Geräteerkennung via Home Assistant
- Echtzeit-Optimierung (PV, Batterie, Wallbox, Wärmepumpe)
- Aktivierungscode-Flow für einfaches Setup
- WebSocket Echtzeit-Updates
- Ingress-Panel in Home Assistant
