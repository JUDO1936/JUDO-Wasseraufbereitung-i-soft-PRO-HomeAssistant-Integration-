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

- das **Auslesen von Gerätedaten** (z. B. Wasserhärte, Salzgehalt, Verbräuche, Gerätenummer)
- das **Setzen von Einstellungen** (z. B. Wunschwasserhärte, Härteeinheit, Grenzwerte)
- das **Starten von Aktionen** (z. B. Regeneration, Szene aktivieren, Leckageschutz)
- die Bedienung über Home-Assistant-Helper
- die automatische Aktualisierung der ausgelesenen Werte
- die Verwendung eines zentralen IP-Helpers

Die Konfiguration ist weitgehend in einer **zentralen Package-Datei** zusammengefasst.

---

# 2. Aktueller Lieferumfang

Die aktuelle Integration besteht im Wesentlichen aus:

| Datei                            | Funktion                                                                               |
| -------------------------------- | -------------------------------------------------------------------------------------- |
| `configuration.yaml`             | Einbindung des Package-Ordners                                                         |
| `judo_isoft_complete.yaml`       | Zentrale Integration mit Helpern, REST-Abfragen, REST-Befehlen, Scripts und Automation |
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
- [ ] Die benötigten YAML-Dateien der Integration

Beispiel aus der Konfiguration:

```text
i-soft PRO IP-Adresse:
192.168.2.22
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

# 5. Installation der Package-Datei

Die aktuelle Integration ist für die Verwendung als **Home Assistant Package** aufgebaut.

Die Datei:

```text
judo_isoft_complete.yaml
```

wird beispielsweise hier abgelegt:

```text
/config/packages/judo_isoft_complete.yaml
```

Die Package-Unterstützung muss in der `configuration.yaml` aktiviert sein.

Beispiel:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

Dadurch lädt Home Assistant die YAML-Dateien aus dem Verzeichnis:

```text
/config/packages/
```

automatisch.

---

# 6. configuration.yaml

Die vorhandene `configuration.yaml` darf **nicht vollständig ersetzt** werden.

Falls noch nicht vorhanden, muss die Package-Einbindung ergänzt werden:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

Eine vorhandene `default_config:`-Zeile bleibt bestehen.

Beispiel:

```yaml
default_config:

homeassistant:
  packages: !include_dir_named packages
```

> **Wichtig:**  
> Wenn bereits ein `homeassistant:`-Abschnitt vorhanden ist, darf nicht einfach ein zweiter `homeassistant:`-Abschnitt angelegt werden. Die Package-Zeile muss in den bestehenden Abschnitt integriert werden.

---

# 7. Zentrale IP-Adresse

Die IP-Adresse des i-soft wird zentral über den Home-Assistant-Helper

```text
input_text.isoft_ip
```

verwaltet.

In der Package-Datei befindet sich beispielsweise:

```yaml
input_text:
  isoft_ip:
    name: "i-soft IP-Adresse"
    initial: "192.168.2.22"
    min: 7
    max: 15
    mode: text
```

Die IP-Adresse muss an die eigene Installation angepasst werden.

## Vorteil

Die IP-Adresse muss nicht bei jedem einzelnen REST-Befehl oder Sensor manuell geändert werden.

Die REST-Abfragen und REST-Befehle verwenden automatisch:

```text
input_text.isoft_ip
```

Beispiel eines verwendeten REST-Pfades:

```text
http://192.168.2.22/api/rest/5100
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
> Falls die Zugangsdaten am i-soft geändert wurden, müssen die entsprechenden Einträge in der Package-Datei ebenfalls angepasst werden.

---

# 9. Funktionen der aktuellen Integration

Die zentrale Package-Datei enthält aktuell folgende Bereiche:

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

# 10. REST-Schnittstelle – Auslesen

Die Package-Datei enthält **28 REST-Sensor-Einträge**.

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

Wenn ein entsprechender Einstellungswert geändert wird, wartet Home Assistant **5 Sekunden** und führt anschließend eine gezielte Entitäten-Aktualisierung (`homeassistant.update_entity`) durch.

Ablauf:

```text
Wunschwasserhärte ändern
        ↓
REST-Befehl wird über Script ausgeführt
        ↓
5 Sekunden warten (Automation)
        ↓
i-soft Werte erneut abfragen (Sensor-Update)
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
│
├── packages/
│   └── judo_isoft_complete.yaml
│
└── Dashboard.yaml
```

---

# 18. Erste Inbetriebnahme

1. **IP-Adresse prüfen:** In `input_text.isoft_ip` die IP-Adresse kontrollieren/anpassen.
2. **REST-API prüfen:** Sicherstellen, dass die REST-API am i-soft aktiviert ist.
3. **Netzwerkverbindung prüfen:** Erreichbarkeit testen.
4. **YAML-Konfiguration prüfen:** Unter *Einstellungen -> System -> Entwicklerwerkzeuge / Konfiguration prüfen*.
5. **Home Assistant neu starten**.
6. **Sensoren kontrollieren:** Unter *Entwicklerwerkzeuge -> Zustände* nach `sensor.i_soft_*` suchen.
7. **Dashboard öffnen** und Werte prüfen.
8. **Steuerfunktionen testen**.

---

# 19. Fehlerbehebung

- **Keine Werte vom i-soft:** IP-Adresse, Passwörter, Netzwerkverbindung und API-Aktivierung am Gerät prüfen.
- **REST-Sensor wird nicht angelegt:** Checken, ob `packages:` in `configuration.yaml` korrekt eingebunden ist und die YAML-Syntax stimmt.
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

- Zentrale Package-Datei `judo_isoft_complete.yaml`
- Vollständige Integration von 28 REST-Sensoren, REST-Commands, Helpern und Scripts
- Standardisierte Action-Syntax für Home Assistant (`action:`-Aufrufe)
- Automatische Sensoraktualisierung 5 Sekunden nach UI-Anpassung
- Salzgewicht (`kg`) und Salzreichweite (`Tage`) als getrennte Sensoren auf Endpunkt `/5600`
- Sicherheitsabfrage bei max. Entnahmedauer (> 255 min)

**Status: Testphase**
