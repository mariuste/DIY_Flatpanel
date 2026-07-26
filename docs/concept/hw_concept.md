# Flatfield Panel Controller

Selbstgebautes, regelbares Flatfield-Panel für die Astrofotografie. Das Gerät wird lichtdicht über die Teleskopöffnung gestülpt und erzeugt eine gleichmäßig ausgeleuchtete Fläche zur Aufnahme von Flatframes. Die Helligkeit wird nicht fest vorgegeben, sondern über einen Lichtsensor in der Panelkammer geregelt: Ein gespeicherter Sollwert erzeugt unabhängig von Versorgungsspannung und Temperatur denselben Fluss. Damit ist ein einmal gefundenes Preset dauerhaft reproduzierbar.

Bedient wird im Feld über beleuchtete Taster und ein 7-Segment-Display; Einrichtung und Kalibrierung laufen über eine lokale Webapp (der Controller spannt dafür einen eigenen WLAN-Access-Point auf).

**Zielsystem:** TS-Optics Photoline 72/432 f/6 + TSFLAT72, ZWO ASI294MC Pro, UV/IR-Cut und (geplant) 3 nm Dualband.

**Status:** Konzept und Bauteilauswahl abgeschlossen. Gehäusekonstruktion, Schaltplan, Layout und Firmware stehen aus.

---

## Funktionsprinzip

Das Panel ist edge-lit: Ein zweifarbiger LED-Streifen (Warmweiß/Kaltweiß) speist in die Kante einer klaren Acrylplatte ein, die das Licht über einen Oberflächenschliff nach vorn auskoppelt. Ein Opal-Frontdiffusor vergleichmäßigt die Fläche. Ein Sensor in der Kammer misst den ausgekoppelten Fluss und bildet die Regelgröße; der Controller stellt die PWM der beiden LED-Kanäle so, dass der Sensor den im Preset hinterlegten Zielwert erreicht.

---

## Aufbau — Baugruppen

Das Gerät besteht aus vier Baugruppen:

1. **Optische Baugruppe** — Lichtleiter, Diffusor, Reflektor, LED-Streifen
2. **Elektronik** — Controller-Platine mit Sensorik, Ansteuerung und Bedienteil
3. **Gehäuse** — lichtdichte Kammer mit Aufsetzmechanik aufs Teleskop
4. **Firmware** — Regelung, Presets, Webapp

---

## 1. Optische Baugruppe

Edge-Lit-Panel mit getrenntem Lichtleiter und Streuer.

| Teil | Ausführung |
|---|---|
| LED-Streifen | 12 V WW/CW, hohe LED-Dichte, ~350 mA je Kanal |
| Lichtleiter | klares Guss-PMMA, Oberseite angeschliffen (Ausgangspunkt 120er, kreuzweise) |
| Frontdiffusor | Opalacryl 2–3 mm (Volumenstreuung) |
| Reflektor | weiß diffus, über dem Lichtleiter, mit Luftspalt, kleines Loch für Regelsensor |
| Zusatzdiffusor (optional) | LEE 216 im Spalt, nur bei messbarem Bedarf |
| Kantenverspiegelung | Alu-Klebeband auf den nicht eingespeisten Kanten |

Aufbauregeln: Einspeisekante plan/poliert, Mischzone 20–30 mm ab LED-Kante ungeschliffen, Luftspalt Lichtleiter–Diffusor 5–10 mm mit weißen Seitenwänden, Reflektor mit Luftspalt (kein Kontakt zum Lichtleiter).

Homogenitätsprüfung: Flat aufnehmen, Panel 90° drehen, zweites Flat, dividieren. Zielmarke < 1–2 % über die Apertur. Struktur unterhalb des 72-mm-Pupillendurchmessers wird in der Aufnahme wegintegriert und ist irrelevant.

---

## 2. Elektronik

Controller auf Basis ESP32. Vollständige Stückliste in `hardware/bom.md` (offen), aktive Bauteile hier im Überblick.

### Zentrale Bauteile

| Funktion | Bauteil | Anschluss |
|---|---|---|
| MCU | ESP32-WROOM-32E-N4 | — |
| Regelsensor (Panelkammer) | TSL25911FN | I²C-0, 0x29 |
| Umgebungslicht | LTR-329ALS-01 | I²C-1, 0x29 |
| Umgebungstemperatur | MCP9804-E/MS | I²C-0, 0x18 |
| Strip-Temperatur | NTC MF52C1103F (bedrahtet) | ADC1 |
| ADC-Referenz | LM4040CYM3-2.5 | Teilerspeisung |
| Display | Kingbright CC04-41SURKWA (4-stellig 7-Segment, rot, CC) | — |
| Display-Treiber | AS1115-BSST (QSOP-24) | I²C-0, 0x00 |
| LED-Schalt-FETs (WW, CW, Tasten rot, Tasten gelb) | 4× AO3400A | Low-side, PWM |
| Verpolschutz | AO3401A | — |
| Spannungsregler | Recom TSR 1-2433E (3,3 V / 1 A) | — |

### Bedienteil

| Funktion | Taster |
|---|---|
| Plus / Minus / Cal | 3× Würth WS-TATL 440RS67082622 (rot, beleuchtet, 12×12 mm) |
| Blackout | 1× Würth WS-TATL 440YS67082622 (gelb, separater LED-Pfad) |

Blackout schaltet Display und rote Tasten-LEDs aus, nur die gelbe Taste bleibt schwach an — für den Betrieb während der Aufnahme.

### Eingangskette (Schutz und Versorgung)

```
12V-Jack -> PPTC (SMD1812B260TF/16) -> Ferrit (FBMH3225HM601NT)
  -> TVS (SMAJ18A) -> Verpolschutz (AO3401A) -> Puffer -> Buck (TSR 1-2433E)
```

Absicherung gegen Kurzschluss des angeschlossenen Akkus, HF-Entkopplung zur Kamera-USB, Transientenschutz.

---

## 3. Gehäuse

Lichtdichte Kammer, die über die Teleskopöffnung gestülpt wird. Die Elektronik-Platine bildet den oberen Abschluss und dichtet per TPU-Ring gegen das Gehäuse; der Regelsensor blickt durch ein Loch im Reflektor in die Panelkammer.

Anforderungen an das Gehäuse:
- lichtdichte Kammer über der Optik (kein Fremdlicht)
- Aufsetzmechanik auf die Taukappe des Teleskops
- getrennte Bedienseite (Display, Taster) mit Ausschnitten
- Elektronik- und Sensorkammer streulichtdicht von der Bedienseite getrennt

**Offen:** Fertigungsverfahren (3D-Druck / gefräst / Alu-Profil), konkrete Aufsetz- und Klemmmechanik, Maße. CAD/Druckdateien unter `mechanical/`.

---

## 4. Firmware

- Regelung auf Sensor-Zielwert (Sollwert in Sensor-Counts, nicht im PWM-Tastgrad)
- WW/CW sequenziell kalibriert, Presets als Wertepaar + Sensor-Gain + Name in NVS
- Regler langsam, nur I-Anteil; Dunkelmessung vor jeder Regelung
- Display- und Tasten-Helligkeit aus dem Umgebungslichtsensor
- Webapp über SoftAP für Kalibrierung und Preset-Verwaltung; WiFi für den Flat-Betrieb abschaltbar

Details und Regel-Leitlinien in `docs/calibration.md` (offen).

---

## Repository-Struktur

```
README.md              dieses Dokument — Geräteübersicht und Teileliste
docs/
  optics.md            Diffusor-Aufbau, Materialien, Schliff, Homogenitätsmessung
  enclosure.md         Gehäuse, Aufsetzmechanik, Lichtdichtung
  calibration.md       Regelkonzept, Kalibrierablauf, Messprotokolle
hardware/
  bom.md               vollständige Stückliste
  (Schaltplan, Layout)
firmware/
mechanical/            CAD / STL für Gehäuse und Optikhalter
```

---

## Offene Arbeitspakete

- [ ] Gehäusekonstruktion (Fertigung, Aufsetzmechanik, Kammertrennung)
- [ ] Pinmapping (GPIO-Zuordnung, Strapping-/Input-only-Pins)
- [ ] Schaltplan und PCB-Layout
- [ ] Firmware (Regelung, Presets, Webapp)
- [ ] Optische Validierung (Homogenität, Kanalbalance durch Dualband)

---

## Kerndaten

| | |
|---|---|
| Versorgung | 12 V (ASIAIR-Port oder Netzteil), ~1,2 A Systemstrom |
| Freie Apertur | ~110 mm |
| PWM | 1 kHz, 16 Bit (WW/CW) |
| Flat-Belichtung | 2–5 s (UV/IR-Cut), länger bei Dualband |
| Bedienung Feld | 4 beleuchtete Taster + 7-Segment-Display |
| Bedienung Einrichtung | Webapp über eigenen WLAN-Access-Point |
