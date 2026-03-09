# Engineering Devlog

> Hardware:  
> Raspberry Pi 5 (8GB) · ESP32 · TB6612FNG · YOLOv8 · PETG-Chassis (3D-Druck)

---

## 2026-03-06 – Erste Motorsteuerungsversuche & Architekturentscheidung

**Ziel**
- ESP32 einrichten, erste Motorsteuerung mit TB6612FNG aufbauen
- Systemarchitektur für die Motorsteuerung festlegen.

**Vorgehensweise**
- Arduino IDE installiert, ESP32 DevKit eingerichtet, ersten Testcode geflasht
- Breadboard-Prototyp mit TB6612FNG aufgebaut
- Stromversorgung für ESP32 DevKit über Powerpack (USB-A auf Micro USB)
- Stromversorgung für DC Gear Motors über Powerpack: altes USB-A Kabel aufgeschnitten und direkt mit Breadboard verbunden

<br>
<details>
<summary><h3><strong><u>📸 Fotos anzeigen</u></strong></h3></summary>
<br>
<p align="center">
<img src="assets/schematics/2026-03-06_schaltplan.svg" width="600" alt="Schaltplan erste Version">
<br>
<em>Erste Verkabelungsidee: 5V an VM, alte Pin-Belegung (G25/26/32)</em>
</p>
<p align="center">
<img src="assets/images/2026-03-06_powerpack_front.JPG" width="600" alt="Powerpack Frontansicht">
<br>
<em>Powerpack Frontalansicht — AC-Ausgang 200W + 2 x USB-A Port (5V/3A) </em>
</p>
<br>
<p align="center">
<img src="assets/images/2026-03-06_powerpack_side.jpeg" width="600" alt="Powerpack Seitenansicht">
<br>
<em>Powerpack Seitenansicht — USB-C Port (5-20V/3A)</em>
</p>
<p align="center">
<img src="assets/images/2026-03-06_usb_kabel_aufgeschnitten.png" width="400" alt="USB-A Kabel aufgeschnitten">
<br>
<em>USB-A Kabel aufgeschnitten: rot = 5V, schwarz = GND — direkt ans Breadboard angeschlossen</em>
</p>
<br>

</details>

<br>

**Probleme**
- Der Motor ließ sich nicht ansteuern
  
  > Vermutung: Entweder liegt ein Fehler in der Verkabelung des Motortreibers vor, oder die gewünschte Spannung wird erst nach einer entsprechenden Anfrage über USB Power Delivery bereitgestellt
- Powerpack liefert über USB-C PD erst nach expliziter Spannungsanforderung die gewünschte Spannung – kein automatischer 12-V-Output

**Lösungen**
- Referenz für die Verkabelung des TB6612FNG-Motortreibers auf GitHub gesucht
- Mehrere YouTube Tutorials zur TB6612FNG Verkabelung angesehen  
- USB-C PD Trigger Kabel bestellt (fordert automatisch 15V an → Step-Down auf 12V für Motoren)
- Expansion Board für das ESP32 DevKit bestellt, um das Breadboard zu ersetzen
- Für den Fall, dass weitere Sensoren angeschlossen werden sollen, wurde ein zweites ESP32 DevKit bestellt

- Architekturentscheidung: Motorsteuerung läuft auf ESP32 DevKit, nicht direkt auf Raspberry Pi
  - Begründung: Mikrocontroller reagiert deterministischer als Linux-System bei zeitkritischer Motorsteuerung
  - ESP32 kann Arduino-Sensoren direkt anbinden
  - Raspberry Pi bleibt für Bildverarbeitung und YOLOv8 zuständig


**Erkenntnis**
- USB-C PD verhält sich nicht wie eine einfache Spannungsquelle — Protokoll-Handshake notwendig
- Klare Aufgabentrennung ESP32/Pi von Anfang an sinnvoll

**Ausblick**
- PD Trigger Kabel + Expansion Board abwarten
- Motor-Test mit sauberer Verkabelung wiederholen

---


## 2026-03-07 – Räder, Clip-System & erste Robotergehäuse-Konzepte

**Ziel**
- Räder für 3D-Druck erstellen 
- Erste Iterationen eines Clip-Systems für Radkappen entwickeln  
- Start der Überlegungen für das Robotergehäuse
  

**Probleme**
- Vertikal gedruckte Clips zeigen eine schwache Layerhaftung → Belastung quer zur Layer-Richtung führt zu Bruchanfälligkeit
- Gehäuseerstellung schwierig, da Endgröße, Motorposition, Sensoranzahl, Powerpack-Layout noch unklar
  
**Lösungen**
- Gridfinity Generator genutzt, um standardisierte Gridfinity Baseplate (248×248 mm) zu erzeugen  
- Erste Idee: Baseplate einfach eine komplette Platte drucken, darauf Stützen für Wände platzieren und darauf Gridfinity Baseplate befestigen
- Grund für modularen Ansatz: Basis-Module sollen austauschbar bleiben → spätere Änderungen bei Motoren, Sensorik oder Powerpack möglich  
- Grobe Überlegung: Wände über seitliches Schienensystem von oben einsetzbar machen, Deckel fixiert die Wände  
- Deckel als obere Abdeckung vorgesehen 
  
**Vorgehensweise**
- CAD Modelle erstellen und zusammensetzen → Schauen ob Vorstellungen funktionieren
- Strategie: 3D-Druck parallel zur Entwicklung laufen lassen, um möglichst viele Iterationen früh testen zu können  

**Ausblick**
- Überlegen, wie Wände, Stützen und Deckel stabil umgesetzt werden können  
- Druckgrößen und Toleranzen prüfen, erste CAD-Tests der Basis-Module durchführen
  
## 2026-03-08 – Robotergehäuse-Design & Baseplate Pain-Points

**Ziel**
- Weiterentwicklung des Gehäuse-Konzepts für den Roboter (Robofinity)  
- Fokus: austauschbare Gridfinity-Baseplate, einfache Wartung, flexible Anpassungen

**Vorgehensweise**
- CAD-Design der Baseplate mit ersten Corner- und Middle-Pieces begonnen  
- Gridfinity-Baseplate austauschbar/herausnehmbar gestalten, um Zugriff auf Elektronik und Sensoren zu erleichtern  
- Erste Konzepte im Kopf: Gridfinity-Baseplate fest montieren, aber gleichzeitig Zugang zum Boden für Sensoren ermöglichen  

**Probleme**
- Corner/Middle-Pieces sind fest mit Baseplate verbunden → jede Höhenänderung erfordert Neudruck
- Sensoren am Boden (z. B. IR-Sensor gegen Abstürze) → Löcher an richtiger Stelle nötig → hoher Filamentverbrauch bei Anpassungen
- Herausforderung: Baseplate modular genug halten, um spätere Änderungen an Höhe, Sensorik, Motoranordnung oder Roboterfläche zu ermöglichen  

**Lösungen**
- Corner/Middle-Pieces an und ab montierbar machen → Höhe des Gehäuses häng von diesen ab → Baseplate muss nicht dafür neu gedruckt werden 
  
**Erkenntnisse**
- Modularer Ansatz notwendig: Gridfinity-Baseplate abnehmbar, Corner/Middle-Pieces fix → erleichtert Wartung und iterative Anpassungen  
- Stabilität bleibt Priorität, daher keine komplett austauschbaren Stützen  

**Ausblick**
- Konzept für Corner/Middle-Pieces und Gridfinity-Baseplate weiter konkretisieren  
- Überlegen, wie Höhenanpassungen und Sensorpositionen flexibel umgesetzt werden können  
- Erste CAD-Tests der Basis-Module durchführen

## 2026-03-09 – Hybrid-Lösung: DIN-Schienen + Gridfinity

**Ziel**
- Befestigung austauschbarer Gridfinity-Tiles an Wänden, die weiterhin im Gridfinity-System verwendet werden können  
- Modularität für spätere Anpassungen an Höhe, Sensorik oder Motoren  

**Vorgehensweise**
- Entscheidung für DIN-Schienen für die Wände getroffen  
  - Andere Systeme (T-Schienen, Gitter an Wänden) geprüft und verworfen  
- Corner- und Middle-Pieces der Baseplate weiter angepasst, iterativ verbessert  
- PETG als Material gewählt für Stabilität und UV-Resistenz  
- Überlegung: Gridfinity-Baseplate austauschbar halten, Fixierung über Heat Inserts direkt an Baseplate vorgesehen  
- Heat Inserts bestellt, um Befestigungen sauber und wiederholbar zu realisieren  
- Idee: Gridfinity-Tile mit Wand (DIN-Schiene) verbinden → erste Hybrid-Struktur konzipiert  

**Probleme**
- Befestigung der Wände stabil und gleichzeitig modular gestalten  
- Zugriff auf Elektronik weiterhin einfach über austauschbare Gridfinity-Baseplate; Befestigung an der Wand möglich  
- Höhe, Sensorposition und Motorplatzierung noch nicht final → Konzept muss flexibel bleiben  
- PETG haftet zu stark an Druckbett

**Lösungen**
- Klebestift auf Druckbett genutzt, damit PETG nicht zu stark haftet  

**Erkenntnisse**
- DIN-Schienen-Lösung pragmatisch, einfach umzusetzen und stabil  
- Lösung bietet hohe Stabilität; maximale Höhe der Schiene für Befestigung bleibt bei ca. 3 mm, ausreichend für sichere Fixierung  
- Konzept zeigt, wie Modularität und Stabilität kombiniert werden können  

**Ausblick**
- CAD-Umsetzung der Hybrid-Struktur starten  
- Druck- und Toleranztests vorbereiten  
- Heat Inserts in CAD einplanen, Fixierung der Wände testen

