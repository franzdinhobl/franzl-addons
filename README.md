# Franzl — dein Energie-Butler für Home Assistant

Franzl verteilt deinen **PV-Überschuss über Auto, Wärmepumpe, Warmwasser und Batterie
gemeinsam** — nicht jedes Gerät für sich. Läuft als Add-on auf deiner eigenen
Home-Assistant-Installation, herstellerübergreifend, ohne Cloud-Zwang für die Steuerung.

**Kostenlos.** Deine Energiedaten bleiben auf deiner Installation.

[![Open your Home Assistant instance and add this add-on repository.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Ffranzdinhobl%2Ffranzl-addons)

---

## Warum überhaupt noch ein Energiemanager?

Einzeln kann fast jedes Gerät heute Überschuss laden. Mehrere davon nebeneinander laufen zu
lassen ist die Stelle, an der es kippt: **der Hausspeicher nimmt die Sonne, die das Auto
gebraucht hätte, und der Heizstab startet genau dann, wenn die Wolke schon da ist.** Jedes Teil
verhält sich für sich korrekt — und das Haus macht es trotzdem falsch.

Franzl rechnet stattdessen **einen Überschuss-Topf pro Regelzyklus** und verteilt ihn in einer
Reihenfolge, die du festlegst. Der Hausspeicher steht in dieser Reihenfolge als ganz normaler
Verbraucher mit drin: Alles, was über ihm gereiht ist, sieht den vollen Topf — das Auto bekommt
also die Sonne, die sonst im Akku landet, ganz ohne Befehl an den Akku.

Dazu kommt die Zeit: Bei einem dynamischen Tarif plant Franzl Ladung und Wärme in die günstigen
Stunden, und das Warmwasser-Band wird als **Speicher** gerechnet — Vorheizen lohnt sich, wenn
die Alternative teurer ist, und zu viel Puffer verhindert sich von selbst, weil eingelagerte
Wärme wieder wegleckt.

## Was Franzl nicht tut

Das ist der Teil, der uns beim Bauen am meisten beschäftigt hat:

- **Unbekannt ist nicht null.** Ist ein Zähler dunkel, sagt Franzl das — statt 0 W anzuzeigen
  und daraus 100 % Autarkie zu rechnen.
- **Kein „lädt", wenn nichts fließt.** Meldet eine Wallbox „charging", während die Messung
  darunter liegt, wird das ehrlich als Pause des Autos ausgegeben.
- **Kein „erledigt" ohne Bestätigung.** Ein Befehl, den wir abgeschickt, aber nicht verifiziert
  haben, heißt im Rückblick genau das.
- **Kein Hämmern.** Ein Gerät, das unsere Befehle nicht annimmt, wird nicht im Minutentakt
  angefunkt, sondern mit wachsendem Abstand — und du erfährst davon.

## Voraussetzungen

| | |
|---|---|
| **Home Assistant** | **HA OS oder Supervised.** Container/Core funktionieren **nicht** — es braucht den Supervisor. |
| Hardware | Mindestens ein Wechselrichter, Speicher, Wallbox, Wärmepumpe oder Warmwasserspeicher in HA |
| App | [iOS](https://apps.apple.com/at/app/franzl/id6779250378) · [Android](https://play.google.com/store/apps/details?id=app.franzl) |

## Installation

1. **Repository hinzufügen** — Button oben, oder in Home Assistant unter
   **Einstellungen → Add-ons → Add-on Store → ⋮ → Repositories** die URL
   `https://github.com/franzdinhobl/franzl-addons` eintragen.
2. **Franzl Gateway** installieren und **starten**.
3. Im Add-on-Panel steht ein **9-stelliger Aktivierungscode**. Die App findet dein Gateway im
   Heimnetz meist von selbst und füllt den Code aus — ein Tap genügt.

Ausführlich: [getfranzl.com/installieren](https://getfranzl.com/installieren/)

## Unterstützte Geräte

Über 140 Modelle von Fronius, Kostal, SMA, Huawei, Sonnen, E3DC, go-e, KEBA, Easee, Zaptec,
Viessmann, Vaillant, Daikin, Nibe, IDM und weiteren.

**Ehrlich dazu:** Die Anbindung ist bei den meisten Marken aus der Herstellerdokumentation
abgeleitet und geprüft — **wirklich gegen echte Hardware gefahren haben wir bisher fünf**
(Fronius GEN24, GEN24 Hybrid, ABB Terra AC, NRGkick, Shelly Plug). Die tragen auf der Geräteseite
das Feld-Badge. Welches Gerät gesteuert und welches nur gelesen werden kann, steht ebenfalls dort
— pro Modell, nicht pauschal:

**→ [Geräte-Datenbank auf getfranzl.com](https://getfranzl.com/geraete/)**

Fehlt deins oder verhält es sich anders als beschrieben? Das interessiert uns wirklich: in der App
unter **Mehr → Feedback** (Versionen und Gerätemarken hängen automatisch dran) oder an
[hi@getfranzl.com](mailto:hi@getfranzl.com).

## Fragen, die oft kommen

**Braucht Franzl Internet?** Für die Steuerung nicht — sie läuft lokal auf deiner Installation.
Für Fernzugriff, Push-Nachrichten und Mehrbenutzer-Zugang schon.

**Bleibt es kostenlos?** Monitoring, Berichte, Fernzugriff, Störungs-Alerts und manuelle
Eingriffe bleiben kostenlos. Für die automatische Optimierung wird es später ein Abo geben —
wer jetzt dabei ist, behält sie dauerhaft.

**Was passiert mit meinen Daten?** Energiedaten bleiben auf deiner Installation. Was die Cloud
sieht, steht in der [Datenschutzerklärung](https://getfranzl.com/datenschutz).

---

## Über dieses Repository

**Reines Verteil-Repo.** Es enthält nur die Add-on-Metadaten (`config.yaml`, Doku, Icon) und
verweist auf ein **vorgebautes Container-Image** auf `ghcr.io`. Home Assistant lädt beim
Installieren nur das fertige Image — es wird nichts lokal kompiliert.

| Add-on | Beschreibung |
|--------|--------------|
| [Franzl Gateway](./energy_gateway) | Energie-Optimierer als HA-Add-on (FastAPI + gebündelte PostgreSQL) |

Sicherheitslücke gefunden? [hi@getfranzl.com](mailto:hi@getfranzl.com) mit Betreff „Security".

### Hinweis für Maintainer
Die Dateien unter [`energy_gateway/`](./energy_gateway) werden aus dem privaten Quell-Repo
generiert (`scripts/sync-addon-repo.sh`). Nicht hier von Hand editieren — Änderungen gehen beim
nächsten Sync verloren. Diese README wird **nicht** überschrieben.
