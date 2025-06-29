---
title: "DDoS-Angriffstypen und Abwehrstrategien"
linkTitle: "DDoS-Angriffstypen"
slug: "attack-types"
description: "Umfassender Leitfaden zu DDoS-Angriffstypen, ihren Merkmalen und wirksamen Abwehrstrategien"
translationKey: "attack-types"
weight: 1
layout: docs
type: docs
url: "/de/blog/ddos-schutz/angriffstypen"

---

{{< callout type="warning" >}}
**Kritische Sicherheitsbedrohung**: DDoS-Angriffe können erhebliche Ausfallzeiten, finanzielle Verluste und Schäden am Ruf Ihrer Organisation verursachen. Das Verständnis der Angriffstypen ist der erste Schritt zur wirksamen Abwehr.
{{< /callout >}}

Dieser umfassende Leitfaden untersucht die verschiedenen Arten von Distributed Denial of Service (DDoS)-Angriffen, mit denen Organisationen heute konfrontiert sind. Wir werden Angriffsmerkmale, Erkennungsmethoden und bewährte Abwehrstrategien für jede Kategorie untersuchen.

## DDoS-Angriffe verstehen

DDoS-Angriffe zielen darauf ab, Zielsysteme mit bösartigem Datenverkehr zu überlasten und sie für legitime Benutzer unzugänglich zu machen. Diese Angriffe können basierend auf ihrem Ansatz und Ziel in drei Haupttypen kategorisiert werden.

{{< callout type="info" >}}
**Angriffsentwicklung**: Moderne DDoS-Angriffe kombinieren oft mehrere Angriffsvektoren, was sie schwieriger zu erkennen und abzuwehren macht. Eine umfassende Verteidigungsstrategie muss alle Angriffstypen berücksichtigen.
{{< /callout >}}

## Volumenbasierte Angriffe

Volumenbasierte Angriffe überfluten das Ziel mit massiven Datenverkehrsmengen und überlasten die Netzwerkbandbreite und Infrastrukturkapazität.

### Häufige Volumenangriffstypen

{{< tabs >}}
{{< tab title="UDP-Flood" >}}
**Beschreibung**: Angreifer senden große Mengen von UDP-Paketen an zufällige Ports des Zielsystems und zwingen es, mit ICMP "Ziel nicht erreichbar"-Nachrichten zu antworten.

**Merkmale**:
- Hoher Bandbreitenverbrauch
- Zielrichtung auf Netzwerkinfrastruktur
- Schwierig zu filtern ohne Auswirkungen auf legitimen Datenverkehr

**Abwehr**:
- Ratenbegrenzung für UDP-Datenverkehr
- Datenverkehrsbereinigungsdienste
- DDoS-Schutzgeräte
{{< /tab >}}

{{< tab title="ICMP-Flood" >}}
**Beschreibung**: Angreifer überlasten das Ziel mit ICMP-Echo-Anfrage (Ping)-Paketen und verbrauchen Netzwerkressourcen und Bandbreite.

**Merkmale**:
- Einfach auszuführen
- Hohes Verstärkungspotenzial
- Zielrichtung auf Netzwerkebene

**Abwehr**:
- ICMP-Ratenbegrenzung
- Firewall-Regeln zum Blockieren von ICMP
- Netzwerküberwachung für ungewöhnliche ICMP-Muster
{{< /tab >}}

{{< tab title="DNS-Amplifikation" >}}
**Beschreibung**: Angreifer nutzen DNS-Server aus, um Angriffsdatenverkehr zu verstärken, indem sie kleine Abfragen senden, die große Antworten an das Ziel generieren.

**Merkmale**:
- Hohes Verstärkungsverhältnis (bis zu 70:1)
- Verwendet legitime DNS-Infrastruktur
- Schwierig zur Quelle zurückzuverfolgen

**Abwehr**:
- DNS-Antwort-Ratenbegrenzung
- Anycast-DNS-Bereitstellung
- Quell-IP-Validierung
{{< /tab >}}
{{< /tabs >}}

### Erkennung von Volumenangriffen

```bash
# Netzwerkbandbreitenauslastung überwachen
netstat -i

# Auf ungewöhnliche Datenverkehrsmuster prüfen
tcpdump -i eth0 -w capture.pcap

# Datenverkehrsflüsse analysieren
iftop -i eth0

# Systemressourcen überwachen
top -p $(pgrep -d',' -f "network|http")
```

{{< callout type="warning" >}}
**Erkennungsherausforderung**: Volumenangriffe können schwer von legitimen Datenverkehrsspitzen zu unterscheiden sein. Implementieren Sie Basisüberwachung, um normale Datenverkehrsmuster zu etablieren.
{{< /callout >}}

## Protokollangriffe

Protokollangriffe nutzen Schwachstellen in Netzwerkprotokollen aus und verbrauchen Serverressourcen anstatt Bandbreite.

### Häufige Protokollangriffstypen

{{< cards >}}
{{< card title="SYN-Flood" icon="exclamation" content="Überlastet Server mit TCP-SYN-Paketen und hinterlässt Verbindungen im halboffenen Zustand, wodurch Verbindungstabellen erschöpft werden." >}}

{{< card title="ACK-Flood" icon="exclamation" content="Sendet große Mengen von ACK-Paketen, um Verarbeitungsressourcen zu verbrauchen und legitime Verbindungen zu stören." >}}

{{< card title="Fragmentierungsangriffe" icon="exclamation" content="Nutzt IP-Fragmentierung aus, um Sicherheitsmaßnahmen zu umgehen und Reassembly-Puffer zu überlasten." >}}
{{< /cards >}}

### SYN-Flood-Angriffsdetails

SYN-Flood-Angriffe sind besonders gefährlich, da sie den TCP-Handshake-Prozess zum Ziel haben:

```bash
# Beispiel für SYN-Flood-Erkennung
# SYN-Pakete pro Sekunde überwachen
tcpdump -i eth0 'tcp[tcpflags] & tcp-syn != 0' | wc -l

# Auf unvollständige Verbindungen prüfen
netstat -an | grep SYN_RECV | wc -l

# Verbindungstabellenauslastung überwachen
cat /proc/net/sockstat
```

**Abwehrstrategien**:
- SYN-Cookie-Implementierung
- Verbindungsratenbegrenzung
- TCP-Timeout-Optimierung
- Load Balancer-Schutz

{{< callout type="info" >}}
**Protokollschutz**: Implementieren Sie ordnungsgemäße TCP/IP-Stack-Härtung und verwenden Sie SYN-Cookies, um SYN-Flood-Angriffe daran zu hindern, Serverressourcen zu verbrauchen.
{{< /callout >}}

## Anwendungsebenenangriffe

Anwendungsebenenangriffe zielen auf spezifische Anwendungen und Dienste ab und sind schwieriger zu erkennen und abzuwehren.

### HTTP-basierte Angriffe

{{< tabs >}}
{{< tab title="HTTP-Flood" >}}
**Beschreibung**: Überlastet Webserver mit legitim aussehenden HTTP-Anfragen und verbraucht Anwendungsressourcen.

**Typen**:
- GET/POST-Floods
- Cache-Busting-Angriffe
- Session-Erschöpfung

**Erkennung**:
- Anfrage-Raten pro IP überwachen
- User-Agent-Muster analysieren
- Session-Erstellungsraten verfolgen
{{< /tab >}}

{{< tab title="Slowloris" >}}
**Beschreibung**: Hält viele Verbindungen zum Zielserver aufrecht und sendet teilweise HTTP-Anfragen, um Verbindungen am Leben zu halten.

**Merkmale**:
- Geringer Bandbreitenverbrauch
- Schwierig zu erkennen
- Zielrichtung auf Verbindungslimits

**Abwehr**:
- Verbindungs-Timeout-Einstellungen
- Anfrage-Ratenbegrenzung
- Load Balancer-Schutz
{{< /tab >}}

{{< tab title="SSL/TLS-Angriffe" >}}
**Beschreibung**: Nutzt den SSL/TLS-Handshake-Prozess aus, um Server-CPU-Ressourcen durch kryptographische Operationen zu verbrauchen.

**Typen**:
- SSL-Renegotiation-Angriffe
- TLS-Handshake-Floods
- Zertifikatsvalidierungsangriffe

**Abwehr**:
- SSL-Offloading
- Verbindungsratenbegrenzung
- Hardware-Beschleunigung
{{< /tab >}}
{{< /tabs >}}

### Erkennung von Anwendungsangriffen

```bash
# HTTP-Anfrage-Raten überwachen
tail -f /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c | sort -nr

# Auf langsame Verbindungen prüfen
netstat -an | grep ESTABLISHED | awk '{print $5}' | cut -d: -f1 | sort | uniq -c

# SSL-Handshake-Leistung überwachen
openssl s_time -connect target:443 -new -time 10
```

## Erweiterte Angriffsvektoren

Moderne DDoS-Angriffe kombinieren oft mehrere Techniken für maximale Wirkung.

### Multi-Vektor-Angriffe

{{< details title="Erweiterte Angriffsmuster" >}}

**Reflexionsangriffe**:
- DNS-Reflexion
- NTP-Verstärkung
- SNMP-Verstärkung
- SSDP-Verstärkung

**Botnet-basierte Angriffe**:
- IoT-Geräteausnutzung
- Kompromittierte Server
- Residential-Proxy-Netzwerke
- Cloud-Service-Missbrauch

**Zielgerichtete Anwendungsangriffe**:
- API-Endpunkt-Flooding
- Datenbankverbindungserschöpfung
- Authentifizierungsdienstangriffe
- CDN-Umgehungstechniken

{{< /details >}}

## Angriffserkennung und Überwachung

Wirksamer DDoS-Schutz erfordert umfassende Überwachungs- und Erkennungsfähigkeiten.

### Überwachungsmetriken

{{< cards >}}
{{< card title="Netzwerkmetriken" icon="eye" content="Bandbreitenauslastung, Paketraten, Verbindungsanzahlen und Datenverkehrsmuster über alle Netzwerkschnittstellen." >}}

{{< card title="Anwendungsmetriken" icon="search" content="Anfrage-Raten, Antwortzeiten, Fehlerraten und Ressourcenauslastung für Webanwendungen und Dienste." >}}

{{< card title="Infrastrukturmetriken" icon="cog" content="CPU-Auslastung, Speicherverbrauch, Festplatten-I/O und Systemressourcenverfügbarkeit." >}}
{{< /cards >}}

### Erkennungstools und -techniken

```bash
# Netzwerkdatenverkehrsanalyse
# Bandbreitenauslastung überwachen
iftop -i eth0 -t

# Paketflüsse analysieren
tcpdump -i eth0 -w capture.pcap -c 1000

# Verbindungszustände überwachen
ss -tuln | grep :80

# Anwendungsüberwachung
# Webserver-Logs prüfen
tail -f /var/log/apache2/access.log | grep -E "(404|500|503)"

# Datenbankverbindungen überwachen
mysql -e "SHOW PROCESSLIST;" | grep -v Sleep

# Systemressourcenüberwachung
# CPU- und Speicherauslastung
top -b -n 1

# Netzwerkschnittstellenstatistiken
cat /proc/net/dev
```

{{< callout type="warning" >}}
**Echtzeitüberwachung**: Implementieren Sie automatisierte Überwachung mit Warnschwellen, um Angriffe früh zu erkennen und Auswirkungen zu minimieren.
{{< /callout >}}

## Abwehrstrategien

Eine umfassende DDoS-Schutzstrategie erfordert mehrere Verteidigungsebenen.

### Netzwerkebenen-Schutz

{{< tabs >}}
{{< tab title="Datenverkehrsbereinigung" >}}
**Beschreibung**: Leiten Sie Datenverkehr durch spezialisierte DDoS-Schutzdienste, die bösartigen Datenverkehr filtern.

**Vorteile**:
- Echtzeit-Datenverkehrsanalyse
- Automatische Angriffserkennung
- Minimale Latenzauswirkungen
- 24/7-Schutz

**Implementierung**:
- BGP-Routing zu Bereinigungszentren
- DNS-basierte Datenverkehrsumleitung
- On-Demand-Aktivierung
{{< /tab >}}

{{< tab title="Ratenbegrenzung" >}}
**Beschreibung**: Implementieren Sie Datenverkehrsratenlimits, um die Überlastung von Netzwerk- und Anwendungsressourcen zu verhindern.

**Konfiguration**:
- Verbindungsratenlimits
- Anfrage-Ratenlimits
- Bandbreiten-Drosselung
- Geografische Beschränkungen

**Tools**:
- iptables/nftables
- Load Balancer
- Web Application Firewalls
{{< /tab >}}

{{< tab title="Datenverkehrsfilterung" >}}
**Beschreibung**: Verwenden Sie Firewalls und Filterregeln, um bekannte Angriffsmuster und bösartige Datenverkehrsquellen zu blockieren.

**Techniken**:
- IP-Reputationsfilterung
- Protokollvalidierung
- Payload-Inspektion
- Verhaltensanalyse
{{< /tab >}}
{{< /tabs >}}

### Anwendungsebenen-Schutz

{{< callout type="info" >}}
**Verteidigung in der Tiefe**: Kombinieren Sie Netzwerk- und Anwendungsebenen-Schutz für umfassende DDoS-Abwehr.
{{< /callout >}}

**Web Application Firewalls (WAF)**:
- Anfragefilterung und -validierung
- Ratenbegrenzung und -drosselung
- Bot-Erkennung und -Blockierung
- Benutzerdefinierte Regelerstellung

**Load Balancing**:
- Datenverkehrsverteilung
- Gesundheitsprüfung
- Automatisches Failover
- Geografisches Load Balancing

**CDN-Schutz**:
- Edge-Caching
- Datenverkehrsabsorption
- Geografische Verteilung
- DDoS-Abwehrdienste

## Incident-Response-Plan

{{< details title="DDoS-Incident-Response-Checkliste" >}}

### Vorbereitungsphase
- [ ] Überwachungs- und Warnsysteme einrichten
- [ ] Response-Team-Rollen und -Verantwortlichkeiten definieren
- [ ] Kommunikationsverfahren erstellen
- [ ] Eskalationsprozesse dokumentieren
- [ ] Response-Verfahren regelmäßig testen

### Erkennungsphase
- [ ] Auf ungewöhnliche Datenverkehrsmuster überwachen
- [ ] Systemleistungsmetriken analysieren
- [ ] Angriffstyp und -umfang verifizieren
- [ ] Potenzielle Auswirkungen bewerten
- [ ] Incident-Response-Team aktivieren

### Reaktionsphase
- [ ] Sofortige Abwehrmaßnahmen implementieren
- [ ] DDoS-Schutzdienste aktivieren
- [ ] Mit Stakeholdern kommunizieren
- [ ] Angriffsverlauf überwachen
- [ ] Alle durchgeführten Aktionen dokumentieren

### Wiederherstellungsphase
- [ ] Verifizieren, dass der Angriff beendet ist
- [ ] Schäden und Auswirkungen bewerten
- [ ] Normalbetrieb wiederherstellen
- [ ] Angriffsmuster analysieren
- [ ] Schutzmaßnahmen aktualisieren

### Nach dem Vorfall
- [ ] Vorfallüberprüfung durchführen
- [ ] Response-Verfahren aktualisieren
- [ ] Gelernte Lektionen implementieren
- [ ] Erkenntnisse mit dem Team teilen
- [ ] Dokumentation aktualisieren

{{< /details >}}

## Best Practices

{{< callout type="warning" >}}
**Kontinuierliche Verbesserung**: DDoS-Schutz erfordert laufende Überwachung, Tests und Anpassung an neue Angriffsvektoren.
{{< /callout >}}

### Präventionsstrategien

1. **Netzwerkarchitektur**:
   - Redundante Verbindungen implementieren
   - Mehrere ISPs verwenden
   - Datenverkehrsbereinigungsdienste bereitstellen
   - Ordentliche Ratenbegrenzung konfigurieren

2. **Anwendungssicherheit**:
   - WAF-Schutz verwenden
   - Ordentliche Authentifizierung implementieren
   - Regelmäßige Sicherheitsupdates
   - Anwendungsleistung überwachen

3. **Überwachung und Warnung**:
   - Echtzeit-Datenverkehrsüberwachung
   - Automatisierte Warnsysteme
   - Regelmäßige Penetrationstests
   - Incident-Response-Übungen

4. **Dienstanbieterauswahl**:
   - Anbieter mit DDoS-Schutz wählen
   - Schutzfähigkeiten verifizieren
   - Service Level Agreements verstehen
   - Schutzeffektivität testen

## Nächste Schritte

{{< callout type="warning" >}}
**Proaktiver Schutz**: Warten Sie nicht auf einen Angriff, um DDoS-Schutz zu implementieren. Beginnen Sie mit grundlegenden Maßnahmen und bauen Sie umfassenden Schutz im Laufe der Zeit auf.
{{< /callout >}}

Dieser Leitfaden bietet eine Grundlage für das Verständnis von DDoS-Angriffen und die Implementierung von Schutzmaßnahmen. Für umfassende DDoS-Schutzdienste und Expertenberatung sollten Sie Folgendes in Betracht ziehen:

- **Professionelle DDoS-Schutzdienste**: Verwalteter Schutz mit 24/7-Überwachung
- **Sicherheitsbewertung**: Bewerten Sie Ihre aktuellen Schutzmaßnahmen
- **Incident-Response-Planung**: Entwickeln Sie umfassende Response-Verfahren
- **Mitarbeiterschulung**: Schulen Sie Teams in Angriffserkennung und -reaktion

---

*Für Expertenberatung zu DDoS-Schutz und -implementierung kontaktieren Sie DeepSec für professionelle Beratung, die auf Ihre spezifische Infrastruktur und Anforderungen zugeschnitten ist.* 