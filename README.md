<div align="center">
  <img width="45%" src="https://github.com/user-attachments/assets/5c8d5020-0813-400c-bc13-7296174d83d3" />
  <img width="45%" src="https://github.com/user-attachments/assets/1a03658d-e2db-4db3-a6f2-3fbca09909f1" />
</div>


# Uniwersalny Interfejs Radiowy / Universal Radio Interface

> Kraków, 31 sierpnia 2026 | Hackerspace Kraków  
> Licencja / License: [CC BY-NC-SA](https://creativecommons.org/licenses/by-nc-sa/4.0/)

---

##  Polski (English below)

### Opis projektu

Uniwersalny interfejs radiowy to otwarto-sprzętowy projekt płytki PCB, której celem jest stworzenie jak najbardziej elastycznego interfejsu do **emisji cyfrowych** oraz **zdalnej kontroli urządzeń radiowych (TRX)**. Interfejs zaprojektowano z myślą o kompatybilności z możliwie największą liczbą modeli i producentów transceiverów.

### Główne założenia

- **Uniwersalność** – współpraca z szeroką gamą modeli TRX różnych producentów
- **Minimalizacja okablowania** – w skrajnym przypadku wystarczy kabel USB, kabel sygnałowy do radia i zasilanie
- **Pełna separacja galwaniczna** – izolacja zaraz za gniazdem USB chroni sprzęt przed przepięciami i biegającym RF; osobna separacja toru audio

### Moduły

| # | Moduł | Chip |
|---|-------|------|
| 1 | HUB USB | **Fe1.1s** |
| 2 | Kodek dźwięku na USB (źródło dla emisji cyfrowych) | **CM108B** |
| 3 | 4-portowy konwerter USB → UART (CAT/CI-V, CW, PTT, FSK) | **CP2105** |
| 4 | Konwerter poziomów RS232/TTL | **MAX232** |
| 5 | Zasilacz (szeroki zakres napięć wejściowych: 7–18 V) | **AMS1117-5.0** |
| 6 | Moduł zworkologii – konfiguracja portów CAT, linii CW, PTT i FSK (w tym z konwerterem poziomów) | – |


### PCB

- Dwustronna płytka w technologii SMD
- Projekt wykonany w **KiCad 9** — pliki schematu, PCB oraz biblioteki footprintów i symboli dostępne w tym repozytorium
- Elementy mają przypisane identyfikatory JLCPCB, dzięki czemu można bezpośrednio zamówić płytkę z usługą montażu SMD (PCBA) — wystarczy wygenerować pliki Gerber, BOM i pliki pick & place z KiCad i wgrać je na stronę JLCPCB
- Katalog `bom/` zawiera interaktywny plik HTNL ułatwiający montaż

### Jak zacząć

1. Sklonuj lub pobierz repozytorium
2. Otwórz `interfejs_digi.kicad_pro` w KiCad 9
3. Przejrzyj schemat (`interfejs_digi.kicad_sch`) i projekt PCB (`interfejs_digi.kicad_pcb`)
4. Wygeneruj pliki Gerber, BOM oraz pick & place z poziomu KiCad
5. Wgraj pliki na stronę [JLCPCB](https://jlcpcb.com) i zamów płytkę z montażem SMD (PCBA) — identyfikatory elementów są już uzupełnione
6. Po otrzymaniu płytki skonfiguruj zworki zgodnie z używanym modelem TRX

### Pinout gniazd RJ45

Interfejs wyprowadza sygnały na dwóch gniazdach RJ45 (HX-RJ45 90 5631-1X1 8P8C).

**RJ1 — gniazdo główne**

| Pin | Sygnał | Opis |
|-----|--------|------|
| 1 | CAT1\_RX | Odbieranie CAT/CI-V (port 1) |
| 2 | CAT1\_TX | Nadawanie CAT/CI-V (port 1) |
| 3 | GND\_IN | Masa zasilania |
| 4 | PWR\_IN | Zasilanie 7-18V (wejście) |
| 5 | PTT\_OUT | Wyjście PTT (otwarty dren, tranzystor 2N7002) |
| 6 | GND | Masa sygnałowa |
| 7 | AUDIO\_OUT | Wyjście audio do radia (TX) |
| 8 | AUDIO\_IN | Wejście audio z radia (RX) |

**RJ2 — gniazdo dodatkowe**

| Pin | Sygnał | Opis |
|-----|--------|------|
| 1 | CAT2\_RX | Odbieranie CAT (port 2) |
| 2 | CAT2\_TX | Nadawanie CAT (port 2) |
| 3 | FSK\_OUT | Wyjście FSK (otwarty dren, tranzystor 2N7002) |
| 4 | – | Niepodłączony |
| 5 | CW\_OUT | Wyjście kluczowania CW (otwarty dren, tranzystor 2N7002) |
| 6 | – | Niepodłączony |
| 7 | – | Niepodłączony |
| 8 | – | Niepodłączony |

Wyjścia PTT, CW i FSK są wyjściami otwartego drenu przez tranzystor MOSFET 2N7002 z diodą LED sygnalizacyjną. Masa obu gniazd jest wspólna (pin 10 złącza RJ45 podłączony do GND).

### Konfiguracja zworkami

Zworki J7–J11 pozwalają elastycznie przypisać sygnały do linii sterujących oraz wybrać poziomy napięć. Każda zworka ma 6 pinów (3 pary), co umożliwia niezależny wybór dla każdej linii.

**J7, J8 — CAT\_1 i CAT\_2**

Pod każdym złączem CAT znajduje się goldpin 2×3 z dwiema klasycznymi zworkami — osobno dla linii RX i TX. Obie zworki ustawia się zawsze w tej samej pozycji. Konfiguracja jest wspólna dla CAT\_1 i CAT\_2:

| Pozycja zworek | Poziom sygnału |
|----------------|----------------|
| obie zworki **w górze** | TTL (5 V) |
| obie zworki **w dole** | RS232 (±12 V) |

**J9 — CW** — wybór źródła sygnału CW spośród linii UART:

| Pozycja zworki | Źródło |
|----------------|--------|
| piny 1–2 | DTR\_1 |
| piny 3–4 | RTS\_1 |
| piny 5–6 | RTS\_2 |

**J10 — PTT** — wybór źródła sygnału PTT:

| Pozycja zworki | Źródło |
|----------------|--------|
| piny 1–2 | DTR\_1 |
| piny 3–4 | RTS\_1 |
| piny 5–6 | RTS\_2 |

**J11 — FSK** — wybór źródła sygnału FSK:

| Pozycja zworki | Źródło |
|----------------|--------|
| piny 1–2 | DTR\_2 |
| piny 3–4 | RTS\_2 |
| piny 5–6 | RTS\_1 |

**J2 — CI-V** — gniazdo CI-V (Icom). Po włożeniu zworki sygnał CAT1\_TX\_TTL jest zwrotnie podłączony przez diodę D5 (1N5819WS) do CAT1\_RX\_TTL, realizując magistralę jednolinową wymaganą przez protokół CI-V.

### Licencja

Projekt udostępniony na licencji **Creative Commons BY-NC-SA**. Możesz go swobodnie używać, modyfikować i udostępniać pod warunkiem podania autora, zachowania tej samej licencji i braku użytku komercyjnego.

---

##  English

### Project Description

The Universal Radio Interface is an open-hardware PCB project designed to provide a flexible interface for **digital mode operations** and **remote control of radio transceivers (TRX)**. The board is designed to be compatible with as many TRX models and manufacturers as possible.

### Design Goals

- **Universality** – works with a wide range of TRX models from various manufacturers
- **Minimal cabling** – in the simplest setup only a USB cable, a radio signal cable, and power supply are needed
- **Full galvanic isolation** – isolation placed immediately after the USB connector protects equipment from voltage spikes and stray RF; the audio path is isolated separately

### Modules

| # | Module | Chip |
|---|--------|------|
| 1 | USB Hub | **Fe1.1s** |
| 2 | USB Audio Codec (source for digital modes) | **CM108B** |
| 3 | 4-port USB → UART converter (CAT/CI-V, CW, PTT, FSK) | **CP2105** |
| 4 | RS232/TTL Level Converter | **MAX232** |
| 5 | Power Supply (wide input voltage range: 7–18 V) | **AMS1117-5.0** |
| 6 | Jumper configuration module – configures CAT ports, CW, PTT and FSK lines (including level converter options) | – |


### PCB

- Double-sided SMD board
- Designed in **KiCad 9** — schematic, PCB layout, footprint and symbol libraries all included in this repository
- All components have JLCPCB part IDs assigned, so the board can be ordered with SMD assembly (PCBA) — simply generate Gerber, BOM and pick & place files from KiCad and upload them to JLCPCB
- The `bom/` directory contains an interactive HTML file to assist with assembly.

### Getting Started

1. Clone or download this repository
2. Open `interfejs_digi.kicad_pro` in KiCad 9
3. Review the schematic (`interfejs_digi.kicad_sch`) and PCB layout (`interfejs_digi.kicad_pcb`)
4. Generate Gerber, BOM and pick & place files from KiCad
5. Upload the files to [JLCPCB](https://jlcpcb.com) and order the board with SMD assembly (PCBA) — component IDs are already filled in
6. Once the board arrives, configure the jumpers for your TRX model

### RJ45 Connector Pinout

The interface exposes signals on two RJ45 connectors (HX-RJ45 90 5631-1X1 8P8C). 

**RJ1 — Main connector**

| Pin | Signal | Description |
|-----|--------|-------------|
| 1 | CAT1\_RX | CAT/CI-V receive (port 1) |
| 2 | CAT1\_TX | CAT/CI-V transmit (port 1) |
| 3 | GND\_IN | Power ground |
| 4 | PWR\_IN | 7-18V power input |
| 5 | PTT\_OUT | PTT output (open drain, 2N7002 MOSFET) |
| 6 | GND | Signal ground |
| 7 | AUDIO\_OUT | Audio output to radio (TX) |
| 8 | AUDIO\_IN | Audio input from radio (RX) |

**RJ2 — Secondary connector**

| Pin | Signal | Description |
|-----|--------|-------------|
| 1 | CAT2\_RX | CAT receive (port 2) |
| 2 | CAT2\_TX | CAT transmit (port 2) |
| 3 | FSK\_OUT | FSK output (open drain, 2N7002 MOSFET) |
| 4 | – | Not connected |
| 5 | CW\_OUT | CW keying output (open drain, 2N7002 MOSFET) |
| 6 | – | Not connected |
| 7 | – | Not connected |
| 8 | – | Not connected |

PTT, CW and FSK outputs are open-drain via 2N7002 MOSFET transistors, each with a status LED indicator. The ground of both connectors is shared (RJ45 pin 10 tied to GND).

### Jumper Configuration

Jumpers J7–J11 allow flexible assignment of signals to control lines and voltage level selection. Each jumper has 6 pins (3 pairs), enabling independent selection per line.

**J7, J8 — CAT\_1 and CAT\_2**

Below each CAT connector there is a 2×3 pin header with two standard jumpers — one for RX and one for TX. Both jumpers are always set to the same position. The configuration applies equally to CAT\_1 and CAT\_2:

| Jumper position | Signal level |
|-----------------|--------------|
| both jumpers **up** | TTL (5 V) |
| both jumpers **down** | RS232 (±12 V) |

**J9 — CW** — selects the UART line used as the CW source:

| Jumper position | Source |
|-----------------|--------|
| pins 1–2 | DTR\_1 |
| pins 3–4 | RTS\_1 |
| pins 5–6 | RTS\_2 |

**J10 — PTT** — selects the PTT source:

| Jumper position | Source |
|-----------------|--------|
| pins 1–2 | DTR\_1 |
| pins 3–4 | RTS\_1 |
| pins 5–6 | RTS\_2 |

**J11 — FSK** — selects the FSK source:

| Jumper position | Source |
|-----------------|--------|
| pins 1–2 | DTR\_2 |
| pins 3–4 | RTS\_2 |
| pins 5–6 | RTS\_1 |

**J2 — CI-V** — CI-V connector (Icom radios). When a jumper is inserted, CAT1\_TX\_TTL is looped back through diode D5 (1N5819WS) to CAT1\_RX\_TTL, implementing the single-wire bus required by the CI-V protocol.

### License

This project is released under the **Creative Commons BY-NC-SA** license. You are free to use, modify, and share it as long as you credit the author, keep the same license, and do not use it commercially.

---

*Design by Szymon Reiter / Hackerspace Kraków*
