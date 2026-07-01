## 1.2.80

**„Dein Tag"-Jetzt-Zeile folgt in ruhigen Momenten dem echten Stromfluss.**
Wenn gerade nichts aktiv gesteuert wird (kein PV-Überschuss, kein Ziel zu erzwingen), erzählt die
Jetzt-Zeile jetzt ehrlich, **woher dein Haus gerade den Strom bezieht** — aus der gemessenen
Wirklichkeit, nicht geraten:
- Akku trägt das Haus → „Keine Sonne gerade — dein Haus läuft aus dem Akku. Alles versorgt."
- wenig Sonne + Akku → „…aus Akku und Restsonne."
- Netzbezug → „…dein Haus läuft aus dem Netz."
- Sonne deckt/überschüssig → „Die Sonne deckt gerade dein Haus." / „…es bleibt sogar Überschuss."

Das ersetzt die faktische, aber wenig hilfreiche Zeile „Dein Auto ist verbunden, nimmt gerade aber
keinen Strom.", wenn das Auto ohnehin nichts tut. Sobald wirklich etwas läuft (Laden/Heizen), zeigt
die Zeile wieder das Gerät. Große Verbraucher sieht man automatisch, weil sich damit der Fluss (Netz/
Verbrauch) ändert. Ohne Netzzähler bleibt es bei der Geräte-/Ruhe-Zeile (keine Fluss-Behauptung).

## 1.2.79

**Meldungs-Durchgang: restliche unehrliche/leere Formulierungen bereinigt.**
- **Volles Auto sagt nicht mehr „wartet".** Ein angestecktes Auto, das sein Ladelimit erreicht hat,
  zeigt jetzt „geladen" statt „wartet" (es wartet auf nichts — es ist fertig).
- **Kein „Ich pausiere dein Auto — Auto bei 85% …" mehr:** ein Grund, der schon ein vollständiger
  Satz ist (Live-Controller), steht für sich; nur Planner-Fragmente bekommen den „Ich pausiere"-Rahmen.
- **Ruhe-Schlagzeile ehrlich:** „Gerade läuft alles ruhig — alles ist versorgt." statt „… und warte
  auf den besten Moment." (Letzteres versprach eine Aktion, die evtl. nie kommt.)
- **Keine „?"-Platzhalter mehr** für Laien: fehlt eine Deadline/Preis, wird die Klausel weggelassen
  („Geplant — bereit" statt „… bereit bis ?").

## 1.2.78

**„Dein Tag" & Franzls Tipp: keine erfundenen Gründe, keine hohlen Meldungen mehr.**
Der Optimierer sagt jetzt überall den ECHTEN Grund, statt einen zu raten — abgeleitet aus den Daten,
die die Entscheidung ohnehin getroffen hat.
- **Kein falsches „warm genug" mehr.** Wenn das Warmwasser gerade nicht heizt, weil im Solar-Modus
  kein PV-Überschuss da ist, sagt „Dein Tag" das auch so („Warte auf Sonnenüberschuss") — statt
  fälschlich „warm genug" zu behaupten. „Auf Zieltemperatur" nur, wenn die Temperatur das Ziel
  wirklich erreicht hat. Betrifft Controller-Default, Planner-Aus-Zeiten und die Jetzt-Zeile.
- **Kaputte/irreführende Formulierungen raus:** „— Aus — kein PV-Überschuss" und „Pausiert —
  Pausiert —" (doppelte Wörter), Pool „zu teuer *oder* warm genug" (zwei Gründe), Wärmepumpe „Wartet
  auf ? — dann günstiger" (leerer Platzhalter).
- **Wallbox ehrlich:** manuell gestoppt heißt „manuell gestoppt" (nicht „Strom zu teuer"); im
  Solar-Modus ohne Überschuss heißt es „Kein Sonnenüberschuss" (die Wallbox lädt dann nie aus dem
  Netz, „zu teuer" war Unsinn).
- **Franzls Tipp gibt wieder echte Tipps.** Der tote Winkel „Nachmittag, Sonne lässt nach" fiel auf
  das nichtssagende „Alles im grünen Bereich — nichts zu timen"; jetzt schaut der Tipp voraus
  (morgen/günstige Stunden). Wo es wirklich keinen Zeitvorteil gibt, sagt er das ehrlich
  („Verschieben bringt gerade nichts — nutz Strom, wann's dir passt") statt einer Leerfloskel.

## 1.2.77

**Lese-/Steuer-Transport aufgeräumt + Ohmpilot lässt jetzt die Max-Temperatur setzen.**
Ein markenübergreifendes Prinzip: pro Gerät sind **Lesen** und **Steuern** getrennt — gelesen wird
zuerst aus der offiziellen Hersteller-Integration in Home Assistant (die das Gerät ohnehin abfragt),
nativ nur, wo HA es nicht kann.
- **Kein Poll-Stress mehr auf fragilen Geräte-Servern.** `read_device_state` liest jetzt HA-first
  pro Primärfeld (Thermik→Temperatur, Batterie→SoC, sonst Leistung): hat die HA-Integration den Wert,
  wird der native Client NICHT mehr nur zum Anzeigen gepollt. Konkret der Fronius Ohmpilot — sein
  eigenes kleines Webserverchen wurde alle 15 s abgefragt und ging dabei immer wieder in die Knie;
  jetzt kommen Temperatur/Leistung aus den Fronius-Sensoren, der Ohmpilot wird nur noch zum **Steuern**
  angesprochen.
- **Selbstgebaute HA-Helfer werden nie mehr fälschlich als Gerät gebunden.** Ein hand-gebauter
  `switch.<x>_boost` (Template), Skripte, `input_boolean`/Automationen tauchen bei der Einrichtung
  nicht mehr als Steuer- oder Sensor-Entity eines Geräts auf (ein normaler Nutzer hat sie nicht).
- **Ohmpilot: Maximal-Temperatur ist jetzt einstellbar** (nicht mehr nur Boost an/aus). Der im UI
  gesetzte Band-Max wird beim Speichern **exakt** (auf 0,1 °C, nicht gerundet) an den Ohmpilot
  geschrieben — über dessen lokale `set.cgi`, wobei ALLE anderen Einstellungen (Legionellenschutz,
  Mindesttemperaturen) unangetastet zurückgeschrieben werden. Live am echten Gerät verifiziert
  (idempotenter Echo-Write + reale Änderung, nur die Max-Temperatur ändert sich). So heizt der
  Ohmpilot auf genau den Wert, den du einstellst — statt auf sein eigenes, abweichendes Cap.

## 1.2.76

**Warmwasser/Thermik: der eingestellte Wert gilt jetzt überall (Ziel-Modell aufgeräumt).**
Ein Thermik-Gerät hat genau zwei Werte — die **Min/Max des Bands, das du im UI einstellst**; dazwischen
wird optimiert. Der Optimierer las bisher an einigen Stellen noch einen **alten, separaten
„Boost"-Wert** aus einem Uralt-Feld (Anlage-Default 60 °C beim Warmwasser), das die aktuelle UI gar
nicht mehr schreibt → er heizte am eingestellten Max (z.B. 55 °C) vorbei Richtung 60. Der Boost-Wert
ist für Thermik jetzt **ersatzlos gestrichen**: die Überschuss-Heizung stoppt am **Band-Max**, die
Karte zeigt das Band-Max, und beim Anlegen werden keine Fake-Defaults mehr gesetzt. Ein Regel-Test
nagelt fest, dass kein alter Wert je wieder das Ziel überschreibt. (Wallbox behält ihren echten
Boost-Wert = Ladelimit.)

## 1.2.73

**Steuerungs- & „Dein Tag"-Ehrlichkeit + IDM-Warmwasser.** Großer Durchlauf, damit die App nur
sagt, was real passiert:
- **Warmwasser-/Pool-Temperatur kommt jetzt am Gerät an.** Der Executor schrieb für Warmwasser/Pool
  bisher keinen Sollwert (nur Watt/Relais); jetzt geht der konfigurierte Komfort-Sollwert real ans
  HA-`water_heater`/`number`-Entity (DHW-Floor 40° erzwungen). Außerdem ein Verify-Bug behoben:
  Sollwerte galten bisher als „nicht bestätigt" (Verify prüfte den Modus statt der Temperatur).
- **IDM Navigator: Warmwasser-Solltemperatur nativ** über Modbus-Register 1032 (offizielle
  IDM-Doku), für IDM-Wärmepumpen ohne Home Assistant.
- **„Dein Tag" erzählt verifizierten Vollzug statt Absicht:** ein „erledigt" entsteht erst, wenn der
  Befehl wirklich durchging; die Jetzt-Zeile nennt nur Aktivität bei gemessener Leistung
  („… heizt gerade nicht — warm genug" / „nicht beobachtbar"); der Teaser zeigt keine geplante
  Zukunfts-Aktion als „jetzt" mehr.
- **Solarprognose-Stundenfehler behoben:** die lokal gespeicherte Forecast-Zeit wurde an zwei
  Stellen doppelt in Lokalzeit umgerechnet → Prognosekurve/Planung um den Zeitzonen-Offset
  verschoben. Auch die ML-Korrektur paart Prognose↔Ist jetzt auf derselben Stunde.
- **Rückblick entspammt:** ein kurzer Wolken-Wechsel solar↔Netz schreibt keinen Phantom-Eintrag mehr
  (Hysterese).
- Plus Optimierer-Härtung + die Backend-Anteile des laufenden UI-Sweeps.

## 1.2.72

**Installer: native Geräte-IP im Netzwerk suchen (ISU-04).** Das Setup-Schema markiert ein
scanbares Host-Feld jetzt markenneutral mit `scan_probe` (= Profil-ID, abgeleitet aus
`host_resolver._PROBES`). Der Installer-„Natives Gerät hinzufügen"-Dialog zeigt daneben einen
„Im Netzwerk suchen"-Button, der `POST /devices/{hid}/scan-native` aufruft und die IP automatisch
einträgt — statt sie vom Gerät abzulesen. Aktuell scanbar: Fronius Ohmpilot (HTTP). Spiegelt den
bestehenden Kunden-App-Upgrade-Flow.

## 1.2.71

**Installer-/Onboarding-/Mehr-Audit (19 Findings).** Zweiter App-weiter Audit auf den bisher
ungeprüften Flächen (Mehr/Settings, Onboarding, komplette Installer-App). Backend-Teil:
- **Fleet-Diagnose ehrlich:** `GET /installer/fleet` liefert jetzt `offline_device_count`,
  `has_unset_meter_roles`, `has_readonly_inverters` — die Installer-Fleet-Karte konnte diese
  Triage-Signale vorher nie anzeigen (Offline-Badge/Zähler-/Wechselrichter-Warnungen tot).

Der Rest sind App-Fixes (greifen beim nächsten App-Build): stille Speicher-Fehler → ehrliche
Fehlermeldung (Überschuss-Reihenfolge, Away-Modus, Routinen, Post-Join, Rescan); kein fabriziertes
„Free" für zahlende Pro-Nutzer bei Box-offline; Installer-Setup-Crash (Presets aus echten Geräte-
IDs statt Discovery-Liste); Diagnose-Health auf das richtige `/health`; ehrliche Verifizierungs-/
Ziel-Anzeigen; Tarif-Preis-Validierung. Report: `docs/qa/remaining-surfaces-audit-2026-06-30.md`.

## 1.2.70

**`power_w` ehrlich nullable (unknown ≠ 0).** Ist die Live-Leistung eines Geräts nicht
lesbar (Entity unavailable), liefert der Snapshot jetzt `null` statt einer erfundenen `0 W`;
die App zeigt „—". Schließt den letzten Audit-Edge-Case (#30) — auch online-Geräte mit
totem Power-Sensor. Dart `DeviceSnapshot.powerW` nullable (Arithmetik via `?? 0`, Anzeige „—").

## 1.2.69

**Audit-Restposten geschlossen.** Die vier im 1.2.68-Report zurückgestellten Findings:

- **Netz-geladener Akku korrekt zugeordnet** (Migration `0011`): die Aggregation rechnet jetzt
  die aus dem Netz geladene Akku-Energie heraus (`battery_charge_grid_kwh` in
  hourly/daily + Totals `battery_charge_solar_kwh`/`_grid_kwh`). Die „Woher/Wohin"-Karte zeigt
  nur noch SOLAR-geladene Energie unter „Erzeugung → in den Speicher", nicht mehr Netzstrom.
- **Modus-Chip live**: `charge_mode`/`battery_mode` reisen jetzt im per-Tick-`device_update` mit,
  sodass externe Moduswechsel propagieren.
- **Standalone-Fahrzeug-SoC** aktualisiert sich am Aktion-Tab alle 60 s (statt nur beim Öffnen).
- **Kein erfundenes „0 W"** mehr für offline Geräte (Home-Status-Karte zeigt „—").

## 1.2.68

**App-weiter Live-Zustand-Audit — systematischer Fix (29 Findings).** Ein Multi-Agent-
Audit fand eine durchgängige Fehlerklasse: die Geräte-Karten zeigten LIVE-Begründungen
neben EINGEFRORENEN Zahlen, und Schlagzeile/Tagesplan kamen aus dem 15-Min-Forecast statt
der Live-Entscheidung. Behoben (Backend):

- **Per-Tick-Broadcast generalisiert:** der `device_update` trägt jetzt für JEDE Kategorie
  (vorher nur Wallbox) die Live-Messwerte — Thermik (`current_temp_c`/`boost_active`),
  Batterie (`battery_soc_pct`), read-only Solar/Grid/Batterie (Leistung+Status), Auto-SoC
  auch im Leerlauf. Throttling pro Gerät verhindert einen Delta-Sturm. → „Ladestrom 0 A",
  „Stopp-Toggle eingefroren", „Batterie zeigt nie SoC" behoben.
- **Tagesplan/Schlagzeile sagt die Live-Wahrheit:** die aktuelle Stunde überlagert jetzt die
  Live-Controller-Entscheidung (reaktiver PV-Überschuss) statt des Forecast-Slots → keine
  „Ich pausiere dein Auto — zu teuer" mehr, während es aus Sonne lädt.
- **Ehrlichkeit bei offline:** ein offline/nicht-erreichbares Gerät wird NICHT mehr als
  heizend/ladend erzählt (Tagesplan + Schlagzeile); Snapshot-Key-Fixes (native Thermik-Temp
  `temp_c`/`temperature`, Batterie-SoC), sensorlose Off-Begründung ohne „(?°C)".

(App-Welle separat: Haus/Auto-Doppelzählung, Offline-Schlagzeile, Prognose-kWh-Integral.)

## 1.2.67

**Live-Messwerte erreichen die Geräte-Karten (systematisch).** Der per-Tick
`device_update` über WebSocket trug bisher NUR Aktion/Status/Begründung — die
gemessenen Werte (Ladestrom, Leistung, Auto-SoC, Auto-Ladelimit, Stecker-Status)
kamen ausschließlich im vollen Snapshot (nur beim Verbinden/Reconnect). Folge: die
Karte zeigte eine LIVE-Begründung („Ich lade dein Auto mit 7,3 kW") neben
EINGEFRORENEN Zahlen („Ladestrom 0 A", „wartet"), und der Ladelimit-Slider folgte
einer Änderung in der Hersteller-App nicht. Jetzt trägt der `device_update` die
Live-Messwerte mit (Client merged sie partiell über den letzten Stand), sodass
Ladestrom, Leistung, SoC, Ladelimit und Stecker-Status bei jedem Tick aktuell sind.

## 1.2.66

**Ehrlicher Tagesplan + konsistenter Auto-Ladestand (Folge-Fix zu 1.2.65).**

- **Tagesplan-Schlagzeile bei vollem Auto:** Auch der MILP-Planer (Live-Engine)
  benannte ein Auto über seinem Ladelimit nur mit „off" → die Home-Schlagzeile las
  sich als „Ich pausiere dein Auto — gerade nicht nötig". Jetzt sagt der Plan
  ehrlich „… — schon auf 92 %, Ladelimit (80 %) erreicht" (MILP + regelbasiert).
- **Ein Ladestand überall:** Der angezeigte Auto-SoC kam aus der DB, die nur für
  API-Autos (Tesla Fleet/VW) nachgeführt wird — bei einem HA-Entity-Auto lag der
  Wert fest (z. B. 90 %), während der Live-Regler den HA-Sensor las (92 %). Folge:
  zwei verschiedene Prozentwerte auf einem Screen (Karte/Geräte-Tab vs. Begründung).
  Der Snapshot liest den SoC jetzt live aus der HA-Entität (wie schon das Ladelimit),
  sodass Karte, Geräte-Tab und Begründung denselben Wert zeigen.

## 1.2.65

**Solar-Wallbox lud nicht mehr trotz Überschuss — Wurzel: invertierte Ziel-Erkennung.**
Eine `solar_only`-Wallbox mit eingeschaltetem Bereitschafts-Floor („Immer einsatzbereit",
mindest 40 %) aber ausgeschaltetem Tagesziel (komfort 0) wurde fälschlich als
Kühl-/invertierte Konfiguration erkannt (Werte-Ordnung 40 > 0). Folge: die MINDEST-
Sicherheitsstufe feuerte bei JEDEM SoC ≥ 40 % (auch bei 90 %!) und erzwang Netzladen mit
voller Leistung — und verdrängte dabei die gesamte Live-PV-Überschuss-Regelung.

- **Fix:** Ziel-Inversion gilt nur noch für `climate` (Kühlbetrieb). SoC-Geräte
  (Wallbox/Batterie) sind immer „höher = besser". `DeviceTargets` trägt jetzt die
  Kategorie; Controller, Planner und MILP erkennen die Richtung kategorie-sicher.
- **Ehrlicher Tagesplan bei vollem Auto:** Ist das Auto auf/über seinem eigenen Ladelimit
  (z. B. 90 % bei Limit 80 %), sagt der Plan jetzt „Ladelimit erreicht" statt „kein
  Überschuss", während der PV-Überschuss (Auto voll + Hausakku voll) ehrlich ins Netz geht.

## 1.2.61

**Solar-Start/Stopp markenuniversell abgeschlossen.** Aufbauend auf 1.2.60 die
restlichen Lücken geschlossen, damit Solar für JEDE Auto-Anbindung sauber
start/stoppt und ehrlich bleibt:

- **Tier-A-Cloud-Autos (Tesla Fleet) starten/stoppen jetzt autonom**: Der
  Solar-Loop ruft `start_charging`/`stop_charging` über die Vendor-API
  (`_sync_vendor_car_charging`, deduped/backoff). `set_charge_current` allein
  startet ein stehendes Tesla NICHT — vorher setzte Solar nur die Ampere und das
  Auto lud trotzdem nie. Gilt zusätzlich zum HA-Schalter-Pfad aus 1.2.60.
- **Ehrlicher Stopp, wenn das Auto voll ist**: Der Controller bekommt jetzt den
  Live-SoC + das Ladelimit des verknüpften Autos (aus dem Batch-States-Cache,
  frisch — Limit-Änderung wirkt sofort) und stoppt im Solar-Modus mit „Auto bei
  X %, Ladelimit Y % erreicht" statt fälschlich „Ich lade Z kW" bei 0 W zu melden.
- **Solar-Start ohne Verzögerung**: schon in 1.2.60, hier mit abgedeckt — der
  erste Ladebeginn wird nicht mehr vom Re-Assert-Throttle gehalten.

## 1.2.60

**Solar-Modus startet/stoppt jetzt wirklich — das verknüpfte Auto wird autonom
ladebereit gemacht.** Bisher regelte der Solar-Überschuss-Loop nur die
Wallbox-Stromstärke, fasste aber den eigenen Laden-Schalter des Autos (z. B.
Tesla via Tessie) nie an — das tat nur der „Jetzt laden"/„Stopp"-Tap. Folge:
Überschuss da, Wallbox auf 10 A gestellt, Auto aber deaktiviert → 0 W, der
Überschuss floss ins Netz (Entscheidung „Ich lade 7,2 kW" bei real 0 W).

- **Eine geteilte Auto-Koordination** (`devices/car_link.py`,
  `set_linked_car_charging`): schaltet den Laden-Schalter des verknüpften
  HA-Autos AN (weckt es bei Bedarf) bzw. AUS — deduped + Backoff, geteilt vom
  `/mode`-Tap (`force=True`, sofort) und vom autonomen 2-s-Loop
  (überschuss-getaktet). Tap und Loop teilen denselben Dedup-State, streiten also
  nie um den Schalter.
- **Autonomer Executor kann das Auto endlich erreichen**: Der Hintergrund-Loop
  baute den `ActionExecutor` ohne `vehicle_registry`/`tier_manager` → das
  verknüpfte Auto wurde nie aufgelöst, weder Strom- noch Schalter-Sync liefen
  autonom (nur über den Tap). Jetzt wie im `/mode`-Pfad verdrahtet.
- **Solar-START ist sofort**: Der Erst-Ladebeginn wird nicht mehr bis zu 2 min
  vom Re-Assert-Throttle gehalten (START ist nun wie STOPP eine prompte
  Transition).
- **Kein `receiveTimeout` mehr beim Moduswechsel**: Der Replan im `/mode` ist
  zeitlich begrenzt (4 s) — eine träge HA blockiert die Antwort nicht mehr.

## 1.2.31

**Wallbox „offline" obwohl online + „Laden" tat nichts — behoben.** Eine OCPP-
Wallbox (z. B. ABB Terra AC), die in HA voll erreichbar ist, wurde fälschlich als
offline geführt, lud auf Knopfdruck nicht, zeigte keine kW und meldete trotzdem
„Mach ich". Wurzel: ein einzelnes dauerhaft `unavailable`-Sensor-Entity kippte das
ganze Gerät auf offline — und das schaltete im Hintergrund die Steuerung ab.

- **Ehrlicher Online-Status**: Ein Gerät gilt nur als offline, wenn *kein* Entity
  mehr antwortet — ein einzelner toter Nebensensor (Leistung) kippt es nicht mehr,
  und der Status erholt sich selbst wieder auf „online" (vorher blieb er für immer
  offline).
- **Echte kW**: Bei Wallboxen wird die tatsächlich gelieferte Leistung
  (`power_active_import`) bevorzugt statt eines oft leeren Setpoints
  (`power_offered`).
- **Ehrlicher Lade-Status**: Der Stecker-/Ladezustand (z. B. „Verbunden · Auto
  pausiert") wird angezeigt statt „offline" — inkl. „Kein Auto verbunden". Der
  Status-Sensor wird korrekt erkannt (nicht der Stecker-Zähler).
- **„Laden"/„Stopp" wirkt wirklich**: Der OCPP-Lade-Schalter wird mitgeschaltet,
  und die App meldet das *echte* Ergebnis (angenommen / kein Auto / fehlgeschlagen)
  statt blind „Mach ich".
- **„Geräte neu erkennen" ergänzt fehlende Fähigkeiten**: ein Rescan übernimmt jetzt
  auch neu erkennbare Felder (Lade-Status, Lade-Schalter), nicht nur bestehende.

## 1.2.30

(intern: erste Fassung der Wallbox-Korrekturen — durch 1.2.31 ersetzt)

## 1.2.29

**Falsch erkannt? In wenigen Klicks korrigierbar (Spec 30, Korrektur-UI).** Auto-
Erkennung ist nie zu 100 % markenübergreifend — darum ist jetzt jede Fehl-Erkennung
ohne Löschen-und-neu reparierbar:

- **Fehlendes Feld ergänzen**: der Zuordnungs-Dialog zeigt jetzt alle Felder, die
  ein Gerät haben sollte (z. B. Batterie-Leistung) — fehlt eines, kann man es direkt
  zuordnen, nicht nur bestehende ändern.
- **Sensor von einem anderen Gerät wählen**: Kandidaten sind nicht mehr auf das eine
  HA-Gerät beschränkt — der passende Sensor (z. B. die Batterie-Leistung am
  Wechselrichter) ist mit Live-Wert wählbar und als „anderes Gerät" markiert.
- **Geräte-Typ ändern**: im Geräte-Detail (Experten-Modus) lässt sich ein
  fehlklassifiziertes Gerät auf den richtigen Typ umstellen — die Sensoren werden
  automatisch neu zugeordnet.

## 1.2.28

**Geräte-Erkennung hört auf zu raten (Spec 30).** Die Aufteilung aus 1.2.27 hat
in echten Fronius-Anlagen mehr kaputt gemacht als geholfen: das System-Gerät
„SolarNet" wurde in Solar/Batterie/Netz zerlegt und duplizierte die echten Geräte
(Wechselrichter, Batterie, Smartmeter) — die **Batterie zeigte 0 kW**, weil
Ladestand und Leistung in getrennte Geräte zerrissen wurden. Jetzt:

- **Ein HA-Gerät mit mehreren Rollen** (SolarNet-Aggregator) wird **nicht mehr
  zerlegt**. Es ergänzt nur, was fehlt — die Batterie-Leistung wandert zum echten
  Batterie-Gerät, sodass **Ladestand UND Leistung an einem Gerät** stehen.
- **Kein Raten mehr**: eine Wallbox/ein Auto, deren Sensoren „charge/export" im
  Namen haben, wird nie als Hausbatterie/Netz gelesen. Forecast- und selbstgebaute
  Hilfssensoren (Template/Scrape) landen unter „Erweitert", nicht als echtes Gerät.
- **Auto ↔ Wallbox nur noch auf Ansage**: ein Fahrzeug wird nicht mehr automatisch
  an „die" Wallbox gehängt — du verknüpfst es selbst (oder lässt es offen).
- Unsichere Vorschläge (z. B. zusammengeführte Batterie) sind mit **„bitte prüfen"**
  markiert.

Ersetzt die Rollen-Aufteilung aus 1.2.27. Abgesichert mit einem Test gegen einen
echten (anonymisierten) Haushalt.

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
