## 1.2.27

Hybrid-Wechselrichter (PV + Batterie + Netz in EINEM HA-Gerät) werden bei der
Erkennung jetzt in **getrennte Geräte je Rolle** (Solar / Batterie / Netz)
aufgeteilt — wie im HA-Energie-Dashboard. So zeigt das Solar-Gerät die echte
PV-Leistung, die Batterie ihren Ladestand und das Netz Bezug/Einspeisung, statt
alles in einem Gerät zu vermischen. Entity-getrieben (markenunabhängig); reine
Solar-/Batterie-/Netz-Geräte bleiben unverändert.

## 1.2.26

Hotfix: **alle Geräte werden wieder gespeichert.** Mehrere Sensoren, die zusammen
EINEN Messwert ergeben (Batterie Laden+Entladen, Netz Bezug+Einspeisung, mehrere
PV-Strings/Phasen), kollidierten bei der Einrichtung und führten dazu, dass nur
ein Teil der Geräte angezeigt wurde. Behoben:
- Die Datenbank erlaubt diese **Summen-Sensoren** jetzt korrekt nebeneinander.
- Bei **Hybrid-Wechselrichtern** (PV + Batterie + Netz in einem Gerät) kapern die
  Batterie-/Netz-Sensoren nicht mehr den PV-Messwert — das Solar-Gerät zeigt
  wieder die echte PV-Leistung.

## 1.2.25

Geräte-Steuerung neu fundiert: **ein Control erscheint nur, wenn es am echten
Gerät verifiziert ist** — nie weil ein Markenprofil es behauptet (Spec 29).
Damit funktioniert die Steuerung herstellerübergreifend statt nur „auf dem Papier".

- **Steuer-Erkennung aus echten Metadaten statt Namen:** Eine Ladestrom-Grenze
  wird jetzt an ihrem Typ erkannt — egal ob sie „dynamic_charger_limit",
  „intensity" oder „Ladestrom" heißt. Custom-/HACS-Integrationen (Huawei, SMA,
  Solax, Sungrow …) finden ihr Profil endlich.
- **Keine toten Schalter mehr:** Bietet ein Gerät keine echte Steuer-Entität
  (und keinen Direkt-Client), zeigt die App nur noch Status — kein Regler, der
  ins Leere greift. Gilt für JEDES Gerät, auch bei schlampigem Profil.
- **Modi werden auf die echten Geräte-Optionen abgebildet:** „Laden"/„Entladen"
  trifft jetzt die real vorhandene Option (z. B. „Force Charge"), statt einen
  festen Wert blind zu senden. Lüftung, Batterie-Modus und Batterie-Schalter
  (Senec/AlphaESS) werden so herstellerübergreifend steuerbar.
- **Manuelle Zuordnung (Experten-Modus):** Wo die Auto-Erkennung unsicher ist,
  ordnest du eine Geräte-Rolle selbst der richtigen HA-Entität zu — mit Live-Wert
  zur Kontrolle.
- **Funktioniert-es-wirklich-Anzeige:** Jeder Steuerbefehl wird mitprotokolliert;
  die App zeigt pro Gerät, ob die Steuerung im Alltag funktioniert, unzuverlässig
  ist oder fehlschlägt — so lassen sich Probleme früh erkennen.
- **Robustere Aktivierung:** ein seltener Fehler beim Aufräumen alter Haushalte
  (fehlender Anzeigename) ist behoben.

## 1.2.24

Systemischer Audit (Onboarding · Steuerung · Tagesplan · Runtime) — `docs/qa/systemic-audit-2026-06-25.md`.

- **Messwerte stimmen jetzt unabhängig von der Einheit:** Die Geräte-Erkennung
  rechnet Energie- und Leistungssensoren einheits-richtig um — ein Zähler in
  **Wh/MWh** landet nicht mehr 1000× zu groß im kWh-Feld, ergänzend zu kW/mW.
- **Keine fragilen Helfer-Sensoren mehr unbemerkt gewählt:** Abgeleitete
  HA-Helfer (Template-/`simple_*`-Sensoren) werden in der Geräte-Erkennung als
  „abgeleitet" markiert, damit die echte Geräte-Entity bevorzugt wird; gewählte
  Entities, die keinem Messwert zugeordnet werden konnten, werden ehrlich gemeldet.
- **Fahrzeug-Ladestand wählt eine LIVE-Entity:** Schläft das Auto (Tesla/Tessie),
  wird nicht mehr eine `unknown`-Entity angezeigt, wenn ein verfügbarer Wert da ist.
- **Ladesteuerung lügt nicht mehr „Erfolg":** Ein Lade-Befehl (Start/Stopp,
  Limit, Strom) gegen ein schlafendes Auto meldet jetzt ehrlich „Auto nicht
  erreichbar — aufwecken" statt eines wirkungslosen Scheinerfolgs.
- **Kein doppeltes Fahrzeug mehr:** Ein HA-Auto erneut hinzufügen aktualisiert das
  bestehende statt ein Duplikat anzulegen.
- **Tagesplan nie mehr kopf-/inhaltslos:** Läuft gerade nichts (z. B. alles im
  Wohlfühlband), zeigt der Plan eine ehrliche Status-Zeile und eine Headline aus
  dem nächsten geplanten Ereignis statt einer leeren Ansicht.
- **„Degraded"-Fehlalarm behoben:** Der Loop-Watchdog nutzt jetzt pro Loop ein
  passendes Zeitfenster und jeder Loop sendet ein Lebenszeichen — die Box meldet
  nicht mehr fälschlich „degraded"/„stalled" im Normalbetrieb.
- Dazu: native Fronius-Ohmpilot-Anbindung (Modbus) + verifizierte Batterie-
  Vorzeichen-Kalibrierung (Sonnen/Tesla Powerwall).
- **Native Geräte überleben IP-Wechsel (DHCP):** Ändert der Router die IP eines
  nativ angebundenen Geräts (z. B. Fronius Ohmpilot), findet die Box es selbst
  wieder — Suche im LAN nach dem stabilen Fingerprint (Seriennummer), neue IP
  wird übernommen. Manueller Fallback: IP im Geräte-Detail setzen.

## 1.2.23

- **Ehrlich bei fehlenden Live-Daten (unknown ≠ 0):** Ist die HA-Entity eines
  Geräts gerade `unavailable` (z. B. ein Fronius-Ohmpilot, dessen Sensoren nicht
  liefern), meldet Franzl jetzt Status **„offline"** statt „idle" bei „0 W". Die
  App zeigt dann „—" und einen Offline-Punkt statt eines erfundenen 0-W-Werts,
  und im Temperaturfühler den aktuellen Wert bzw. „—" statt nur „verbunden".
- **Slider-Wertanzeige lesbar:** Die Wert-Tooltips an Schiebereglern waren
  schwarz-auf-schwarz (unleserlich) — heller Textstil ergänzt (alle Slider).

## 1.2.22

- **Mehr Marken steuerbar (Audit B):** Die Geräte-Erkennung mappt jetzt auch
  `number`/`select`-Steuer-Entities auf die Felder, die der Optimierer wirklich
  bedient — Wallbox-Ladestrom, Batterie-Modus, Lüftungs-Modus/-Stufe,
  Warmwasser-Leistung — und ein HA-`climate`-Gerät steuert auch Wärmepumpe + Pool.
  Damit sind viele Marken ohne Hersteller-Profil out-of-box steuerbar statt nur
  ablesbar (gerätezentriert + Namens-Heuristik, konservativ).
- **Keine toten Bedienelemente mehr (Audit C):** Ein Gerät gilt nur noch als
  steuerbar, wenn wirklich ein bedienbares Steuer-Feld erkannt wurde — sonst
  ehrlich nur Anzeige (kein Schieberegler, der ins Leere läuft).
- **Netz-Leistung korrekt (Audit D):** Zähler mit getrennten Import-/Export-
  Leistungssensoren werden jetzt zu einem signierten Netto-Wert verrechnet
  (Export negativ) statt summiert — sonst war Autarkie/Verbrauch verfälscht.

## 1.2.21

- **Fix (Laden starten/Ladelimit ging nicht — 500):** Die HA-Ladesteuerung warf
  `NameError: get_ha_client` (Import lag nur lokal) → 500 bei
  `/vehicles/{id}/charge` und `/charge-limit`. Import auf Modulebene gezogen.
- **Fix (Crash-Klasse):** `VehicleRepo.get_by_vin` nutzte `scalar_one_or_none`
  und wäre bei zwei Fahrzeugen mit derselben VIN abgestürzt — gibt jetzt das
  jüngste zurück (wie `get_by_device`).

## 1.2.20

- **Auto laden steuern (Fahrzeug über Home Assistant):** Ist ein Auto in HA mit
  Steuer-Entities integriert (Tesla via Tessie: Laden an/aus, Ladelimit,
  Ladestrom), erkennt Franzl diese beim Hinzufügen automatisch und macht
  **Laden starten/stoppen, Ladelimit und Ladestrom** steuerbar — ganz ohne
  Hersteller-Cloud-Login. Die Wallbox-Capability `can_control_charging` schaltet
  die Bedienelemente frei; die Charge-Endpoints (`/vehicles/{id}/charge`,
  `/charge-limit`, `/charge-current`) bedienen den HA-Pfad.

## 1.2.19

- **Batterie-Leistung wird gefunden (Fronius SOLAR.net & Co.):** Discovery hängt
  jetzt eine Netto-Speicherleistung (`*leistung_speicher*`/`*battery_power*`,
  `device_class: power`) an das Batterie-Gerät, auch wenn sie unter dem
  Wechselrichter-HA-Gerät liegt — Laden/Entladen erscheint endlich bei der
  Batterie statt als PV verbucht zu werden (Vorzeichen wie im Hybrid-Profil
  invertiert). Herstellerunabhängig.
- **Doppeltes Fahrzeug legt nicht mehr die ganze Geräteliste lahm:**
  `get_by_device` gibt bei mehreren Fahrzeugen an einer Wallbox das jüngste
  zurück statt zu crashen; der Geräte-Snapshot isoliert pro Gerät — ein kaputtes
  Gerät leert nie wieder die ganze Liste.

## 1.2.18

- **Eine Box = ein Zuhause (kein Müll mehr):** Bei jeder Aktivierung räumt die
  Box verwaiste Alt-Haushalte automatisch weg. Vorher legte „Zurücksetzen / Neu
  aktivieren" (Soft-Reset, der die DB behält) bei der Re-Aktivierung einen
  ZWEITEN Haushalt an — die Box meldete dann den falschen als primär, die App
  verband sich mit einem toten Tunnel, und Firestore/Cloudflare sammelten Leichen
  an. Jetzt bleibt nach jeder Aktivierung garantiert genau der eine aktivierte
  Haushalt übrig (inkl. aller abhängigen Daten der alten — metadaten-getrieben,
  deckt auch Tabellen ohne FK ab).

## 1.2.17

- **Land automatisch & korrekt:** Das Land (für den CO₂-Faktor) wird jetzt aus
  Home Assistants eigener Konfiguration übernommen (`GET /api/config`, dasselbe
  wie der Standort) statt aus der Zeitzone geraten — Letzteres lag falsch, sobald
  die Box-Zeitzone nicht zum Standortland passt (z. B. AT-Haushalt mit
  Zeitzone `Europe/Berlin`). Überschreibt nie eine manuelle Wahl; Zeitzone bleibt
  nur letzter Fallback.

## 1.2.16

- **Aufräumen:** Veraltete FastAPI-`regex`-Query-Parameter auf `pattern`
  umgestellt (Energie-Verlauf-Endpunkte) — entfernt zwei Deprecation-Warnungen
  beim Start, keine Verhaltensänderung. Bündelt den aufgelaufenen Backend-Stand
  (Capability-Grounding, entrauschtes Logging, Tagesplan-Rückblick,
  Wallbox-Teaser) in ein auslieferbares Image.

## 1.2.15

- **UX (ganzheitlicher Umbau):** Klare Trennung „Schauen / Jetzt tun /
  Einstellen". Wallbox-Karte zeigt den Lademodus als Status + Tagesziel/
  Mindestladung-Teaser (Umschalten im Aktion-Tab); Autopilot zog vom Home- in
  den Mehr-Tab („Wie Franzl optimiert"); „Haus & Strom"-Sektion bündelt Tarif +
  PV + Land; Onboarding fragt Land + Sprache; Karten-Fachbegriffe erklärt
  (Legionellenschutz, SG-Ready, Batterie-Modus) und entschärft (Tier → „Mit
  Auto-Daten"/„Standard", „Session" → „Geladen"). Read-only-Geräte zeigen keinen
  internen Roh-Datendump mehr.
- **Intern:** WS-Geräte-Snapshot trägt Wallbox-Tagesziel/Mindestladung;
  `GATEWAY_VERSION` wieder mit der Add-on-Version synchronisiert.

## 1.2.14

- **Ehrlicher (Geräte-Steuerung):** Franzl zeigt jetzt nur noch Bedienelemente
  für Geräte, die wirklich gesteuert werden können. Read-only-Geräte (z. B. ein
  Fronius Ohmpilot, den der Fronius-Wechselrichter selbst regelt, oder ein
  Speicher, den Home Assistant nur ausliest) erscheinen als reine Statusanzeige —
  keine Schieberegler oder Schalter mehr, die ins Leere laufen. Der Tagesplan
  plant ebenfalls nur noch steuerbare Geräte. Schlägt eine Steuerung trotzdem
  fehl, wird das jetzt protokolliert (sichtbar im Log-Viewer), damit die
  Erkennung gezielt verbessert werden kann. Experten/Installateure können die
  Steuerbarkeit eines Geräts im Detail-Bildschirm manuell korrigieren.

## 1.2.13

- **Werkzeug (Werksreset im Image):** `reset-box.sh` ist jetzt fest im Add-on
  enthalten. Ein vollständiger „von 0"-Reset (DB + Backup-Dumps + Cloud-Link +
  Aktivierungscode) ist damit **ein einziger Befehl** im Container
  (`reset-box.sh --yes`) statt das Skript hineinkopieren zu müssen.

## 1.2.12

- **Robuster (Aktivierung atomar):** Bei der Aktivierung schreibt das Gateway die
  Firebase-Zugangsdaten jetzt als **letzten** Schritt vor dem Neustart in den
  Running-Modus — Tunnel-Token/-URL und Haushalts-Verknüpfung werden davor
  gespeichert. Die Zugangsdaten-Datei ist der einzige Auslöser für den
  Running-Modus; schlägt ein vorheriger Schritt fehl, bleibt die Box sauber im
  Setup-Modus und versucht es erneut, statt halb auf „läuft" umzuspringen.
  Schließt eine seltene Falle, bei der das Add-on im Aktivierungs-Bildschirm
  hängen blieb.

## 1.2.11

- **Neu (PV-Anlage konfigurierbar):** Die Solarprognose rechnete bisher für jeden
  mit festen Annahmen (10 kWp, 30° Neigung, Süd-Ausrichtung) — falsch für jede
  Ost/West- oder anders dimensionierte Anlage. Jetzt erfasst Franzl pro Zuhause
  eine oder mehrere PV-Teilanlagen mit echter Leistung (kWp), Ausrichtung und
  Neigung; Ost/West-Dächer werden als getrennte Teilanlagen summiert. Einstellbar
  im Onboarding (mit „Ich habe eine PV-Anlage"-Auswahl) und unter Mehr →
  Solaranlage, mit Erklär-Hilfen statt Fachbegriffen.
- **Ehrlicher statt geraten:** Ohne hinterlegte Anlage (oder ohne Standort) zeigt
  Franzl keine erfundene Prognose mehr, sondern bleibt ehrlich still — statt wie
  bisher heimlich auf einen Wiener Standard-Standort zurückzufallen.
- **Standort:** kommt weiterhin automatisch aus Home Assistant; eine Änderung an
  der Anlage berechnet die Prognose sofort neu.
- **Fix (Aktivierungs-Panel — „Neuen Code generieren"):** Der Knopf war ein
  klassisches Formular und navigierte das Panel auf die rohe JSON-Antwort
  (`{"data":{"code":…}}`) statt den Code zu erneuern. Jetzt wird der Code per
  Hintergrund-Anfrage neu erzeugt und das Panel frisch geladen (inkl. passendem
  QR-Code für den neuen Code).

## 1.2.10

- **Fix (Solarprognose wurde nie aktiviert):** Nach dem Onboarding blieb die
  Solarprognose dauerhaft stumm, weil die Haushalts-Koordinaten nie gesetzt
  wurden — die Prognose-Schleife übersprang jeden Haushalt ohne Standort
  stillschweigend. Franzl holt den Standort jetzt automatisch aus Home Assistant
  (`GET /api/config`) und trägt ihn nach; der Nutzer muss nichts eingeben. Das
  wirkt auch rückwirkend für bestehende Haushalte und liefert die erste Prognose
  direkt nach dem Start statt erst nach 30 Minuten. Steht HA auf 0/0, bleibt der
  Standort bewusst leer.
- **Intern:** `GATEWAY_VERSION` (Anzeige im Ingress-Panel + `/health`) war auf
  1.2.6 stehengeblieben und wieder mit der Add-on-Version synchronisiert.

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
