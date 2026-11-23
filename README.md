# Mein Heimnetzwerk mit Cisco Packet Tracer

## Über das Projekt

In diesem Projekt baue ich mein Heimnetzwerk mit Cisco Packet Tracer nach. Ich wollte besser verstehen, wie die Geräte in meiner Wohnung miteinander verbunden sind und welche Aufgaben ein Router, DHCP und DNS übernehmen.

Als Grundlage habe ich den Grundriss meiner Wohnung und die Geräte aus meinem Haushalt verwendet. Zusätzlich habe ich ein kleines Anbieternetz ergänzt, um nachzustellen, wie mein Heimrouter seine WAN-Adresse vom Internetanbieter erhält.

## Verwendete Software und Packet Tracer Geräte

- Cisco Packet Tracer 8.2.2
- WRT300N als Heimrouter
- Cisco 2911 als simulierter Vodafone Router
- Cisco 2960 als Switch auf der Anbieterseite
- Server-PT als Vodafone DHCP- und DNS-Server
- Server-PT als simulierter Internet Web-Server
- PC-PT für die PCs
- Laptop-PT für den Laptop
- Smartphone-PT und TabletPC-PT für Handys und Tablets
- Generic Wireless als vereinfachtes Modell für TV und Alexa
- Ein zweiter WRT300N als sichtbarer Ersatz für den WLAN Repeater

## Mein Heimnetzwerk

In meinem echten Heimnetzwerk sind ein PC direkt per LAN und ein weiterer PC per LAN mit einem WLAN Repeater verbunden. Der Repeater selbst verbindet sich per WLAN mit dem Hauptrouter. Laptop, Fernseher, Handys, Tablets und Alexa nutzen ebenfalls WLAN.

Der Drucker ist kein Netzwerkgerät. Er ist lokal per Kabel mit dem Laptop verbunden.

Für das WLAN habe ich die SSID `Heimnetz RaphaelFISI` eingerichtet. Als Sicherheit habe ich **WPA2 Personal mit AES** gewählt. WPA2 Personal passt zu einem Heimnetz, in dem kein zentraler Anmeldeserver verwendet wird. AES verschlüsselt die drahtlose Verbindung.

## Umsetzung in Packet Tracer

Zuerst habe ich den Grundriss und die Geräte in der Wohnung platziert. Danach habe ich den WRT300N hinzugefügt und den R-PC per LAN angeschlossen. Anschließend habe ich das WLAN mit WPA2 Personal und AES eingerichtet und die drahtlosen Geräte verbunden.

Für den Anbieterbereich habe ich einen Cisco 2911, einen 2960 Switch und einen Server ergänzt. Der Server stellt DHCP und DNS bereit. Ein weiterer Server simuliert ein Ziel im Internet und stellt HTTP bereit.

### IP-Adressplan

#### Anbieternetz und WAN Verbindung

| Gerät oder Funktion | Adresse oder Einstellung |
|---|---|
| Vodafone Router G0/0 | `10.0.0.1/24` |
| Vodafone DHCP/DNS Server | `10.0.0.2/24` |
| DHCP Pool | `serverPool`, Start `10.0.0.100`, Maske `/24`, maximal 50 Adressen |
| DHCP Gateway | `10.0.0.1` |
| DHCP DNS Server | `10.0.0.2` |
| WRT300N WAN | `10.0.0.100` per DHCP |

#### Simuliertes Internetnetz

| Gerät oder Funktion | Adresse oder Einstellung |
|---|---|
| Vodafone Router G0/1 | `198.51.100.1/24` |
| Internet Web-Server | `198.51.100.10/24`, Gateway `198.51.100.1` |
| DNS Eintrag | `www.heimnetz.test` zeigt auf `198.51.100.10` |

Das Netz `198.51.100.0/24` ist nur für diese Simulation gedacht und keine echte Vodafone Adresse.

#### Heimnetz

Das Heimnetz verwendet `192.168.0.0/24`. Der WRT300N hat die Adresse `192.168.0.1` und vergibt Adressen aus dem Bereich `192.168.0.100–149`.

| Gerät | IP-Adresse |
|---|---|
| TV0 | `192.168.0.101` |
| A-Handy | `192.168.0.102` |
| M-Handy | `192.168.0.104` |
| R-Handy | `192.168.0.105` |
| A-Tablet | `192.168.0.107` |
| R-Tablet | `192.168.0.108` |
| R-PC | `192.168.0.109` |
| M-Laptop | `192.168.0.110` |
| A-PC | `192.168.0.112` |
| Alexa-Küche | `192.168.0.113` |

Die aufgeführten Adressen wurden den Geräten während der Einrichtung per DHCP zugeteilt.

### Wichtige Kabelverbindungen

| Verbindung | Anschlüsse |
|---|---|
| Vodafone Router zum Switch | `GigabitEthernet0/0` zu `FastEthernet0/1` |
| Vodafone DHCP/DNS Server zum Switch | `FastEthernet0` zu `FastEthernet0/2` |
| WRT300N zum Switch | `Internet` zu `FastEthernet0/3` |
| Vodafone Router zum Internet Web-Server | `GigabitEthernet0/1` zu `FastEthernet0` |
| R-PC zum WRT300N | `FastEthernet0` zu `Ethernet 1` |
| A-PC zum WRT300N in Packet Tracer | `FastEthernet0` zu `Ethernet 3` |

## DHCP, DNS und Webserver

Der Vodafone DHCP-Server vergibt dem Internetanschluss des WRT300N die Adresse `10.0.0.100`. Der WRT300N vergibt anschließend eigene Adressen an die Geräte im Heimnetz. So sind Anbieternetz und Heimnetz getrennt.

Auf dem Vodafone Server ist außerdem DNS eingerichtet. Der Name `www.heimnetz.test` verweist auf den simulierten Webserver. Auf dem Internet Web-Server ist HTTP eingeschaltet.

Der vorgegebene DHCP Pool `serverPool` ließ sich in Packet Tracer nicht löschen oder umbenennen. Deshalb habe ich ihn für die Vodafone WAN Adressen verwendet.

## Probleme und Packet Tracer Vereinfachungen

### WLAN Repeater

In echt ist der A-PC per LAN mit dem Repeater verbunden, und der Repeater verbindet sich per WLAN mit dem Hauptrouter. Packet Tracer konnte diese Verbindung mit dem gewählten Ersatz nicht zuverlässig nachbilden. Deshalb ist der A-PC in der Simulation direkt per LAN mit dem Hauptrouter verbunden. Der zweite WRT300N bleibt als sichtbarer Repeater-Ersatz in der Topologie.

### Laptop WLAN

Beim M-Laptop erschien zuerst eine Fehlermeldung, weil ein WLAN Modul fehlte. Ich habe das Ethernet Modul durch `WPC300N` ersetzt. Danach konnte sich der Laptop mit dem WLAN verbinden und per DHCP eine Adresse erhalten.

### TV und Drucker

Das Packet Tracer Modell `TV-PT` hatte keine passende WLAN Schnittstelle. Deshalb verwende ich `Generic Wireless` als TV0. Auch die Alexa-Küche ist als `Generic Wireless` dargestellt. Ihre Sprach- und Cloud-Funktionen werden nicht simuliert.

Der K-Printer bleibt außerhalb des Netzwerks. Packet Tracer bildet seine lokale Kabelverbindung zum Laptop in dieser Topologie nicht ab.

### DNS Einstellung am WRT300N

Beim Speichern der DNS Adresse im WRT300N erschien die Meldung `This IP Address is not allowed for use on a port`. Ich habe das Feld `Static DNS 1` deshalb auf `0.0.0.0` gelassen. Der M-Laptop erhielt trotzdem `10.0.0.2` als DNS Server vom Heimnetz.

## Mein Fazit

Durch das Projekt habe ich mein eigenes Heimnetz besser kennengelernt. Ich konnte nachvollziehen, wie sich die IP Adressen im Heimnetz und im Anbieternetz unterscheiden und wie DHCP und DNS in der Simulation zusammenspielen.

Besonders hilfreich war es, die Geräte Schritt für Schritt einzurichten und Fehler anhand der angezeigten Meldungen zu suchen. Dabei habe ich auch gelernt, dass Packet Tracer nicht jedes Gerät aus einem echten Haushalt genau darstellen kann. Bei Repeater, Fernseher und Drucker musste ich deshalb passende Vereinfachungen verwenden und diese dokumentieren.
