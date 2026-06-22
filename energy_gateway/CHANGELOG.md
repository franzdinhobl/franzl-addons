## 1.2.9

- **Fix (Wallbox-Aktionen — „Hat nicht geklappt"):** Die Sofort-Aktionen für die
  Wallbox (Solar / Sofort laden / Stopp) lösten einen Server-Fehler (HTTP 500)
  aus und wurden in der App fälschlich als „keine Verbindung" angezeigt. Ursache:
  die Datenbank-Session wurde in den Wallbox-Endpoints direkt statt über die
  FastAPI-Dependency geholt (`get_session()` statt `Depends(get_session)`) —
  dadurch landete ein Generator-Objekt statt einer echten Session im Repository.
  Behoben für alle Wallbox-Endpoints (Lademodus, Ziel-SoC, Fahrzeug-Verknüpfung).
- **App:** Fehlgeschlagene Aktionen zeigen jetzt die echte Ursache an (z. B.
  Server-Fehler vs. wirklich keine Verbindung), statt jeden Fehler pauschal als
  „keine Verbindung" zu melden.

## 1.2.8

- **Neu (Steckdose/Schalter als Warmwasser- oder Pool-Heizung):** Wer einen
  Heizstab über eine smarte Steckdose oder einen Shelly schaltet, kann diesen
  jetzt als **Warmwasser- oder Pool-Heizung** nutzen — meist als Zusatzheizung.
  Franzl plant sie standardmäßig **nur mit Sonnenstrom** (kein teurer Netzbezug).
  In den Geräte-Details lässt sich der Verwendungszweck wählen; optional ein
  **Temperaturfühler** (z. B. der Boiler-Fühler der Heizung) zuordnen — dann
  schaltet Franzl bei der Zieltemperatur ab. Ohne Fühler gibt es nur Ein/Aus +
  Nur-Sonne (keine wirkungslosen Regler).
- **Fix (Temperatur im Optimierer):** Die gemessene Temperatur thermischer
  Geräte kam intern nicht beim Optimierer an — dadurch konnte die Abschaltung
  am Zielwert nicht greifen. Jetzt korrekt verdrahtet; die Live-Karte zeigt
  Ist- und Ziel-Temperatur auch direkt über die Verbindung.

## 1.2.7

- **Neu (Geräte im Netzwerk finden):** Geräte, die im Netzwerk hängen aber in
  Home Assistant noch **nicht** eingerichtet sind, erscheinen jetzt im Scan unter
  „Im Netzwerk gefunden" — Home Assistant entdeckt sie selbst (zeroconf/DHCP/
  SSDP/Bluetooth/USB), und Franzl richtet sie per **„Einrichten" direkt aus der
  App** ein (HA-Config-Flow wird durchgereicht). Vorher fand der Scan nur bereits
  in HA eingerichtete Geräte. Geräte mit ungewöhnlichem Einrichtungs-Dialog
  zeigen einen ehrlichen Hinweis statt einer halben Maske.
  - Nur energierelevante Funde werden gezeigt — Drucker, Bluetooth-Beacons,
    Fernseher/Lautsprecher u. ä. werden ausgeblendet.
  - Einrichtungs-Fehler von Home Assistant werden in Klartext angezeigt (z. B.
    „lokale API des Geräts ist deaktiviert" statt eines kryptischen Codes).
  - Der Einrichtungs-Dialog zeigt jetzt Home Assistants **eigene Texte** —
    Schritt-Beschreibung inkl. Anleitung, Feld-Beschriftungen und Fehlertexte
    (in deiner App-Sprache), statt technischer Schlüssel.
- **Fix (Batterie-Leistung):** Speicher, die getrennte **Lade-/Entlade-Sensoren**
  liefern (z. B. Sonnen), zeigten nur den Ladestand (%), nicht die Leistung —
  es wurde nur einer der beiden Sensoren übernommen. Jetzt werden beide
  zusammengeführt (Laden +, Entladen −), die Leistung wird korrekt angezeigt.
  Hinweis: bestehende Speicher einmal neu hinzufügen (Gerät löschen → erneut
  scannen), damit die Zuordnung greift.

## 1.2.6

- **Fix:** Ein iPhone (HA-App mit Standort) wurde als „Fahrzeug" erkannt — jetzt
  werden Handys/Wearables ausgeschlossen (Fahrzeug nur bei echter Auto-Integration).
- **Fix:** Deutsch benannte Fronius-Geräte (Wechselrichter, Smartmeter, Ohmpilot)
  landeten in „nicht erkannt" — Keywords + Profil-Matching (auch ohne Hersteller
  in der HA-Registry, via Integration + Entity-Muster) erkennen sie jetzt.
- **Fix:** Beim erneuten Scan werden bereits hinzugefügte Geräte nicht mehr
  angeboten (kein doppeltes Hinzufügen).

## 1.2.5

- **Neu (Steuerung wirklich angebunden):** Beim Hinzufügen eines Geräts wird
  jetzt das erkannte **Hersteller-Profil mitgenommen** — damit weiß die Box, wie
  das Gerät zu **schalten** ist (HA-Services / Modbus / nativer Treiber / OCPP),
  und der Optimierer kann es tatsächlich steuern statt es nur zu beobachten.
  Vorher wurden so angelegte Geräte als „nur Monitor" registriert.

## 1.2.4

- **Neu (Erkennung von Grund auf):** Geräte werden jetzt **geräte-zentrisch**
  erkannt — die Box liest die Home-Assistant-Geräte-Registry (Hersteller/Modell)
  und ordnet **ein Gerät = eine Kategorie** zu, statt entity-weise zu raten.
  Folgen: ein Tesla erscheint **einmal als Fahrzeug** (nicht als „Klima" UND
  „Batterie"); Fronius-Netzzähler + Ohmpilot werden über die 139 Profile
  zuverlässig erkannt; Handy-/Sensor-Akkus werden **nicht** mehr als
  Hausspeicher klassifiziert. Fällt ohne Registry auf das alte Verfahren zurück.
- **Neu:** Ein in Home Assistant integriertes **Fahrzeug** (z. B. Tesla) speist
  jetzt seinen **Ladestand** direkt in die Wallbox-Ladeplanung ein — ohne
  separates Auto-Login. Der Optimierer plant die Ladung aus echtem SoC +
  Kapazität.
- **Neu:** Geräte lassen sich in der App umbenennen + der erkannte Typ ändern;
  nicht zugeordnete Entitäten manuell zuweisen.

## 1.2.3

- **Fix (kritisch):** Das Add-on durfte Home Assistant gar nicht auslesen — der
  Zugriff auf die HA-Core-API fehlte (`homeassistant_api`), HA antwortete mit
  401. Folge: Geräte-Erkennung fand nichts und alle Live-Energiedaten blieben
  leer. Berechtigung ergänzt — Erkennung + Energiedaten funktionieren jetzt.
- **Neu:** Geräte lassen sich jetzt direkt in der Kunden-App hinzufügen
  („Geräte"-Tab → „Geräte suchen") — Erkennung, Bestätigen und Registrieren
  inkl. Zähler-Rolle, ohne Installer-App.
- **Fix:** Aktivierungs-Panel wechselt nach erfolgreicher Aktivierung automatisch
  zum Dashboard (vorher blieb ein offener Tab am Code hängen).
- **Fix:** Mitglieder-Liste zeigt nie den internen Platzhalter
  (`<id>@pending.franzl`) als Namen.

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
