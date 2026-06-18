## 1.2.2

- **Fix:** Tunnel-Status im Panel zeigte „nicht konfiguriert", obwohl der
  Fernzugriff lief — er hängt jetzt am tatsächlich laufenden Tunnel (Token), nicht
  an einem DB-Feld, und zeigt die Tunnel-Adresse an.
- **Fix:** Geräte-Erkennung beim Einrichten lief ins Leere („Keine
  Gateway-Adresse"), wenn die Box über den Tunnel verbunden war — die Erkennung
  nutzt jetzt die aufgelöste Verbindung; man bleibt nie mehr im Screen hängen.
- **Fix:** Mitglieder-Liste — du kannst dich nicht mehr versehentlich selbst
  entfernen (Knopf verschwindet auf der eigenen Zeile), und jede Person wird mit
  E-Mail statt generischem „Besitzer:in" angezeigt, damit Familienmitglieder
  unterscheidbar sind.
- **Fix:** Aktivierungs-Panel lud sich alle 5 Sekunden komplett neu (Log-Spam) —
  jetzt nur noch, wenn der Code wirklich abläuft.
- **Fix:** Reset löst zusätzlich die gespeicherte Tunnel-Adresse, damit nach dem
  Neu-Aktivieren keine veraltete Adresse hängenbleibt.
- Version wird im Add-on-Panel jetzt korrekt angezeigt (vorher immer „v1.0.0").
- Reset-Knopf protokolliert deutlich sichtbar im Log (`=== BOX RESET requested ===`),
  damit nachvollziehbar ist, ob er ausgelöst wurde.

## 1.2.1

- **Neu:** „Zurücksetzen / Neu aktivieren"-Knopf im Add-on-Panel — setzt die Box
  sauber in den Aktivierungs-Modus zurück (neuer Code), **nur auf Klick, nie
  automatisch**. Löst nur die Cloud-Anbindung; **lokale Energiedaten bleiben**.
- **Fix:** Gelöschte Haushalte werden nicht mehr versehentlich neu angelegt
  (Box meldet „online" nur noch für existierende Haushalte) — keine Zombie-
  Haushalte mehr.
- **Fix:** Mitglieder-Liste zeigt die echte E-Mail statt des internen
  Platzhalters (`…@pending.franzl`).

## 1.2.0

- **Fix (kritisch):** Aktivierung schlug bisher immer fehl — die Box bekam ihre
  Zugangsdaten nie und blieb im Setup-Modus. Behoben: Auslieferung der
  Credentials (service_account-Config-Form), robuster Aktivierungs-Poller (eine
  Code-Quelle, registriert immer den angezeigten Code, sichtbare Logs) und
  korrekter Wechsel in den Running-Mode nach der Aktivierung (Erkennung der
  Zugangsdaten direkt auf der Platte, unabhängig vom init-config-Lauf).
- **Neu:** Box-bestätigte Aktivierung — die Box meldet „online" zurück, die App
  zeigt echten Fortschritt statt blindem Warten; fehlgeschlagene Versuche werden
  automatisch aufgeräumt (keine Phantom-Haushalte mehr).
- **Neu:** Mehrere Boxen pro Konto (Haushalts-Wechsler), „Box ersetzen" für
  Hardware-Tausch, Multi-Home-Abo.

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
