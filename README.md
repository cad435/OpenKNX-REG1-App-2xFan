# REG1-FanAktor-2x (V0.2)

Applikationsplatine (Addon-Board) für das [OpenKNX REG1](https://github.com/OpenKNX/OpenKNX-REG1) Modulsystem zur Ansteuerung von dezentralen Lüftungsgeräten über den KNX-Bus.

## Überblick

Die REG1-FanAktor-2x Platine wird zusammen mit dem [REG1-Controller2040](https://github.com/OpenKNX/OpenKNX-REG1/tree/main/hardware/EDA-Files/REG1-Controller2040) (RP2040-basiert) als Applikationsplatine in einem [REG1-Gehäuse](https://github.com/OpenKNX/OpenKNX/wiki/REG1-Case) (1TE, 17.5mm, DIN-Hutschiene) verbaut.

Die Platine steuert bis zu **2 unabhängige Lüfterausgänge** (Kanal A und B), jeweils mit:
- **PWM-Drehzahlregelung** (Open-Drain, 3.3V -> 5V über Level-Shifter)
- **Leistungsschalter** (High-Side-Switch für Ein/Aus)
- **Tacho-Eingang** (galvanisch isoliert über Optokoppler)

## Unterstützte Lüftertypen

| Konfiguration | Beschreibung |
|---------------|-------------|
| 2x Einzellüfter | z.B. Fawas AirSolitaire 160 -- ein Lüfter pro Kanal, unabhängig steuerbar |
| 1x Push-Pull-Lüfter | z.B. Maico PP B30 -- beide PWM-Kanäle steuern ein Gerät (S1/S2 für Richtungsumkehr) |

Die Konfiguration erfolgt über die ETS-Software (KNX-Parametrierung).

## Anschlüsse

### Lüfterklemmen

| Klemme | Bezeichnung | Funktion |
|--------|-------------|----------|
| **P1** | PWR IN | Lüfter-Versorgungsspannung (12...24V DC), 2-polig (FAN+, GND_FAN) |
| **P2** | FAN A | Lüfterausgang Kanal A, 4-polig (PWM_A_OUT, Tacho_A_IN, GND_FAN, GND_FAN) |
| **P3** | FAN B | Lüfterausgang Kanal B, 4-polig (PWM_B_OUT, Tacho_B_IN, GND_FAN, GND_FAN) |

Klemmentyp: KF2EDGR-3.5 (P1: 2-polig, P2/P3: 4-polig), Rastermaß 3.5mm.

### Controller-Schnittstelle

| Anschluss | Funktion |
|-----------|----------|
| **J1** | Header 1x10, Verbindung zum REG1-Controller2040 (GPIO, Versorgung) |

#### J1 Pinbelegung (vom Controller)

| Pin | Signal | GPIO | Funktion |
|-----|--------|------|----------|
| 1 | 12V | -- | Versorgungsspannung vom Controller |
| 2 | 3V3 | -- | 3.3V Versorgung vom Controller |
| 3 | GND_KNX | -- | Masse |
| 4 | GPIO18 | GP18 | PWM Kanal A |
| 5 | GPIO27 | GP27 | Tacho Kanal A (Eingang) |
| 6 | GPIO28 | GP28 | PWM Kanal B |
| 7 | GPIO29 | GP29 | Power-Switch Kanal A |
| 8 | GPIO26 | GP26 | Power-Switch Kanal B |
| 9 | GPIO17 | GP17 | Tacho Kanal B (Eingang) |
| 10 | GPIO16 | GP16 | Spare (auf J1 herausgeführt, nicht belegt) |

## Schaltungsbeschreibung

### Spannungsversorgung

Die Lüfter werden extern mit 12...24V DC über P1 versorgt.

### PWM-Ausgänge (Kanal A/B)

Jeder Kanal hat einen PWM-Ausgang zur Drehzahlregelung der Lüfter. Die Ausgänge sind galvanisch vom KNX-Bus getrennt ausgeführt.

### Leistungsschalter (Power Ein/Aus)

Jeder Kanal hat einen High-Side-Switch zum An- und Ausschalten der Supply-Spannung für den Lüfter. Dies dient rein zur Eliminierung der Standbyleistung.

### Tacho-Eingänge

Jeder Kanal hat einen galvanisch getrennten Tacho-Eingang.

Typische Lüfter liefern 2 Pulse pro Umdrehung.

### Tacho Pull-Up Konfiguration (CN1...CN6)

Die Pull-Up-Konfiguration für die Tacho-Eingänge wird über Lötbrücken eingestellt:

| Lüfter-Typ | Kanal A | Kanal B | Beschreibung |
|-------------|---------|---------|-------------|
| Interner Pull-Up vorhanden | Keine Brücke | Keine Brücke | Standard, keine externe Beschaltung nötig |
| Pull-Up auf 5V nötig | CN2 brücken | CN5 brücken | 5V-Referenz als Pull-Up |
| Pull-Up auf VCC (12-24V) nötig | CN1 brücken | CN4 brücken | Versorgungsspannung als Pull-Up |
| 5V Push-Pull Eingang | CN3 brücken, R1 entfernen | CN6 brücken, R3 entfernen | Für Lüfter mit Push-Pull-Tacho-Ausgang |


## Firmware

Die zugehörige Firmware basiert auf dem [OpenKNX](https://github.com/OpenKNX) Framework:
- **OAM-FanControl** (Original): [github.com/mrspieb/OAM-FanControl](https://github.com/mrspieb/OAM-FanControl)
- **OFM-FanControl** (Original): [github.com/mrspieb/OFM-FanControl](https://github.com/mrspieb/OFM-FanControl)

> **Hinweis:** Die Unterstützung für die REG1-FanAktor-2x Hardware (Reg1-Boarddefinition, Fawas AirSolitaire Lüfterklasse, Vollsteuerung, Tacho-Drehzahlmessung) ist aktuell nur in den folgenden Forks verfügbar:
> - [github.com/cad435/OAM-FanControl](https://github.com/cad435/OAM-FanControl)
> - [github.com/cad435/OFM-FanControl](https://github.com/cad435/OFM-FanControl)

## Dateien

| Datei | Beschreibung |
|-------|-------------|
| `REG1-FanAktor-2x_V0.2-Schematic.PDF` | Schaltplan |
| `REG1-FanAktor-2x_V0.2-Pinout.PDF` | Pinbelegung und Lötbrücken-Konfiguration |
| `REG1-FanAktor-2x_V0.2_Assembly.PDF` | Bestückungsplan (Top/Bottom) |
| `BOM-REG1-FanAktor-2x(V0.2).pdf` | Stückliste |
| `JLC_PCBA_BOM-REG1-FanAktor-2x(V0.2).xlsx` | BOM für JLCPCB-Bestückung |
| `JLC_PCBA_CPL-REG1-FanAktor-2x(V0.2).xlsx` | Bauteilplatzierung für JLCPCB |
| `REG1-FanAktor-2x_Gerber.zip` | Gerber-Daten für PCB-Fertigung |
| `REG1-FanAktor-2x(V0.2).step` | 3D-Modell |

## Lizenz

Dieses Projekt ist Teil des [OpenKNX](https://github.com/OpenKNX) Ökosystems.
