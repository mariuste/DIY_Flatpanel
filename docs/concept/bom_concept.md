# BOM — Flatfield Panel Controller

Vollständige Stückliste der Controller-Platine. Bestückung mit Lotpaste und Reflow-Ofen; Gehäuse mit außenliegenden Pins bevorzugt, Thermal-Pads nur bei Packages > 2×2 mm.

Passiva-Standard: 0805, sofern nicht anders angegeben. Mit **(trimmen)** markierte Werte sind Startwerte und im Testaufbau festzulegen. Mit **(prüfen)** markierte Teilenummern vor Bestellung gegen Distributor-Vorschau/Datenblatt gegenlesen.

Status: aktive Bauteile final gewählt, Passiva als Richtwerte. Referenzen (U/Q/R/C…) sind vorläufig und werden beim Schaltplan vergeben.

---

## Aktive Bauteile / ICs

| Ref | Funktion | Bauteil | Gehäuse | Menge | Hinweis |
|---|---|---|---|---|---|
| U1 | MCU | ESP32-WROOM-32E-N4 | Modul, PCB-Antenne | 1 | 4 MB Flash; Keep-out unter Antenne |
| U2 | Buck 12→3,3 V | Recom TSR 1-2433E | SIP-3 | 1 | 1 A, rauscharm |
| U3 | Regelsensor Panelkammer | TSL25911FN | DFN-6 (Kantenpads) | 1 | I²C-0, 0x29 |
| U4 | Umgebungslichtsensor | LTR-329ALS-01 | LGA (Kantenpads) | 1 | I²C-1, 0x29 |
| U5 | Umgebungstemperatur | MCP9804T-E/MS | MSOP-8 | 1 | I²C-0, 0x18; Adr.-Pins auf 0x18 |
| U6 | Display-Treiber | AS1115-BSST | QSOP-24 | 1 | I²C-0, 0x00; Common-Cathode |
| U7 | ADC-Referenz | LM4040CYM3-2.5 | SOT-23-3 | 1 | 2,5 V Shunt |
| Q1 | Strip-FET WW | AO3400A | SOT-23 | 1 | Low-side, PWM 16 Bit |
| Q2 | Strip-FET CW | AO3400A | SOT-23 | 1 | Low-side, PWM 16 Bit |
| Q3 | Tasten-LED-FET (rote Gruppe) | AO3400A | SOT-23 | 1 | Low-side, PWM |
| Q4 | Tasten-LED-FET (Blackout gelb) | AO3400A | SOT-23 | 1 | Low-side, PWM, separat |
| Q5 | Verpolschutz | AO3401A | SOT-23 | 1 | P-Kanal, Plusleitung |

---

## Anzeige und Bedienteil

| Ref | Funktion | Bauteil | Menge | Hinweis |
|---|---|---|---|---|
| DS1 | Display | Kingbright CC04-41SURKWA | 1 | 4-stellig 7-Segment, rot, gem. Kathode, 20-DIP |
| SW1 | Taster Plus | Würth WS-TATL 440RS67082622 (rot) | 1 | 12×12 mm, integrierte LED, THT |
| SW2 | Taster Minus | Würth WS-TATL 440RS67082622 (rot) | 1 | |
| SW3 | Taster Cal | Würth WS-TATL 440RS67082622 (rot) | 1 | |
| SW4 | Taster Blackout | Würth WS-TATL 440YS67082622 (gelb) | 1 | separater LED-Pfad |
| — | Cap Pfeil ↑ / ↓ | Würth 714401002 | 2 | für Plus/Minus (prüfen) |
| — | Cap transparent flach | Würth 714409000 | 2 | für Cal/Blackout, Symbol selbst plotten (prüfen) |

---

## Eingang, Schutz, Versorgung

| Ref | Funktion | Bauteil | Gehäuse | Menge | Hinweis |
|---|---|---|---|---|---|
| J1 | DC-Buchse | Hohlbuchse 5,5/2,1 mm | THT | 1 | ASIAIR-kompatibel |
| F1 | Sicherung | PPTC SMD1812B260TF/16 | 1812 | 1 | 2,6 A Haltestrom |
| FB1 | Ferritperle | FBMH3225HM601NT | 1210 | 1 | 600 Ω/100 MHz |
| D1 | TVS | SMAJ18A | SMA | 1 | 18 V unidirektional |
| D2 | Gate-Zener Verpolschutz | 10 V Zener | SOD-323 | 1 | Q5 Gate-Source-Schutz |
| C1 | Eingangs-Elko | 470 µF / 25 V | Elko, low ESR | 1 | am Eingang |
| C2 | Strip-Puffer-Elko | 470 µF / 25 V | Elko, low ESR | 1 | nahe Strip-Anschluss |
| C3 | Eingangs-Keramik | 10 µF / 25 V | 1206 X7R | 1 | |
| C4 | Buck-Ausgang | 100 µF | Elko/MLCC | 1 | nahe MCU |

---

## Passiva und Kleinsignal

| Ref | Funktion | Wert | Menge | Hinweis |
|---|---|---|---|---|
| R1–R4 | FET-Gate-Serie | 33 Ω | 4 | je FET Q1–Q4 |
| R5–R8 | FET-Gate-Pulldown | 10 kΩ | 4 | definierter Aus-Zustand beim Boot |
| R9–R12 | Tasten-LED-Vorwiderstand | 3,3 kΩ | 4 | je LED; **(trimmen)** nach Helligkeit |
| R13 | RSET Display | 28 kΩ | 1 | Segmentstrom AS1115; **(trimmen)** ~27–30 k |
| R14 | NTC-Teiler | 10 kΩ 1 % | 1 | gegen NTC, an 2,5-V-Ref |
| R15 | NTC-Serie zum ADC | 1 kΩ | 1 | mit C für RC-Filter |
| R16 | Referenz-Vorwiderstand | 820 Ω | 1 | Speisung LM4040 aus 3,3 V |
| R17, R18 | Spannungsteiler 12-V-Messung | 68 kΩ / 10 kΩ 1 % | 2 | an ADC1, aus 2,5-V-Ref referenziert |
| R19, R20 | I²C-0 Pullup | 4,7 kΩ | 2 | SDA/SCL Bus 0 |
| R21, R22 | I²C-1 Pullup | 4,7 kΩ | 2 | SDA/SCL Bus 1 |
| R23 | Verpolschutz Gate-Pulldown | 100 kΩ | 1 | Q5 |
| C5 | NTC-Filter | 100 nF | 1 | am ADC-Pin |
| C6 | Referenz-Abblock | 100 nF | 1 | an LM4040 |
| C7 | Display VDD Abblock | 10 µF | 1 | an AS1115 |
| C8 | Display VDD Keramik | 100 nF | 1 | an AS1115 |
| C9–C14 | Abblock allgemein | 100 nF | 6 | an MCU, FETs, Buck-Eingang |

---

## Sensor am LED-Streifen (extern verkabelt)

| Ref | Funktion | Bauteil | Menge | Hinweis |
|---|---|---|---|---|
| RT1 | Strip-Temperatur | NTC MF52C1103F (10 k, B3380, Glasperle, 200 mm Kabel) | 1 | ADC1; thermisch an Alu-Profil koppeln |

Anschluss über 2-polige Klemme oder JST-PH, damit Streifen trennbar bleibt. Adern verdrillen (hochohmiger Pfad neben PWM-Strip).

---

## Steckverbinder / mechanisch

| Ref | Funktion | Bauteil | Menge | Hinweis |
|---|---|---|---|---|
| J2 | LED-Streifen-Anschluss | 3-pol Schraubklemme 5 mm | 1 | +12 V, WW−, CW− (gemeinsame Anode) |
| J3 | NTC-Anschluss | 2-pol JST-PH | 1 | Strip-Temperatur |
| J4 | Programmierheader | 6-pol 2,54 mm | 1 | GND, 3V3, EN, GPIO0, TX, RX |
| — | Dichtung | TPU-Ring | 1 | PCB gegen Gehäuse, lichtdicht |

---

## Nicht bestückt / Vorhalt (optional)

Für späteren Konstantstrombetrieb der LED-Kanäle als unbestückten Footprint mitlayouten:

| Funktion | Bauteil | Hinweis |
|---|---|---|
| Shunt je Kanal | 0,1 Ω 1 % | in Source-Leitung, per 0 Ω gebrückt |
| Stromregel-Opamp | MCP6002 (SOT-23-5-Footprint) | RC vom PWM-Pin auf Plus-Eingang |

---

## Sammelbestellhinweise

- **ESP32-WROOM-32E-N4:** bestverfügbare Variante (4 MB, PCB-Antenne).
- **AS1115-BSST:** QSOP-24-Variante nehmen, nicht die TQFN (Exposed Pad, 4×4 mm).
- **Würth WS-TATL:** kostenlose Muster für Prototypen möglich; Cap-Teilenummern (714401002, 714409000) vor Bestellung gegen Vorschau prüfen.
- **CC04-41SURKWA:** 7-Segment (nicht 14), gemeinsame Kathode — passt zum AS1115.
- **AO3400A:** ein Typ für alle vier Schaltpfade (Strip + Tasten), Teilevereinheitlichung.
