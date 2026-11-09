# FSD-TBR-001 – ESP32-S31 Thread Border Router

- Status: Entwurf / Spike-Baseline
- Version: 0.1
- Zugehörige System-FSD: [FSD-001](https://github.com/mcuw/physis-x-system-manager/blob/main/documents/FSD-001-system.md)

## 1. Zweck und Umfang

Diese Spezifikation beschreibt die TBR-Firmware auf dem ESP32-S31 Function Coreboard-1. Der TBR stellt die Thread-Netzwerkinfrastruktur und das IPv6-Border-Routing für Physis-X-Nodes bereit.

Der TBR ist ein separates Repository und eine eigenständige Firmware. Geräteverwaltung, fachliche Orchestrierung und Simulation gehören nicht zum TBR.

## 2. Hardware- und Softwarebasis

- Plattform: ESP32-S31 Function Coreboard-1.
- Ausgangsbasis: [esp32-thread-br](https://github.com/espressif/esp32-thread-br).
- ESP-IDF: Version/Commit ist verbindlich zu pinnen und zu dokumentieren.
- PHY-/Ethernet-Konfiguration, Reset-/MDC-/MDIO-Pins, Radioanbindung und Boardrevision sind in der Hardware-Baseline festzuhalten.
- Vorhandene Konfiguration enthält u. a. PHY-/Ethernet-Optionen und native Thread-Radio-Konfiguration; diese sind am realen Board zu verifizieren.
- Flash-/Partitionseinstellungen sind auf Konsistenz zu prüfen. Insbesondere müssen konfigurierte Flashgröße und Partitionstabelle übereinstimmen.

## 3. Funktionale Anforderungen

- TBR-F-001: Firmware MUSS für das S31-Board reproduzierbar bauen.
- TBR-F-002: TBR MUSS das konfigurierte Thread-Netz initialisieren und den Border-Router-Betrieb starten.
- TBR-F-003: Netzwerkdataset und erforderliche Parameter MÜSSEN nach definiertem Verfahren persistent verwaltet werden.
- TBR-F-004: Der TBR MUSS einen dokumentierten Netzwerk-Lifecycle einschließlich Autostart und Fehlerbehandlung besitzen.
- TBR-F-005: Border-Routing zwischen Thread-IPv6 und Upstream-Netz MUSS nachgewiesen werden.
- TBR-F-006: TBR MUSS das vorgesehene Commissioning/Join-Verfahren unterstützen.
- TBR-F-007: Nach Neustart MUSS der TBR in einen definierten Netzwerkzustand zurückkehren.
- TBR-F-008: Ein C6-Node MUSS dem TBR-Netz beitreten und über IPv6 erreichbar sein.
- TBR-F-009: Netzwerk- und Radiofehler MÜSSEN diagnostizierbar sein.
- TBR-F-010: TBR MUSS seine Netzwerkrolle klar von Node- und System-Manager-Funktionen trennen.

## 4. Schnittstellen und Verantwortungsgrenzen

### 4.1 Thread-Seite

- Thread-Netzwerk mit IPv6.
- Commissioning/Joiner entsprechend der gewählten OpenThread-Konfiguration.
- Routing und Erreichbarkeit der Thread-Nodes.
- Dataset-/Credential-Verwaltung gemäß Sicherheitskonzept.

### 4.2 Upstream-Seite

Der Upstream kann je nach Boardkonfiguration über Ethernet oder eine andere dokumentierte IP-Verbindung erfolgen. Die konkrete Topologie ist im Spike festzulegen. Die Konfiguration darf nicht stillschweigend voraussetzen, dass ein Smartphone-Hotspot direkt Ethernet bereitstellt; erforderliche Adapter/Bridges sind zu spezifizieren.

### 4.3 Managementgrenze

Der TBR stellt Netzwerktransport bereit. Geräteinventar, Capability-Modell, Telemetrieverwaltung und fachliche Kommandos sind Aufgaben des Nodes bzw. System Managers.

## 5. Laufzeit und Recovery

Zu spezifizieren und zu testen:
- Startreihenfolge von NVS, Event Loop, Netif, PHY/Upstream, OpenThread und Border-Router-Komponenten.
- Verhalten bei fehlendem Upstream.
- Verhalten bei Thread-Radio-/Dataset-Fehlern.
- Persistenz und Wiederherstellung des Thread-Datasets.
- Neustart und Wiederanlauf nach Stromunterbrechung.
- Erreichbarkeit bestehender Nodes nach TBR-Neustart.
- Diagnose ohne Ausgabe vertraulicher Credentials.

## 6. Spike-Arbeitspakete und Abhängigkeiten

1. TBR-1 Reproduzierbarer S31-Build: IDF, Buildflags, Partitionen, Flashgröße, PHY, GPIO, Radio und Lizenzhinweise verifizieren.
2. TBR-2 Border Routing/Lifecycle: Dataset, Autostart, Prefixe, Routen, IPv6-Forwarding, Upstream und Recovery.
3. TBR-3 Node-Interop/Managementgrenze: C6 Join, IPv6/CoAP-Erreichbarkeit und Verantwortungsabgrenzung.

TBR-3 hängt von TBR-1/TBR-2 und dem Node-C6-Spike ab.

## 7. Abnahmetests

| ID | Test | Kriterium |
|---|---|---|
| TBR-AT-001 | Clean Build | S31-Firmware baut mit dokumentierter IDF-Version reproduzierbar |
| TBR-AT-002 | Hardware Boot | Board startet stabil; PHY/Radio werden korrekt initialisiert |
| TBR-AT-003 | Flash Consistency | Flashgröße und Partitionstabelle sind konsistent und verifiziert |
| TBR-AT-004 | Thread Start | Border-Router- und Thread-Netzwerkzustand sind diagnostizierbar |
| TBR-AT-005 | Dataset Persistence | Definierte Neustarts erhalten den vorgesehenen Netzwerkkontext |
| TBR-AT-006 | IPv6 Routing | IPv6-Erreichbarkeit zwischen Thread-Node und Upstream ist nachgewiesen |
| TBR-AT-007 | C6 Interop | ESP32-C6 joint und ist über den vorgesehenen Pfad erreichbar |
| TBR-AT-008 | Recovery | TBR und Node erholen sich nach Strom-/Uplink-Unterbrechung gemäß Spezifikation |

## 8. Offene Architekturentscheidungen

- Verbindliche S31-Boardrevision und PHY.
- Upstream-Transport und konkrete Netzwerktopologie.
- Native Radio- oder Spinel-Konfiguration.
- Dataset-Erzeugung, Provisionierung, Rotation und Backup.
- Commissioning-Fenster und Zugriffsschutz.
- IPv6-Prefix- und Route-Strategie.
- OTA-/Rollback- und Recovery-Strategie.
- Konkrete Diagnose-/Managementschnittstelle des TBR.

## 9. Traceability

Systemanforderungen: SYS-F-010 bis SYS-F-013, SYS-S-001 und SYS-S-002. TBR-spezifische Anforderungen werden nach dem S31-Hardware- und Interop-Spike finalisiert.
