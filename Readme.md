# JUDO i-soft PRO / PRO L – Home Assistant Integration

**Lokale REST-API-Integration für den JUDO i-soft PRO / PRO L**

```text
      JUDO i-soft PRO / PRO L
      Home Assistant Integration
      Dokumentation & Einrichtungsanleitung
      Lokale REST-API / TCP-Verschlüsselung
```

> **WICHTIG:**\
> Diese Integration befindet sich derzeit in der **Testphase**. Die verwendeten REST-Endpunkte und Datenformate wurden für die Integration des JUDO i-soft PRO / PRO L umgesetzt und sollten vor dem produktiven Einsatz entsprechend geprüft werden.

---

# 1. Überblick

Diese Integration ermöglicht die lokale Einbindung eines **JUDO i-soft PRO bzw. i-soft PRO L** in Home Assistant.

Die Kommunikation erfolgt über die lokale REST-Schnittstelle des i-soft.

Die Integration ermöglicht sowohl:

- das **Auslesen von Gerätedaten**
- das **Setzen von Einstellungen**
- das **Starten von Aktionen**
- die Bedienung über Home-Assistant-Helper
- die automatische Aktualisierung der ausgelesenen Werte
- die Verwendung eines zentralen IP-Helpers

Die aktuelle Version wurde gegenüber der ursprünglichen Aufteilung vereinfacht und ist weitgehend in einer **zentralen Package-Datei** zusammengefasst.

---

# 2. Aktueller Lieferumfang

Die aktuelle Integration besteht im Wesentlichen aus:

| Datei                            | Funktion                                                                               |
| -------------------------------- | -------------------------------------------------------------------------------------- |
| `configuration.yaml`             | Einbindung des Package-Ordners                                                         |
| `isoft_pro_control_package.yaml` | Zentrale Integration mit Helpern, REST-Abfragen, REST-Befehlen, Scripts und Automation |
| `Dashboard.yaml`                 | Lovelace-Dashboard zur Bedienung und Anzeige                                           |

Je nach verwendeter Installation können zusätzlich Dateien wie `customize.yaml` oder weitere Home-Assistant-Konfigurationsdateien vorhanden sein.

## WICHTIG

Bereits vorhandene Home-Assistant-Konfigurationen dürfen **nicht überschrieben oder gelöscht** werden.

Die benötigten Einträge müssen in die bestehende Konfiguration integriert werden.

---

# 3. Voraussetzungen

Für die Nutzung werden benötigt:

- [ ] JUDO i-soft PRO oder i-soft PRO L
- [ ] funktionsfähige Home-Assistant-Installation
- [ ] Netzwerkverbindung zwischen Home Assistant und dem i-soft
- [ ] aktivierte REST-API am i-soft
- [ ] lokale IP-Adresse des i-soft
- [ ] die benötigten YAML-Dateien der Integration

Beispiel:

```text
i-soft PRO IP-Adresse:
192.168.178.255
```

Die IP-Adresse ist nur ein Beispiel und muss durch die tatsächliche IP-Adresse des eigenen Gerätes ersetzt werden.

---

# 4. REST-API am i-soft PRO / PRO L aktivieren

Vor der Einrichtung in Home Assistant muss die REST-API am i-soft aktiviert werden.

Am Gerät:

1. Hauptmenü öffnen.
2. **Konnektivität** auswählen.
3. **REST-API** öffnen.
4. REST-API auf **ON** stellen.
5. Die gewünschte Verschlüsselungsart auswählen.
6. Einstellungen mit **OK** bestätigen.
7. Einstellungen speichern.

## TCP- bzw. SSL-Verschlüsselung

Je nach vorhandener Ausstattung kann die Kommunikation über unterschiedliche Varianten erfolgen.

Wenn kein Homeguard verwendet wird, kann die entsprechende TCP-Variante verwendet werden.

Bei Verwendung eines Homeguard kann die SSL-Variante erforderlich sein.

> **Hinweis:**\
> Die aktuelle hier beschriebene Konfiguration verwendet die in der Integration hinterlegte REST-Kommunikation. Für eine SSL-Variante können zusätzliche Dateien bzw. Anpassungen erforderlich sein.

---

# 5. Installation der Package-Datei

Die aktuelle Integration ist für die Verwendung als **Home Assistant Package** aufgebaut.

Die Datei:

```text
isoft_pro_control_package.yaml
```

wird beispielsweise hier abgelegt:

```text
/config/packages/isoft_pro_control_package.yaml
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

> **Wichtig:**\
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
    initial: "192.168.178.255"
    min: 7
    max: 15
    mode: text
```

Die IP-Adresse muss an die eigene Installation angepasst werden.

Beispiel:

```yaml
initial: "192.168.178.255"
```

## Vorteil

Die IP-Adresse muss nicht bei jedem einzelnen REST-Befehl geändert werden.

Die REST-Abfragen und REST-Befehle verwenden automatisch:

```text
input_text.isoft_ip
```

Beispiel eines verwendeten REST-Pfades:

```text
http://192.168.178.255/api/rest/5100
```

---

# 8. Authentifizierung

Die aktuelle Integration verwendet für die REST-Kommunikation:

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

> **Wichtig:**\
> Falls die Zugangsdaten am i-soft geändert wurden, müssen die entsprechenden Einträge in der Package-Datei ebenfalls angepasst werden.

---

# 9. Funktionen der aktuellen Integration

Die zentrale Package-Datei enthält aktuell folgende Bereiche:

## 9.1 Eingabefelder

Folgende Home-Assistant-Helper stehen zur Verfügung:

- i-soft IP-Adresse
- Wunschwasserhärte
- Salzmangel-Warnschwelle
- maximale Entnahmemenge
- maximaler Volumenstrom
- Urlaubstage

---

## 9.2 Auswahlfelder

Zusätzlich stehen Auswahlmöglichkeiten für folgende Funktionen zur Verfügung:

### Härteeinheit

- °dH
- °eH
- °fH
- gpg
- ppm
- mmol
- mval

### Szenen

- Alltag meistern
- Körper pflegen
- Garten bewässern
- Urlaub genießen
- Wäsche waschen
- Hochdruckreinigen
- Pool befüllen
- Heizung befüllen
- Custom Szene 1
- Custom Szene 2
- Custom Szene 3

### Szenendauer

- 15 Minuten
- 30 Minuten
- 45 Minuten
- 60 Minuten / 1 Stunde
- 2 Stunden
- 6 Stunden
- 12 Stunden
- dauerhaft

### Maximale Entnahmedauer

Von:

```text
10 Minuten
```

bis:

```text
600 Minuten
```

in den vorgesehenen Stufen.

---

# 10. REST-Schnittstelle – Auslesen

Die Package-Datei enthält aktuell **28 REST-Sensor-Einträge**.

Diese lesen verschiedene Werte direkt über die REST-API des i-soft aus.

Unter anderem werden folgende Daten ausgelesen:

| Bereich       | Werte                                |
| ------------- | ------------------------------------ |
| Wasserhärte   | Wunschwasserhärte                    |
| Härteeinheit  | verwendete Einheit                   |
| Salz          | Salzgewicht                          |
| Salz          | Salzreichweite                       |
| Salz          | Salzmangel-Warnschwelle              |
| Wasser        | Gesamtwassermenge                    |
| Wasser        | Weichwassermenge                     |
| Leckageschutz | maximale Entnahmedauer               |
| Leckageschutz | maximale Entnahmemenge               |
| Leckageschutz | maximaler Volumenstrom               |
| Gerät         | Gerätetyp                            |
| Gerät         | Gerätenummer                         |
| Gerät         | Firmware-Version                     |
| Gerät         | Betriebstage                         |
| Gerät         | Inbetriebnahmedatum                  |
| Service       | Kundendienst-Telefon                 |
| Verbrauch     | Wasserverbrauch Tag/Woche/Monat/Jahr |
| Verbrauch     | Volumenstrom Tag/Woche/Monat/Jahr    |
| Verbrauch     | Salzverbrauch Tag/Woche/Monat/Jahr   |

Die REST-Sensoren verwenden unterschiedliche Abfrageintervalle.

Beispiele:

```text
60 Sekunden
120 Sekunden
180 Sekunden
3600 Sekunden
86400 Sekunden
```

Dadurch werden häufig benötigte Werte häufiger abgefragt als statische Gerätedaten.

---

# 11. Automatische Aktualisierung nach Änderungen

Eine wichtige Funktion der aktuellen Version ist die automatische Aktualisierung nach einer Änderung über die Home-Assistant-Oberfläche.

Wenn ein entsprechender Einstellungswert geändert wird, wartet Home Assistant:

```text
5 Sekunden
```

und aktualisiert anschließend die relevanten REST-Sensoren.

Dadurch kann geprüft werden, ob der neue Wert vom i-soft übernommen wurde.

## Beispiel

Wird beispielsweise die Wunschwasserhärte über die Oberfläche geändert:

```text
Wunschwasserhärte ändern
        ↓
REST-Befehl wird ausgeführt
        ↓
5 Sekunden warten
        ↓
i-soft Wert erneut abfragen
        ↓
Sensor aktualisieren
```

Die Automation reagiert unter anderem auf Änderungen von:

- Wunschwasserhärte
- Härteeinheit
- Szenenauswahl
- Szenendauer bzw. Entnahmedauer
- maximaler Entnahmemenge
- maximalem Volumenstrom
- Salzmangel-Warnschwelle
- Urlaubstage

> **Hinweis:**\
> Die normale REST-Abfrage läuft unabhängig davon mit dem jeweiligen `scan_interval`. Die 5-Sekunden-Aktualisierung ist eine zusätzliche Aktualisierung nach einer Änderung über die Benutzeroberfläche.

---

# 12. Steuerbefehle

Die Integration stellt aktuell folgende REST-Befehle zur Verfügung:

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

Damit können beispielsweise folgende Aktionen durchgeführt werden:

- Regeneration starten
- Wunschwasserhärte ändern
- Härteeinheit ändern
- Szene aktivieren
- Szene zurücksetzen
- Salzmangel-Warnung einstellen
- Leckageschutz öffnen
- Leckageschutz schließen
- maximale Entnahmedauer einstellen
- maximale Entnahmemenge einstellen
- maximalen Volumenstrom einstellen
- Urlaubsmodus starten

---

# 13. Scripts für die Bedienung

Für die Bedienung über Home Assistant werden Scripts bereitgestellt.

Unter anderem:

```text
i-soft - Wunschwasserhärte setzen
i-soft - Härteeinheit setzen
i-soft - Szene aktivieren
i-soft - Szene zurücksetzen
i-soft - Salzmangelwarnung setzen
i-soft - Max. Entnahmedauer setzen
i-soft - Max. Entnahmemenge setzen
i-soft - Max. Volumenstrom setzen
i-soft - Regeneration starten
i-soft - Leckageschutz schließen
i-soft - Leckageschutz öffnen
i-soft - Urlaubsmodus starten
```

Diese Scripts bilden die Verbindung zwischen den Home-Assistant-Helfern und den REST-Befehlen.

---

# 14. Besonderheit bei der maximalen Entnahmedauer

Die maximale Entnahmedauer wird in der aktuellen Implementierung über einen Byte-Wert übertragen.

Daher wird geprüft, ob der ausgewählte Wert innerhalb des übertragbaren Bereichs liegt.

Der aktuell unterstützte technische Bereich des REST-Befehls beträgt:

```text
0 – 255 Minuten
```

Werte über 255 Minuten können mit diesem Byte-Format nicht direkt übertragen werden.

Wird beispielsweise ein höherer Wert ausgewählt, wird der Befehl nicht an das Gerät gesendet und stattdessen eine Home-Assistant-Benachrichtigung erzeugt.

> **Hinweis:**\
> Die Auswahl enthält aktuell auch Werte oberhalb von 255 Minuten. Diese Werte dienen daher nicht automatisch als gültige Übertragungswerte.

---

# 15. Dashboard

Die Datei:

```text
Dashboard.yaml
```

enthält ein vorbereitetes Lovelace-Dashboard für den i-soft PRO.

Das Dashboard ist in verschiedene Bereiche gegliedert.

## Anlage- und Gerätedaten

- Gerätetyp
- Gerätenummer
- Firmware
- Status
- Betriebstage
- Inbetriebnahmedatum

## Szenen und Bedienung

- Szenenauswahl
- Szenendauer
- Szene aktivieren
- Szene zurücksetzen
- manuelle Regeneration

## Leckageschutz

- Leckageschutz öffnen
- Leckageschutz schließen
- maximale Entnahmedauer
- maximale Entnahmemenge
- maximaler Volumenstrom

## Wasserhärte

- Wunschwasserhärte
- Härteeinheit

## Salz

- Salzgewicht
- Salzreichweite
- Salzmangel-Warnschwelle
- Salzverbrauch

## Verbrauch

- Wasserverbrauch
- Volumenstrom
- Gesamtwassermenge
- Weichwassermenge

---

# 16. Dashboard einrichten

Das Dashboard kann über den Lovelace Raw-Konfigurationseditor eingefügt werden.

### Vorgehensweise

1. Home Assistant öffnen.
2. Zum gewünschten Dashboard wechseln.
3. Oben rechts das Menü öffnen.
4. **Dashboard bearbeiten** auswählen.
5. Erneut das Menü öffnen.
6. **Raw-Konfigurationseditor** öffnen.
7. Vorhandene Konfiguration sichern.
8. Den Inhalt aus `Dashboard.yaml` in die bestehende Dashboard-Konfiguration integrieren.
9. Änderungen speichern.
10. Dashboard neu laden.

> **Wichtig:**\
> Eine vorhandene Dashboard-Konfiguration sollte nicht einfach vollständig überschrieben werden. Bestehende Views und Karten vorher sichern.

---

# 17. Empfohlene Dateistruktur

Eine einfache Installation kann beispielsweise so aussehen:

```text
/config/
│
├── configuration.yaml
│
├── packages/
│   └── isoft_pro_control_package.yaml
│
└── Dashboard.yaml
```

Je nach Home-Assistant-Konfiguration können weitere Dateien vorhanden sein.

---

# 18. Erste Inbetriebnahme

Nach der Installation sollten folgende Schritte durchgeführt werden:

### 1. IP-Adresse prüfen

In:

```text
input_text.isoft_ip
```

muss die korrekte IP-Adresse des i-soft hinterlegt sein.

### 2. REST-API prüfen

Sicherstellen, dass die REST-API am i-soft aktiviert wurde.

### 3. Netzwerkverbindung prüfen

Home Assistant muss das Gerät im lokalen Netzwerk erreichen können.

### 4. YAML-Konfiguration prüfen

In Home Assistant:

```text
Einstellungen
→ System
→ Reparaturen / YAML-Konfiguration prüfen
```

bzw. die entsprechende Funktion zur Konfigurationsprüfung verwenden.

### 5. Home Assistant neu starten

Nach erfolgreicher Prüfung Home Assistant neu starten.

### 6. Sensoren kontrollieren

Unter:

```text
Entwicklerwerkzeuge → Zustände
```

können die i-soft-Entitäten kontrolliert werden.

### 7. Dashboard öffnen

Das i-soft-Dashboard öffnen und überprüfen, ob die Werte angezeigt werden.

### 8. Erst danach Befehle testen

Wenn die Sensorwerte korrekt gelesen werden, sollten anschließend einzelne Steuerfunktionen getestet werden.

---

# 19. Fehlerbehebung

## Keine Werte vom i-soft

Folgende Punkte prüfen:

- Ist die IP-Adresse korrekt?
- Ist der i-soft eingeschaltet?
- Ist die REST-API aktiviert?
- Kann Home Assistant den i-soft im Netzwerk erreichen?
- Sind Benutzername und Passwort korrekt?
- Ist der verwendete REST-Endpunkt erreichbar?
- Ist die Package-Datei korrekt geladen?
- Gibt es Fehler im Home-Assistant-Log?

---

## REST-Sensor wird nicht angelegt

Prüfen:

- Liegt die Package-Datei im richtigen Verzeichnis?
- Ist

```yaml
homeassistant:
  packages: !include_dir_named packages
```

korrekt eingerichtet?

- Gibt es YAML-Syntaxfehler?
- Wurde Home Assistant nach Änderungen neu gestartet?
- Hat der REST-Sensor eine gültige `platform: rest`-Definition?

---

## Werte werden angezeigt, aber nicht aktualisiert

Die Sensoren besitzen unterschiedliche Abfrageintervalle.

Zusätzlich wird nach bestimmten Änderungen über die Benutzeroberfläche eine Aktualisierung nach:

```text
5 Sekunden
```

ausgelöst.

Falls ein Wert trotzdem nicht aktualisiert wird:

1. einige Sekunden warten,
2. den Sensor in den Entwicklerwerkzeugen prüfen,
3. Home-Assistant-Log kontrollieren,
4. REST-Verbindung zum Gerät überprüfen.

---

## Befehle werden nicht ausgeführt

Prüfen:

- REST-API am Gerät aktiviert?
- IP-Adresse korrekt?
- Zugangsdaten korrekt?
- REST-Endpunkt korrekt?
- Netzwerkverbindung vorhanden?
- Fehler im Home-Assistant-Log?

---

## Dashboard zeigt fehlende Entitäten

Prüfen:

- Sind die Sensoren und Scripts vorhanden?
- Wurde die Package-Datei geladen?
- Stimmen die verwendeten Entity-IDs mit der Dashboard-Konfiguration überein?
- Wurde das Dashboard nach Änderungen neu geladen?

---

# 20. Sicherheitshinweise

Die Kommunikation erfolgt innerhalb des lokalen Netzwerks über die REST-Schnittstelle des Gerätes.

Die in dieser Dokumentation bzw. Konfiguration verwendeten Zugangsdaten sollten nicht öffentlich veröffentlicht werden.

Insbesondere sollten:

- Passwörter nicht in öffentlichen Screenshots erscheinen
- IP-Adressen nicht unnötig veröffentlicht werden
- Konfigurationsdateien mit Zugangsdaten nicht öffentlich hochgeladen werden

Bei Veröffentlichung des Projektes auf GitHub sollten sensible Zugangsdaten aus den öffentlich sichtbaren Dateien entfernt oder entsprechend abgesichert werden.

---

# 21. Bekannte Einschränkungen

Die Integration befindet sich weiterhin in der **Testphase**.

Insbesondere folgende Punkte sollten bei Änderungen an der Integration überprüft werden:

- REST-Endpunkte
- Rückgabeformate der API
- Hexadezimal-/Byte-Dekodierung
- Wertebereiche einzelner Parameter
- Verhalten verschiedener i-soft-Versionen
- Unterschiede zwischen i-soft PRO und PRO L
- Verhalten bei aktivierter Verschlüsselung

Die verwendeten Register- und Dateninterpretationen sollten nicht ohne Prüfung auf andere Gerätegenerationen übertragen werden.

---

# 22. Projektstruktur

Die Integration ist so aufgebaut, dass die wesentlichen Funktionen zentral in der Package-Datei zusammengefasst sind:

```text
isoft_pro_control_package.yaml
│
├── input_text
│   └── IP-Adresse
│
├── input_number
│   ├── Wunschwasserhärte
│   ├── Salzmangel-Warnung
│   ├── max. Entnahmemenge
│   ├── max. Volumenstrom
│   └── Urlaubstage
│
├── input_select
│   ├── Härteeinheit
│   ├── Szene
│   ├── Szenendauer
│   └── Entnahmedauer
│
├── rest_command
│   └── Steuerbefehle
│
├── sensor
│   └── REST-Sensoren
│
├── script
│   └── Bedienlogik
│
└── automation
    └── automatische Aktualisierung nach Änderungen
```

---

# 23. Lizenz / Herstellerbezug

**Hersteller:**

JUDO Wasseraufbereitung GmbH

**Produkt:**

JUDO i-soft PRO / PRO L

**System:**

Home Assistant

**Schnittstelle:**

Lokale REST-API

> Diese Dokumentation beschreibt eine technische Home-Assistant-Integration für den JUDO i-soft PRO / PRO L.
>
> Sie stellt **keine offizielle Herstellerspezifikation der JUDO Wasseraufbereitung GmbH dar**, sofern keine ausdrückliche Freigabe durch den Hersteller vorliegt.

---

# 24. Änderungsstand

**Letzte Überarbeitung:** 15.09.2026

### Aktueller Stand

- Zentrale Package-Konfiguration
- REST-Sensoren direkt in der Package-Datei
- REST-Befehle direkt in der Package-Datei
- Scripts direkt in der Package-Datei
- automatische Aktualisierung nach UI-Änderungen
- 5-Sekunden-Verzögerung vor der erneuten Abfrage
- zentrale Verwaltung der Geräte-IP
- Unterstützung für i-soft PRO / PRO L
- Salzgewicht und Salzreichweite als getrennte Sensoren
- Prüfung der Entnahmedauer auf den übertragbaren Byte-Bereich

**Status: Testphase**
