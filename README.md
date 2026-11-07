# Physis-X Thread Border Router (TBR)

## Zweck und Rolle

Dieses Repository enthält die eigenständige Firmware des Physis-X Thread Border Routers auf dem **ESP32-S31 Function Coreboard-1**. Der TBR verbindet das Thread-Netz mit dem Upstream-IP-Netz (zunächst Ethernet/WLAN entsprechend der verifizierten Hardwarekonfiguration) und stellt Netzwerk-/Commissioning-Funktionen bereit.

Technische Ausgangsbasis ist [mcuw/esp32-thread-br](https://github.com/mcuw/esp32-thread-br). TBR bleibt ein eigenständiges Repository und Firmware-Artefakt. Es wird weder in den System Manager eingebettet noch mit der C6-Node-Firmware vermischt.

## Ziele des Spikes

1. Den vorhandenen Border-Router-Code auf dem S31 reproduzierbar bauen und flashen.
2. Ethernet-/PHY-, Radio- und OpenThread-Konfiguration gegen das reale Board verifizieren.
3. Thread-Netzwerk, IPv6-Routing, Dataset-Persistenz und Recovery nachweisen.
4. Interoperabilität mit dem ESP32-C6-Node und die Verwaltungsgrenze zum System Manager definieren.

## Nicht-Ziele

- Keine gerätespezifischen Actor-APIs für C6-Nodes.
- Keine zentrale Geräteverwaltung oder Simulation im TBR.
- Keine ungeprüfte Übernahme der S31-spezifischen Konfiguration auf andere ESP32-Targets.
- Kein Ersatz für die FSD durch diese Spike-Beschreibung.

## Spike-Plan und Issues

| Reihenfolge | Issue | Arbeitspaket | Abnahmekriterien |
|---|---|---|---|
| 1 | [TBR-1 – Reproduzierbarer S31-Build](https://github.com/mcuw/physis-x-tbr/issues/1) | Quellbaum, IDF-Commit, Dependencies, PHY/GPIO, Radio-Anbindung, Partitionen und Flash-Konfiguration prüfen. | Clean checkout baut mit festgelegtem IDF-Commit; S31 startet; Ethernet-Link und Thread-Radio sind separat nachgewiesen; Konfiguration und Logs sind dokumentiert. |
| 2 | [TBR-2 – Border Routing und Lifecycle](https://github.com/mcuw/physis-x-tbr/issues/2) | Dataset-Erzeugung/Persistenz, Network Autostart, Prefix-/Route-Verteilung, IPv6-Forwarding, Upstream-Ausfall und Recovery testen. | Thread-Netz startet mit persistiertem Dataset; IPv6-Routing ist in den vorgesehenen Richtungen nachgewiesen; Neustart-/Ausfallverhalten ist reproduzierbar dokumentiert. |
| 3 | [TBR-3 – Node-Interop und Managementgrenze](https://github.com/mcuw/physis-x-tbr/issues/3) | C6-Join, CoAP-Erreichbarkeit aus dem Upstream-Netz, Discovery/Adressierung und TBR-Managementgrenze testen. | C6 ist über den TBR erreichbar; CoAP GET/PUT funktioniert Ende-zu-Ende; Neustart-Recovery ist dokumentiert; TBR bleibt unabhängig vom System-Manager-Code. |

## Vorläufige Verantwortungsgrenze

**TBR verantwortet:**
- Thread Border Routing und Upstream-Konnektivität
- Thread-Netzwerk-/Dataset-Lifecycle
- Commissioning-Infrastruktur und netzwerkbezogene Diagnose
- Weitergabe bzw. Erreichbarkeit von IPv6-Routen/Prefixes gemäß OpenThread- und Plattformfähigkeiten

**Node verantwortet:**
- Geräteidentität und Capabilities
- Actor-Funktionen, Gerätestatus und Telemetrie
- Command-Ausführung und Ergebnis

**System Manager verantwortet:**
- Geräteinventar, logische Registrierung und übergreifende Orchestrierung
- Darstellung des Netzwerk-/Erreichbarkeitsstatus
- Simulation und Transportadapter

Die konkrete Management-API des TBR ist offen und wird erst nach dem Interop-Spike festgelegt.

## Abhängigkeiten

- TBR-1 ist Voraussetzung für TBR-2.
- TBR-3 benötigt TBR-1/TBR-2 sowie C6-2/C6-3 aus [physis-x-node-c6](https://github.com/mcuw/physis-x-node-c6).
- Der System Manager liefert mit SM-1 einen vorläufigen logischen Gerätevertrag; er darf keine TBR-Firmware-Abhängigkeit erzeugen.

## Technische Risiken und offene Entscheidungen

- S31-Boardrevision, PHY-Typ, Reset-/MDC-/MDIO-GPIOs und Ethernet-Konfiguration.
- Konsistenz von Partitionstabelle, tatsächlicher Flash-Größe und sdkconfig.
- Native Radio gegenüber UART-/SPI-Spinel: tatsächliche Radio-Hardware und Verbindung verifizieren.
- ESP-IDF-Version und reproduzierbare Pinning-Strategie für Komponenten.
- IPv6-Forwarding, Firewall-/Upstream-Router-Verhalten und DNS/mDNS.
- Persistenz, Backup und sichere Provisionierung des Thread-Datasets.
- Lizenz-/SPDX-Herkunft übernommener Dateien: bestehende Copyright- und Lizenzhinweise erhalten.

## Definition of Done für den Spike

Build-/Flash-Nachweise, Netzwerktopologie, IPv6-/CoAP-Interop-Logs und Recovery-Testfälle sind dokumentiert. Ungeklärte Hardware- und Sicherheitsfragen sind explizit als Risiken festgehalten.

## Repository-Grenze

Dieses Repository enthält ausschließlich TBR-Firmware und TBR-spezifische Betriebs-/Build-Artefakte. Der C6-Node und der System Manager bleiben eigenständige Projekte.
