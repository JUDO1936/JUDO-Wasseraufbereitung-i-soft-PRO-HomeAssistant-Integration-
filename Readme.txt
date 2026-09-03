================================================================================
          JUDO i-soft PRO / PRO L - Home Assistant Integration
                     Dokumentation & Einrichtungsanleitung
                            für TCP Verschlüsselung 
================================================================================

WICHTIGER HINWEIS:
------------------
Diese Dokumentation beschreibt die bereitgestellte Home-Assistant-Konfiguration 
für die lokale Einbindung einer JUDO i-soft PRO / PRO L über die REST-Schnittstelle.
Diese Integration befindet sich noch in der Testphase!!!

================================================================================
1. LIEFERUMFANG & DATEIÜBERSICHT
================================================================================

Für die vollständige Einrichtung werden folgende Dateien benötigt:

  Datei                          Funktion
  -----------------------------  -----------------------------------------------
  configuration.yaml             Zentrale Home-Assistant-Konfiguration
  rest.yaml                      Auslesen der Sensorwerte vom i-soft Gerät
  rest_commands.yaml             Senden von Befehlen/Aktionen an das Gerät
  scripts.yaml                   Steuerungslogik für die Bedienung
  customize.yaml                 Zuordnung der i-soft-Sensoren zum Gerät
  isoft_pro_control_package.yaml Eingabefelder, Auswahlmöglichkeiten & Skripte
  Dashboard.yaml                 Lovelace-Dashboard zur visuellen Bedienung

WICHTIG: Bereits vorhandene Konfigurationen in Ihrer Home-Assistant-Installation
dürfen nicht überschrieben oder gelöscht werden! Der Inhalt dieser Datei muss 
dann dort hinzugefügt werden.


================================================================================
2. VORBEREITUNGEN an dem i-soft PRO / PRO L
================================================================================

   Man muss bei dem i-soft PRO /PRO L die Rest-API Aktivieren. Das geht wie folgt,
   im Hauptmenü auf Konnektivität klicken. Dann werden Sie in ein weiteres Menü
   geleitet. In diesem müssen Sie auf den Botton REST-API klicken. Jetzt sind Sie 
   in dem entscheidenden Menü. In diesem Menü müssen sie den Schieberegler auf ON 
   stellen. Dann Leuchtet dieser Orange. Daneben ist ein Weiter Schieberegler dort 
   stellen Sie die Verschlüsselung ein. Wenn sie kein Homeguard haben können Sie 
   auch die TCP Verschlüsselung verwenden. Wenn Sie aber einen Homeguard haben müssen
   Sie die SSL Variante benutzen. Diese benötigt aber auch andere Dateien.
   Wenn Sie also SSL Verwenden wollen müssen Sie die Dateien aus dem Ordner SSL
   Verschlüsselung Herunterladen. Dann als letzter Schritt müssen Sie die Einstellungen
   mit der Taste OK bestätigen und Speichern.
   

================================================================================
3. VORAUSSETZUNGEN
================================================================================

  [x] JUDO i-soft PRO oder i-soft PRO L
  [x] Lauffähige Home Assistant Installation
  [x] Aktive Netzwerkverbindung zwischen Home Assistant und dem i-soft Gerät
  [x] Die mitgelieferten YAML-Dateien
  [x] Lokale IP-Adresse des i-soft Geräts (muss von HA erreichbar sein)

  Beispiel:
    i-soft PRO IP-Adresse: 192.168.2.38


================================================================================
4. CONFIGURATION.YAML EINRICHTEN
================================================================================

Die bestehende configuration.yaml darf NICHT vollständig ersetzt werden. 
Fügen Sie die folgenden Strukturen in Ihre vorhandene Datei ein bzw. ergänzen
Sie diese entsprechend:

--------------------------------------------------------------------------------
default_config:

input_text:
  isoft_ip:
    name: i-soft IP-Adresse
    initial: "192.168.2.38"
    min: 7
    max: 15
    mode: text

homeassistant:
  packages: !include_dir_named packages

rest: !include rest.yaml
rest_command: !include rest_commands.yaml
script: !include scripts.yaml

template:
  - sensor:
      - name: "i-soft PRO Status"
        unique_id: isoft_pro_status_clean
        state: "Online"
        icon: mdi:water-softener
--------------------------------------------------------------------------------

HINWEIS: Achten Sie darauf, dass `default_config:` nicht versehentlich entfernt 
wird, da dies grundlegende Home-Assistant-Funktionen bereitstellt.


================================================================================
5. ZENTRALE IP-ADRESSE KONFIGURIEREN
================================================================================

Die IP-Adresse des i-soft-Geräts wird zentral über den Home-Assistant-Helper 
`input_text.isoft_ip` verwaltet.

IP-Adresse ändern:
  Wenn sich die IP-Adresse des i-soft-Geräts ändert, passen Sie einfach den Wert
  unter `initial` in der `configuration.yaml` (oder via HA-Entität) an:

  Beispiel:
    initial: "192.168.2.50"

Vorteil: Die Dateien `rest.yaml` und `rest_commands.yaml` greifen automatisch auf 
diesen Helper zu. Es ist nicht nötig, jede einzelne REST-URL manuell anzupassen.


================================================================================
6. REST-API-SCHNITTSTELLE & COMMANDS
================================================================================

- rest.yaml: 
  Enthält Abfragen zum fortlaufenden Auslesen der Gerätedaten.
- rest_commands.yaml: 
  Enthält die Steuerbefehle zum Schreiben / Ausführen von Aktionen am Gerät.

Beide Dateien nutzen dynamisch die zentrale IP-Adresse über Pfade wie:
  http://<IP-Adresse>/api/rest/...   (z. B. http://192.168.2.38/api/rest/5100)


================================================================================
7. ISOFT_PRO_CONTROL_PACKAGE.YAML
================================================================================

Speichern Sie diese Datei im Unterordner `/config/packages/`. Sie stellt folgende
Funktionen und Steuerungen zur Verfügung:

  - Wunschwasserhärte & Härteeinheit
  - Szenenauswahl & Szenendauer
  - Maximale Entnahmedauer, Entnahmemenge & maximaler Volumenstrom
  - Urlaubstage & Urlaubsmodus
  - Manuelle Regeneration starten
  - Szene aktivieren
  - Leckageschutz öffnen / schließen


================================================================================
8. CUSTOMIZE.YAML
================================================================================

Die `customize.yaml` ordnet die folgenden Sensoren und Entitäten sauber dem 
Gerät "i-soft PRO" in der Home-Assistant-Benutzeroberfläche zu:

  - Wunschwasserhärte, Härteeinheit
  - Salzgewicht, Salzreichweite & Salzmangel-Warnschwelle
  - Max. Entnahmedauer, Entnahmemenge & Volumenstrom
  - Seriennummer & Firmware-Version
  - Gesamtwassermenge & Weichwassermenge


================================================================================
9. DASHBOARD EINRICHTEN (LOVELACE)
================================================================================

Die Datei `Dashboard.yaml` bietet eine vorgefertigte Benutzeroberfläche mit 
Bereichen für:
  1. Anlage- & Infodaten (Seriennummer, Firmware, Status)
  2. Szenen & Manuelle Regeneration
  3. Leckageschutz & Urlaubsmodus
  4. Wasserhärte-Einstellungen
  5. Grenzwerte für den Leckageschutz
  6. Salzvorrat & Warnschwellen

Schritte zum manuellen Einfügen (Raw-Konfigurationseditor):
  1. Home Assistant öffnen -> Zum gewünschten Dashboard wechseln.
  2. Oben rechts das Menü öffnen -> "Dashboard bearbeiten".
  3. Erneut das Menü oben rechts öffnen -> "Raw-Konfigurationseditor".
  4. WICHTIG: Sichere zuerst den vorhandenen Inhalt!
  5. Den Inhalt aus `Dashboard.yaml` einfügen/zusammenführen.
     Der Code beginnt mit:
       views:
         - title: i-soft PRO
           path: isoft-pro
           icon: mdi:water-softener
  6. Speichern und Seite neu laden.


================================================================================
10. DATEISTRUKTUR IN HOME ASSISTANT
================================================================================

Eine typische Verzeichnisstruktur sieht wie folgt aus:

/config/
├── configuration.yaml
├── rest.yaml
├── rest_commands.yaml
├── scripts.yaml
├── customize.yaml
└── packages/
    └── isoft_pro_control_package.yaml


================================================================================
11. ERSTE SCHRITTE & INBETRIEBNAHME
================================================================================

  1. Korrekte IP-Adresse in `configuration.yaml` prüfen.
  2. Prüfen, ob alle `!include`-Pfade zur Dateistruktur passen.
  3. Developer Tools -> Konfiguration prüfen (YAML-Syntax-Check).
  4. Home Assistant neu starten.
  5. Das i-soft Dashboard öffnen.
  6. Kontrollieren, ob Sensorwerte (z. B. Wasserverbrauch, Salzstand) gelesen werden.
  7. Ggf Sensoren auswählen nach Vorgabe
  8. Erst nach erfolgreicher Datenanzeige erste Steuerbefehle testen.


================================================================================
12. FEHLERBEHEBUNG (TROUBLESHOOTING)
================================================================================

Keine Werte vom Gerät?
  - Ist die IP-Adresse korrekt und im Netzwerk erreichbar (Ping test)?
  - Ist das Gerät eingeschaltet?
  - Stimmen die `!include`-Pfade in der `configuration.yaml`?
  - Sind Benutzername/Passwort der REST-Schnittstelle korrekt (falls geändert)?

Befehle werden nicht ausgeführt?
  - REST-Befehle und API-Pfade prüfen.
  - Home-Assistant-Logfile (`home-assistant.log`) auf REST-Fehler prüfen.
  - Netzwerkkonnektivität zwischen HA und Gerät prüfen.

Dashboard zeigt Fehler / Entitäten fehlen?
  - Prüfen, ob `isoft_pro_control_package.yaml` im Ordner `/config/packages/` liegt.
  - Prüfen, ob `homeassistant: packages: !include_dir_named packages` geladen ist.
  - Namen der Skripte und Entitäten abgleichen.


================================================================================
13. HINWEISE ZUM HERSTELLER & IMPRESSUM
================================================================================

  Herstellerbezug: JUDO Wasseraufbereitung GmbH
  Produkt:         JUDO i-soft PRO / PRO L
  System:          Home Assistant (Lokale REST-API Integration)

  Letztes Änderungsdatum: 21.08.26

  Hinweis: Diese Datei dient als technische Dokumentation für Anwender der 
  Home-Assistant-Integration. Es ist eine offizielle Herstellerspezifikation 
  der JUDO Wasseraufbereitung GmbH.
================================================================================
