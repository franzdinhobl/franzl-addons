# Franzl Add-ons

Öffentliches Home-Assistant-Add-on-Repository für **Franzl Gateway** — intelligentes,
herstellerübergreifendes Energiemanagement (PV, Batterie, Wallbox, Wärmepumpe).

> **Dies ist ein reines Verteil-Repo.** Es enthält nur die Add-on-Metadaten
> (`config.yaml`, Doku, Icon) und verweist auf ein **vorgebautes Container-Image**
> auf `ghcr.io`. Der Quellcode liegt in einem separaten, privaten Repository.
> Home Assistant lädt beim Installieren nur das fertige Image — es wird nichts
> lokal kompiliert (schnelle, zuverlässige Installation).

## Installation

### 1-Klick (empfohlen)

[![Open your Home Assistant instance and add this add-on repository.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Ffranzdinhobl%2Ffranzl-addons)

Auf den Button klicken → Home Assistant öffnet sich → Repository ist hinzugefügt.
Danach **Franzl Gateway** im Add-on-Store installieren und starten.

### Manuell

1. In Home Assistant: **Einstellungen → Add-ons → Add-on Store → ⋮ → Repositories**
2. URL einfügen: `https://github.com/franzdinhobl/franzl-addons`
3. **Franzl Gateway** installieren und **starten**
4. Im Add-on-Panel erscheint ein **9-stelliger Aktivierungscode** — diesen in der
   Franzl App eingeben. Fertig.

## Add-ons

| Add-on | Beschreibung |
|--------|--------------|
| [Franzl Gateway](./energy_gateway) | Energie-Optimierer als HA-Add-on (FastAPI + gebündelte PostgreSQL) |

## Hinweis für Maintainer

Die Dateien unter [`energy_gateway/`](./energy_gateway) werden aus dem privaten
Quell-Repo generiert (`scripts/sync-addon-repo.sh`). Nicht hier von Hand
editieren — Änderungen gehen beim nächsten Sync verloren.
