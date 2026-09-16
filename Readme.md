# JUDO i-soft PRO / PRO L – Home Assistant Integration

**Lokale REST-API-Integration für den JUDO i-soft PRO / PRO L**

```text
      JUDO i-soft PRO / PRO L
      Home Assistant Integration
      Dokumentation & Einrichtungsanleitung
      Lokale REST-API / HTTP Basic Auth
```

> **WICHTIG:**  
> Diese Integration befindet sich derzeit in der **Testphase**. Die verwendeten REST-Endpunkte und Datenformate wurden für die Integration des JUDO i-soft PRO / PRO L umgesetzt und sollten vor dem produktiven Einsatz entsprechend geprüft werden.

---

# 1. Überblick

Diese Integration ermöglicht die lokale Einbindung eines **JUDO i-soft PRO bzw. i-soft PRO L** in Home Assistant.

Die Kommunikation erfolgt über die lokale REST-Schnittstelle des i-soft.

Die Integration ermöglicht sowohl:

- das **Auslesen von Gerätedaten** (z. B. Wasserhärte, Salzgehalt, Verbräuche, Gerätenummer) über die REST-Sensoren in `rest.yaml`
- das **Setzen von Einstellungen** (z. B. Wunschwasserhärte, Härteeinheit, Grenzwerte)
- das **Starten von Aktionen** (z. B. Regeneration, Szene aktivieren, Leckageschutz)
- die Bedienung über Home-Assistant-Helper
- die automatische Aktualisierung der ausgelesenen Werte
- die Verwendung eines zentralen IP-Helpers

---

# 2. Aktueller Lieferumfang

Die aktuelle Integration besteht aus folgenden Kern-Dateien:

| Datei                            | Funktion                                                                               |
| -------------------------------- | -------------------------------------------------------------------------------------- |
| `configuration.yaml`             | Hauptkonfiguration & Einbindung der Modul-Dateien / Package-Ordner                     |
| `rest.yaml`                      | Enthält die REST-Sensor-Abfragen für die Mess- und Statuswerte des i-soft              |
| `judo_isoft_complete.yaml`       | Zentrale Integration mit Helpern, REST-Befehlen, Scripts und Automations               |
| `Dashboard.yaml`                 | Lovelace-Dashboard zur Bedienung und Anzeige                                           |

Je nach verwendeter Installation können zusätzlich Dateien wie `customize.yaml` oder weitere Home-Assistant-Konfigurationsdateien vorhanden sein.

## WICHTIG

Bereits vorhandene Home-Assistant-Konfigurationen dürfen **nicht überschrieben oder gelöscht** werden.

Die benötigten Einträge müssen in die bestehende Konfiguration integriert werden.

---

# 3. Voraussetzungen

Für die Nutzung werden benötigt:

- [ ] JUDO i-soft PRO oder i-soft PRO L
- [ ] Funktionsfähige Home-Assistant-Installation
- [ ] Netzwerkverbindung zwischen Home Assistant und dem i-soft
- [ ] Aktivierte REST-API am i-soft
- [ ] Lokale IP-Adresse des i-soft
- [ ] Die benötigten YAML-Dateien der Integration (`rest.yaml`, `judo_isoft_complete.yaml`, etc.)

Beispiel aus der Konfiguration:

```text
i-soft PRO IP-Adresse:
192.168.178.2
```

Die IP-Adresse muss durch die tatsächliche IP-Adresse des eigenen Gerätes in Home Assistant angepasst werden.

---

# 4. REST-API am i-soft PRO / PRO L aktivieren

Vor der Einrichtung in Home Assistant muss die REST-API am i-soft aktiviert werden.

Am Gerät:

1. Hauptmenü öffnen.
2. **Konnektivität** auswählen.
3. **REST-API** öffnen.
4. REST-API auf **ON** stellen.
5. Die gewünschte Verschlüsselungsart / Schnittstelle auswählen.
6. Einstellungen mit **OK** bestätigen.
7. Einstellungen speichern.

## TCP- bzw. SSL-Verschlüsselung

Je nach vorhandener Ausstattung kann die Kommunikation über unterschiedliche Varianten erfolgen.

Wenn kein Homeguard verwendet wird, kann die entsprechende Standard-HTTP/TCP-Variante verwendet werden.

Bei Verwendung eines Homeguards kann die SSL-Variante erforderlich sein.

> **Hinweis:**  
> Die aktuelle hier beschriebene Konfiguration verwendet die in der Integration hinterlegte REST-Kommunikation via HTTP Basic Auth. Für eine SSL-Variante können zusätzliche Anpassungen erforderlich sein.

---

# 5. Integration & Dateistruktur

Die Integration kann flexibel entweder über direkte `!include`-Anweisungen in der `configuration.yaml` oder als **Home Assistant Package** eingebunden werden.

### Option A: Einbindung über `rest.yaml` & Packages

In der `configuration.yaml`:

```yaml
default_config:

# REST-Sensoren aus separater Datei laden
rest: !include rest.yaml

# Package-Ordner aktivieren
homeassistant:
  packages: !include_dir_named packages
```

### Dateistruktur:

```text
/config/
│
├── configuration.yaml
├── rest.yaml
│
├── packages/
│   └── judo_isoft_complete.yaml
│
└── Dashboard.yaml
```

Die Datei `rest.yaml` enthält die einzelnen REST-Sensor-Definitionen (`- resource: ...`), während `judo_isoft_complete.yaml` die Steuerbefehle, Helper und Automations bereitstellt.

---

# 6. configuration.yaml

Die vorhandene `configuration.yaml` darf **nicht vollständig ersetzt** werden.

Falls noch nicht vorhanden, müssen die Einbindungen ergänzt werden:

```yaml
default_config:

rest: !include rest.yaml

homeassistant:
  packages: !include_dir_named packages
```

> **Wichtig:**  
> Wenn bereits ein `homeassistant:`-Abschnitt oder eine `rest:`-Zeile vorhanden ist, dürfen diese nicht dupliziert werden. Ergänzen Sie die bestehenden Blöcke entsprechend.

---

# 7. Zentrale IP-Adresse

Die IP-Adresse des i-soft wird zentral über den Home-Assistant-Helper

```text
input_text.isoft_ip
```

verwaltet.

In der Integration befindet sich beispielsweise:

```yaml
input_text:
  isoft_ip:
    name: "i-soft IP-Adresse"
    initial: "192.168.178.2"
    min: 7
    max: 15
    mode: text
```

Die IP-Adresse muss an die eigene Installation angepasst werden.

## Vorteil

Die IP-Adresse muss nicht bei jedem einzelnen REST-Befehl oder Sensor manuell geändert werden.

Die REST-Abfragen in `rest.yaml` und die REST-Befehle verwenden automatisch den Wert aus `input_text.isoft_ip`.

Beispiel eines verwendeten REST-Pfades:

```text
http://192.168.178.2/api/rest/5100
```

---

# 8. Authentifizierung

Die aktuelle Integration verwendet für die REST-Kommunikation folgende Standard-Zugangsdaten:

```text
Benutzername: admin
Passwort: Connectivity
```

Die Kommunikation ist in der aktuellen Konfiguration als **HTTP Basic Authentication** hinterlegt.

Beispiel:

```yaml
authentication: basic
username: "admin"
password: "Connectivity"
```

> **Wichtig:**  
> Falls die Zugangsdaten am i-soft geändert wurden, müssen die entsprechenden Einträge in `rest.yaml` und der Package-Datei ebenfalls angepasst werden.

---

# 9. Funktionen der aktuellen Integration

Die Integration enthält aktuell folgende Steuer- und Hilfselemente:

## 9.1 Eingabefelder (Helper)

Folgende Home-Assistant-Helper stehen zur Verfügung:

- **IP-Adresse:** `input_text.isoft_ip`
- **Wunschwasserhärte:** `input_number.isoft_wunschwasserhaerte_eingabe`
- **Salzmangel-Warnschwelle:** `input_number.isoft_salzmangel_warnung_eingabe`
- **Maximale Entnahmemenge:** `input_number.isoft_max_entnahmemenge_eingabe`
- **Maximaler Volumenstrom:** `input_number.isoft_max_volumenstrom_eingabe`
- **Urlaubstage:** `input_number.isoft_urlaubsmodus_tage`

---

## 9.2 Auswahlfelder (Helper)

Zusätzlich stehen Auswahlmöglichkeiten für folgende Funktionen zur Verfügung:

### Härteeinheit (`input_select.isoft_haerteeinheit_auswahl`)

- 0 - °dH
- 1 - °eH
- 2 - °fH
- 3 - gpg
- 4 - ppm
- 5 - mmol
- 6 - mval

### Szenen (`input_select.isoft_szene_auswahl`)

- 0 - Alltag meistern
- 1 - Körper pflegen
- 2 - Garten bewässern
- 3 - Urlaub genießen
- 4 - Wäsche waschen
- 5 - Hochdruckreinigen
- 6 - Pool befüllen
- 7 - Heizung befüllen
- 8 - Custom Szene 1
- 9 - Custom Szene 2
- A - Custom Szene 3

### Szenendauer (`input_select.isoft_szene_dauer_auswahl`)

- 15 Minuten - 000F
- 30 Minuten - 001E
- 45 Minuten - 002D
- 60 Minuten / 1 Std - 0100
- 2 Stunden - 0200
- 6 Stunden - 0600
- 12 Stunden - 0C00
- Dauerhaft - FFFF

### Maximale Entnahmedauer (`input_select.isoft_entnahmedauer_auswahl`)

Von **10 min** bis **600 min** in den vorgesehenen Stufen.

---

# 10. REST-Schnittstelle – Auslesen (`rest.yaml`)

In der Datei `rest.yaml` befinden sich **28 REST-Sensor-Einträge**.

Diese lesen verschiedene Werte direkt über die REST-API des i-soft aus.

Unter anderem werden folgende Daten ausgelesen:

| Bereich       | Werte                                | Endpunkt |
| ------------- | ------------------------------------ | -------- |
| Wasserhärte   | Wunschwasserhärte                    | `/5100`  |
| Härteeinheit  | verwendete Einheit                   | `/2300`  |
| Salz          | Salzgewicht                          | `/5600`  |
| Salz          | Salzreichweite                       | `/5600`  |
| Salz          | Salzmangel-Warnschwelle              | `/5700`  |
| Wasser        | Gesamtwassermenge                    | `/2800`  |
| Wasser        | Weichwassermenge                     | `/2900`  |
| Leckageschutz | maximale Entnahmedauer               | `/3E00`  |
| Leckageschutz | maximale Entnahmemenge               | `/3F00`  |
| Leckageschutz | maximaler Volumenstrom               | `/4000`  |
| Gerät         | Gerätetyp                            | `/FF00`  |
| Gerät         | Gerätenummer                         | `/0600`  |
| Gerät         | Firmware-Version                     | `/0100`  |
| Gerät         | Betriebstage                         | `/2500`  |
| Gerät         | Inbetriebnahmedatum                  | `/0E00`  |
| Service       | Kundendienst-Telefon                 | `/5800`  |
| Verbrauch     | Wasserverbrauch Tag/Woche/Monat/Jahr | `/FB00`–`/FE00` |
| Verbrauch     | Volumenstrom Tag/Woche/Monat/Jahr    | `/F700`–`/FA00` |
| Verbrauch     | Salzverbrauch Tag/Woche/Monat/Jahr   | `/F300`–`/F600` |

Die REST-Sensoren verwenden unterschiedliche Abfrageintervalle (von 60 Sekunden bis 86.400 Sekunden).

---

# 11. Automatische Aktualisierung nach Änderungen

Eine wichtige Funktion der aktuellen Version ist die automatische Aktualisierung nach einer Änderung über die Home-Assistant-Oberfläche.

Wenn ein entsprechender Einstellungswert geändert wird, wartet Home Assistant **5 Sekunden** und führt anschließend eine gezielte Entitäten-Aktualisierung (`homeassistant.update_entity`) auf die in `rest.yaml` definierten Sensoren durch.

Ablauf:

```text
Wunschwasserhärte ändern
        ↓
REST-Befehl wird über Script ausgeführt
        ↓
5 Sekunden warten (Automation)
        ↓
i-soft Werte erneut abfragen (Sensor-Update aus rest.yaml)
```

Die Automation (`isoft_auto_refresh_after_change`) reagiert unter anderem auf Änderungen von:

- Wunschwasserhärte
- Härteeinheit
- Szenenauswahl
- Entnahmedauer
- maximaler Entnahmemenge
- maximalem Volumenstrom
- Salzmangel-Warnschwelle
- Urlaubstage

---

# 12. Steuerbefehle (REST Commands)

Die Integration stellt folgende REST-Befehle (`rest_command`) zur Verfügung:

```text
isoft_regeneration_starten
isoft_wunschwasserhaerte_setzen
isoft_szene_aktivieren
isoft_szene_zuruecksetzen
isoft_salzmangel_warnung_setzen
isoft_haerteeinheit_setzen
isoft_leckageschutz_schliessen
isoft_leckageschutz_oeffnen
isoft_max_entnahmedauer_setzen
isoft_max_entnahmemenge_setzen
isoft_max_volumenstrom_setzen
isoft_urlaubsmodus_starten
```

---

# 13. Scripts für die Bedienung

Für die Bedienung über Home Assistant werden entsprechende Scripts bereitgestellt, welche die Home-Assistant-Syntax nutzen:

- `script.isoft_set_wunschwasserhaerte_ui`
- `script.isoft_set_haerteeinheit_ui`
- `script.isoft_activate_scene_ui`
- `script.isoft_reset_scene_ui`
- `script.isoft_set_salt_warning_ui`
- `script.isoft_set_max_duration_ui`
- `script.isoft_set_max_volume_ui`
- `script.isoft_set_max_flow_ui`
- `script.isoft_start_regeneration_ui`
- `script.isoft_leak_close_ui`
- `script.isoft_leak_open_ui`
- `script.isoft_start_holiday_ui`

Diese Scripts bilden die Verbindung zwischen den Home-Assistant-Helfern und den REST-Befehlen.

---

# 14. Besonderheit bei der maximalen Entnahmedauer

Die maximale Entnahmedauer wird in der aktuellen Implementierung über einen Byte-Wert übertragen.

Daher prüft das Script `isoft_set_max_duration_ui`, ob der ausgewählte Wert innerhalb des übertragbaren Bereichs liegt (max. 255 Minuten).

- **0–255 Minuten:** Befehl wird normal übertragen.
- **> 255 Minuten:** Befehl wird abgebrochen und eine Home-Assistant-Benachrichtigung (`persistent_notification`) erzeugt.

---

# 15. Dashboard

Die Datei `Dashboard.yaml` enthält ein vorbereitetes Lovelace-Dashboard für den i-soft PRO.

Das Dashboard ist in verschiedene Bereiche gegliedert:

- **Anlage- und Gerätedaten:** Typ, Nummer, Firmware, Betriebstage, Inbetriebnahme
- **Szenen und Bedienung:** Szenenauswahl, Szenendauer, Aktivierung, Zurücksetzen, Regeneration
- **Leckageschutz:** Öffnen/Schließen, Entnahmedauer, Entnahmemenge, Volumenstrom
- **Wasserhärte:** Wunschwasserhärte, Härteeinheit
- **Salz:** Gewicht, Reichweite, Salzmangel-Warnschwelle, Verbrauch
- **Verbrauch:** Wasserverbrauch, Volumenstrom, Gesamtwasser, Weichwasser

---

# 16. Dashboard einrichten

1. Home Assistant öffnen.
2. Zum gewünschten Dashboard wechseln.
3. Oben rechts das Menü öffnen -> **Dashboard bearbeiten**.
4. Erneut das Menü öffnen -> **Raw-Konfigurationseditor**.
5. Vorhandene Konfiguration sichern.
6. Inhalt aus `Dashboard.yaml` einfügen/integrieren.
7. Speichern und Dashboard neu laden.

---

# 17. Empfohlene Dateistruktur

```text
/config/
│
├── configuration.yaml
├── rest.yaml
│
├── packages/
│   └── judo_isoft_complete.yaml
│
└── Dashboard.yaml
```

---

# 18. Erste Inbetriebnahme

1. **Dateien ablegen:** `rest.yaml` in `/config/` ablegen und `judo_isoft_complete.yaml` in `/config/packages/`.
2. **IP-Adresse prüfen:** In `input_text.isoft_ip` die IP-Adresse kontrollieren/anpassen.
3. **REST-API prüfen:** Sicherstellen, dass die REST-API am i-soft aktiviert ist.
4. **Netzwerkverbindung prüfen:** Erreichbarkeit testen.
5. **YAML-Konfiguration prüfen:** Unter *Einstellungen -> System -> Entwicklerwerkzeuge / Konfiguration prüfen*.
6. **Home Assistant neu starten**.
7. **Sensoren kontrollieren:** Unter *Entwicklerwerkzeuge -> Zustände* nach `sensor.i_soft_*` suchen.
8. **Dashboard öffnen** und Werte prüfen.
9. **Steuerfunktionen testen**.

---

# 19. Fehlerbehebung

- **Keine Werte vom i-soft:** IP-Adresse, Passwörter, Netzwerkverbindung und API-Aktivierung am Gerät prüfen.
- **REST-Sensoren werden nicht angelegt:** Prüfen, ob `rest: !include rest.yaml` in `configuration.yaml` korrekt eingebunden ist und die Dateipfade stimmen.
- **Package wird nicht geladen:** Checken, ob `packages:` in `configuration.yaml` aktiviert ist.
- **Werte aktualisieren nicht:** Einige Sensoren haben ein Intervall von bis zu 86400s (z. B. Firmware/Seriennummer). Nach UI-Änderungen greift die 5-Sekunden-Automation.
- **Dashboard zeigt fehlende Entitäten:** Entity-IDs der Sensoren und Scripts in den Entwicklerwerkzeugen abgleichen.

---

# 20. Sicherheitshinweise

Die Kommunikation erfolgt unverschlüsselt im lokalen Netzwerk (HTTP Basic Auth).
Sensible Zugangsdaten sollten nicht in öffentlichen GitHub-Repositories oder Screenshots geteilt werden.

---

# 21. Lizenz / Herstellerbezug

**Hersteller:** JUDO Wasseraufbereitung GmbH  
**Produkt:** JUDO i-soft PRO / PRO L  
**System:** Home Assistant  

> Diese Dokumentation beschreibt eine inoffizielle technische Home-Assistant-Integration für den JUDO i-soft PRO / PRO L und stellt **keine offizielle Herstellerspezifikation der JUDO Wasseraufbereitung GmbH** dar.

---

# 22. Änderungsstand

**Letzte Überarbeitung:** 16.09.2026

### Aktueller Stand

- Separate Einbindung der Sensor-Abfragen über `rest.yaml`
- Zentrale Steuerungs- und Logikdatei `judo_isoft_complete.yaml`
- Vollständige Integration von 28 REST-Sensoren, REST-Commands, Helpern und Scripts
- Standardisierte Action-Syntax für Home Assistant (`action:`-Aufrufe)
- Automatische Sensoraktualisierung 5 Sekunden nach UI-Anpassung
- Salzgewicht (`kg`) und Salzreichweite (`Tage`) als getrennte Sensoren auf Endpunkt `/5600`
- Sicherheitsabfrage bei max. Entnahmedauer (> 255 min)

**Status: Testphase**
