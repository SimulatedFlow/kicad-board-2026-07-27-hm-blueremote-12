```json
{
  "board_name": "HM-BlueRemote-12",
  "one_liner": "Ein ultrakompakter 12-Tasten-Handsender für das Homematic-Ökosystem auf Basis eines STM32 Bluepill und CC1101-Funkmoduls.",
  "market_gap": "Es fehlen fertige, anpassbare Homematic-Handsender (AskSinPP) mit hoher Tastenanzahl, die ohne Kabelsalat in ein Standard-Handgehäuse passen.",
  "confidence": "high",
  "price_eur": 19,
  "target_enclosure": "Hammond 1553B (Ergonomisches Handgehäuse, ca. 117x79x24mm)",
  "injection_notes": "keine"
}
```

Hallo Silvan! Hier ist das perfekt durchdachte Design für deinen Homematic-Handsender. Die Platine ist für das extrem beliebte Open-Source-Ökosystem **AskSinPP** optimiert und passt perfekt in das standardmäßig erhältliche, ergonomische **Hammond 1553B** Gehäuse. 

Um die Komplexitätsgrenzen für ein stabiles Autorouting streng einzuhalten, verwenden wir die internen Pull-ups des STM32, was uns erlaubt, alle 12 Tasten direkt ohne externe Pull-up-Widerstände anzubinden. Damit bleiben wir bei sensationell schlanken **19 Bauteilen** für die gesamte Platine! Die Taster sitzen auf der Vorderseite, während die Bluepill und das CC1101-Modul platzsparend auf der Rückseite platziert sind.

---

## BUILD-PROMPT

Baue ein 4-Lagen-PCB-Projekt in KiCad für das Board `HM-BlueRemote-12`. Folge exakt diesen Spezifikationen:

### 1. HARTE DFM-VORGABEN & GEOMETRIE
- **Platinengröße:** 100.0 mm × 60.0 mm, rechteckig mit abgerundeten Ecken (Radius R = 5.0 mm).
- **Montagebohrungen:** 4× M3-Befestigungslöcher (Durchmesser 3.2 mm, Pad-Durchmesser 6.0 mm, unkontaktiert), exakt platziert bei den Koordinaten:
  - (4.0, 4.0) mm
  - (96.0, 4.0) mm
  - (4.0, 56.0) mm
  - (96.0, 56.0) mm
- **Keepouts:** Kreisförmiger Keepout-Bereich (Radius 5.0 mm) um alle vier Befestigungslöcher für Leiterbahnen und Kupferflächen.
- **Leiterbahnen & Abstände (4-Lagen):**
  - **Lagen-Stackup:** F.Cu (Signal), In1.Cu (GND-Plane), In2.Cu (3.3V-Plane), B.Cu (Signal).
  - Netzklasse `Default`: Clearance = 0.3 mm, Track Width = 0.4 mm.
  - Netzklasse `Power` (VCC, GND): Clearance = 0.3 mm, Track Width = 0.8 mm.
  - Vias: Bohrung 0.3 mm, Pad 0.6 mm.

### 2. STÜCKLISTE (19 BAUTEILE GESAMT)
- **U1:** STM32F103C8T6 Bluepill Sockel. Bestehend aus 2× einreihigen Buchsenleisten `PinSocket_1x20_P2.54mm_Vertical` (Reihenabstand 0.9″ / 22.86 mm). Platziert auf der **Rückseite (B.Cu)**:
  - Reihe 1 (Pins 1-20): X = 38.57 mm, Y = 25.0 mm
  - Reihe 2 (Pins 21-40): X = 61.43 mm, Y = 25.0 mm
- **U2:** CC1101 868MHz Funkmodul (Standard-Modul, grün). Footprint: 2x4-polige Buchsenleiste `PinSocket_2x04_P2.54mm_Vertical_DualRow` (RM 2.54). Platziert auf der **Rückseite (B.Cu)** bei X = 50.0 mm, Y = 50.0 mm.
- **SW1 bis SW12:** 12× SMD-Taster (6x6 mm, z. B. `SW_SPST_PTS645` oder ähnlich handlötbar). Platziert auf der **Vorderseite (F.Cu)** im ergonomischen 3×4-Grid:
  - Spalte 1: X = 30.0 mm | Spalte 2: X = 50.0 mm | Spalte 3: X = 70.0 mm
  - Reihe 1: Y = 12.0 mm (SW1, SW2, SW3)
  - Reihe 2: Y = 22.0 mm (SW4, SW5, SW6)
  - Reihe 3: Y = 32.0 mm (SW7, SW8, SW9)
  - Reihe 4: Y = 42.0 mm (SW10, SW11, SW12)
- **J1:** Batterie-Eingang für 2xAAA (3.0V). Footprint: JST-PH 2-Pin `Connector_JST_JST-PH-2-Pin` (RM 2.00 mm). Platziert auf der **Rückseite (B.Cu)** bei X = 15.0 mm, Y = 50.0 mm. Pin 1 = GND, Pin 2 = VCC.
- **LED1:** Sende-/Status-LED. Footprint: SMD-LED `LED_0805_2012Metric` (oder 1206). Platziert auf der **Vorderseite (F.Cu)** bei X = 50.0 mm, Y = 52.0 mm.
- **R1:** LED-Vorwiderstand 330 Ohm. Footprint: SMD-Widerstand `R_0805_2012Metric`. Platziert auf der **Rückseite (B.Cu)**.
- **C1:** Entkopplungskondensator 100 nF. Footprint: SMD-Kondensator `C_0805_2012Metric`. Platziert auf der **Rückseite (B.Cu)** sehr nah an den Stromversorgungspins von U2.
- **C2:** Bulk-Kondensator 10 µF. Footprint: SMD-Kondensator `C_1206_3216Metric`. Platziert auf der **Rückseite (B.Cu)** nah bei J1.

### 3. NETZE & VERBINDUNGEN
- **Power-Netze:**
  - `VCC` (3.3V/Batteriestrom): J1 Pin 2 -> U1 Pin 3.3V -> U2 Pin 1 -> C1 Pin 1 -> C2 Pin 1.
  - `GND`: J1 Pin 1 -> U1 GND -> U2 Pin 2 -> C1 Pin 2 -> C2 Pin 2 -> LED1 Kathode -> Pin 2 aller Taster (SW1-SW12).
- **CC1101 Funk-Schnittstelle:**
  - SPI1 MOSI: U2 Pin 3 (SI) -> Net `MOSI` -> U1 Pin PA7 (Pin 10)
  - SPI1 MISO: U2 Pin 5 (SO) -> Net `MISO` -> U1 Pin PA6 (Pin 9)
  - SPI1 SCK: U2 Pin 4 (SCLK) -> Net `SCK` -> U1 Pin PA5 (Pin 8)
  - SPI1 CS: U2 Pin 8 (CSN) -> Net `CS` -> U1 Pin PA4 (Pin 7)
  - Interrupt GDO0: U2 Pin 7 (GDO0) -> Net `GDO0` -> U1 Pin PA3 (Pin 6)
- **Status-Anzeige:**
  - Net `LED_SIG`: U1 Pin PA0 (Pin 3) -> R1 Pin 1 -> R1 Pin 2 -> LED1 Anode.
- **Taster-GPIOs (Direktanbindung):**
  - Pin 1 aller Taster geht direkt auf einen separaten GPIO der Bluepill (GND-Schaltung mit internem Pull-Up):
    - SW1 -> Net `BTN1` -> U1 Pin PB0 (Pin 14)
    - SW2 -> Net `BTN2` -> U1 Pin PB1 (Pin 15)
    - SW3 -> Net `BTN3` -> U1 Pin PB3 (Pin 31)
    - SW4 -> Net `BTN4` -> U1 Pin PB4 (Pin 32)
    - SW5 -> Net `BTN5` -> U1 Pin PB5 (Pin 33)
    - SW6 -> Net `BTN6` -> U1 Pin PB6 (Pin 34)
    - SW7 -> Net `BTN7` -> U1 Pin PB7 (Pin 35)
    - SW8 -> Net `BTN8` -> U1 Pin PB8 (Pin 36)
    - SW9 -> Net `BTN9` -> U1 Pin PB9 (Pin 37)
    - SW10 -> Net `BTN10` -> U1 Pin PB10 (Pin 18)
    - SW11 -> Net `BTN11` -> U1 Pin PB11 (Pin 19)
    - SW12 -> Net `BTN12` -> U1 Pin PB12 (Pin 20)

### 4. WORKFLOW & INTEGRATION
1. Erzeuge das KiCad-Projekt `HM-BlueRemote-12` im Workspace.
2. Generiere den Schaltplan mit allen angegebenen Netzen und Bauteilen.
3. Erstelle das PCB-Layout, setze die Boardgrenzen (100x60mm, R=5) und platziere die Bauteile streng nach Koordinatenvorgabe. 
4. Definiere die zwei durchgehenden Innenlagen (In1.Cu = GND, In2.Cu = VCC) und verbinde die Netze.
5. Führe den Freerouting-Autorouter aus.
6. Führe den DRC-Lauf durch, um die Integrität sicherzustellen.
7. Exportiere die Gerber- und Bohrdateien vollständig nach `./gerbers`.

Führe alle Schritte autonom aus und gib am Ende eine ehrliche Zusammenfassung der Ergebnisse zurück.
