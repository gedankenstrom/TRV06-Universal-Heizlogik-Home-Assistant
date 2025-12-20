# TRV06 – Dynamic Setpoint Heizlogik (Sensor + Abwesenheit + Live-Status)

Komplett automatische Heizlogik für **AVATTO TRV06** Thermostate in Home Assistant.

Dieses Blueprint nutzt:
- **externe Raumtemperatursensoren** (echte Raumtemperatur)
- **TRV interne Temperatur** (aus `current_temperature` → Template-Sensor)
- **4 Zeitfenster** (Morgen/Mittag/Abend/Nacht)
- **Fenster/Tür/Lüften-Blocker** (externer Helfer)
- **Global Away + Room Away** (tagsüber)
- **Live-Statusausgabe** in ein `input_text` fürs Dashboard

**Wichtig:** Room-Away wird automatisch ignoriert, wenn Global-Away aktiv ist.

---

## Features

### 🌡️ Externer Sensor als Referenz
Die Raumtemperatur wird über einen externen Temperatursensor geregelt (z. B. Zigbee). Das ist meist deutlich genauer als die TRV-Messung direkt am Heizkörper.

### ⏱️ Zeitfenster / Profile
4 Zeitprofile mit eigener Solltemperatur:
- Morgen
- Mittag
- Abend
- Nacht

**Nachtmodus:** Es gilt ausschließlich die Nacht-Solltemperatur – **keine zusätzliche Absenkung durch Away**.

### 🎯 Automatik-Modi
Je nach Raumtemperatur schaltet die Logik automatisch:

- **HEIZEN**: Raum < Zieltemperatur → TRV auf Zieltemperatur
- **HALTEN**: Ziel ≤ Raum < Ziel + Overshoot → TRV auf `TRV_intern + HOLD-Offset`
- **COOL**: Raum ≥ Ziel + Overshoot → TRV auf `TRV_intern + COOL-Offset`

Damit wird Takten reduziert und Überschwingen besser kontrolliert.

### 🚪 Fenster/Tür/Lüften (extern)
Die Blueprint nutzt einen externen Helfer:
- `input_boolean` oder `binary_sensor`
- **ON/TRUE = Heizung aus**

**Delay/Entprellung** (z. B. „Fenster muss 2 Minuten offen sein“) wird bewusst **nicht** in der Blueprint gemacht, sondern über **separate Automationen** (siehe Beispiele unten).

### 🏠 Abwesenheit (tagsüber)
- **Global Away**: ganze Wohnung abwesend
- **Room Away**: nur ein Raum abwesend

Regeln:
- wirkt nur **tagsüber**
- nachts **keine zusätzliche Absenkung**
- Room Away wird ignoriert, wenn Global Away aktiv ist

### 📟 Live-Status im Dashboard
Die Automation schreibt in ein `input_text` z. B.:
- `HEIZEN • 22.0 °C`
- `HALTEN • 21.1 °C`
- `COOL • 19.3 °C`
- `AUS • Lüften/Tür/Fenster`
- `AUS • Master OFF`

---

## Voraussetzungen

- Home Assistant
- AVATTO TRV06 Thermostat als `climate.*`
- Externer Temperatursensor als `sensor.*` (Raumtemperatur)

---

## Helfer anlegen (nur UI)

Alle Helfer legst du in Home Assistant über die Oberfläche an:

**Einstellungen → Geräte & Dienste → Helfer → Helfer erstellen**

### 1) Master EIN/AUS (Pflicht)
**Typ:** Schalter (`input_boolean`)  
**Beispiel:** `input_boolean.heating_enabled`  
- OFF = TRV wird auf 5°C gesetzt und ausgeschaltet (harte Abschaltung)

### 2) Lüften-Blocker (Pflicht)
**Typ:** Schalter (`input_boolean`) *oder* Binärsensor (`binary_sensor`)  
**Beispiel:** `input_boolean.vent_blocker`  
- ON/TRUE = Heizung aus  
- Delay/Entprellung machst du optional über Automationen (siehe unten)

### 3) Global Away (optional, empfohlen)
**Typ:** Schalter (`input_boolean`)  
**Beispiel:** `input_boolean.wohnung_abwesenheit`  
- Markiert die ganze Wohnung als abwesend

### 4) Room Away / Raum-Abwesenheit (optional)
**Typ:** Schalter (`input_boolean`)  
**Beispiel (Bad):** `input_boolean.badezimmer_anwesenheit`  
- In deiner Logik bedeutet dieses Beispiel: **ON = Abwesenheit aktiv** (siehe Automation unten)
- Wird ignoriert, wenn Global Away aktiv ist

> Hinweis: Du kannst pro Raum einen eigenen Helfer anlegen (z. B. Schlafzimmer, Büro, etc.).

### 5) Status Text (Pflicht)
**Typ:** Text (`input_text`)  
**Beispiel:** `input_text.trv06_status_badezimmer`  
- Hier schreibt die Blueprint den Live-Status rein

---

## TRV interne Temperatur als Sensor anlegen (nur UI)

Damit du im Blueprint „TRV interne Temperatur“ auswählen kannst, brauchst du einen **Template-Sensor**, der das Attribut `current_temperature` aus der `climate`-Entity als `sensor.*` bereitstellt.

### Schritte
1. **Einstellungen → Geräte & Dienste → Helfer**
2. **Helfer erstellen**
3. **Template** auswählen
4. **Template-Sensor** auswählen
5. Werte setzen:

- **Name:** `TRV Badezimmer interne Temperatur`
- **Zustands-Template (State template):**
  ```jinja2
  {{ state_attr('climate.badezimmer_thermostat', 'current_temperature') | float(0) }}
  ```
- **Einheit:** `°C`
- **Geräteklasse (Device class):** `temperature`
- **Zustandsklasse (State class):** `measurement`

✅ Danach erscheint ein neuer Sensor `sensor.*`, den du im Blueprint auswählen kannst.

### Kontrolle (UI)
**Entwicklerwerkzeuge → Zustände**  
Suche `climate.badezimmer_thermostat` und prüfe, ob in den Attributen `current_temperature` vorhanden ist.

---

## Installation

1. Blueprint in Home Assistant importieren (Blueprints → Import).
2. Automation aus dem Blueprint erstellen.
3. Entities auswählen:
   - TRV Thermostat (`climate.*`)
   - externer Raumtemperatursensor (`sensor.*`)
   - TRV interne Temperatur (Template-Sensor `sensor.*`)
   - Master-Schalter (`input_boolean`)
   - Lüften-Blocker (`input_boolean`/`binary_sensor`)
   - optional: Global Away / Room Away (`input_boolean`)
   - Status Text (`input_text`)

---

## Beispiel-Automationen (mit Delay)

Die folgenden Beispiele schalten nur die Helfer-Entities (Away / Lüften-Blocker).  
So bleibt die Blueprint selbst schlank, reagiert sauber, und du kannst Delay/Entprellung frei anpassen.

> **Wichtig:** In den Beispielen steht `action:` (neuer YAML-Stil). Falls dein HA noch `service:` nutzt, kannst du das entsprechend umstellen.

### 1) Badezimmer Abwesenheit automatisch setzen (Motion-basiert)

Schaltet die Badezimmer-Abwesenheit:
- **AUS**, sobald Bewegung erkannt wird
- **EIN**, wenn 30 Minuten keine Bewegung erkannt wird

```yaml
alias: Badezimmer Abwesenheit automatisch setzen
description: >-
  Schaltet die Badezimmer Abwesenheit EIN, wenn X Minuten keine Bewegung erkannt
  wird – und AUS, sobald Bewegung erkannt wird.
triggers:
  - entity_id:
      - binary_sensor.badezimmer_sensor_occupancy
    from:
      - "off"
    to:
      - "on"
    trigger: state
  - entity_id:
      - binary_sensor.badezimmer_sensor_occupancy
    from:
      - "on"
    to:
      - "off"
    for:
      hours: 0
      minutes: 30
      seconds: 0
    trigger: state
conditions: []
actions:
  - choose:
      - conditions:
          - condition: state
            entity_id: binary_sensor.badezimmer_sensor_occupancy
            state: "on"
        sequence:
          - action: input_boolean.turn_off
            target:
              entity_id: input_boolean.badezimmer_anwesenheit
          - stop: Bewegung erkannt → Abwesenheit aus
      - conditions:
          - condition: state
            entity_id: binary_sensor.badezimmer_sensor_occupancy
            state: "off"
        sequence:
          - action: input_boolean.turn_on
            target:
              entity_id: input_boolean.badezimmer_anwesenheit
mode: restart
```

### 2) Badezimmer Tür Lüften-Block automatisch (mit 2 Minuten Delay)

Blockiert die Heizung bei offener Tür (oder Fensterkontakt) und gibt sie wieder frei:
- Tür **offen** für 2 Minuten → Blocker **AN**
- Tür **zu** → Blocker **AUS**

```yaml
alias: Badezimmer Tür Lüften-Block automatisch
description: >
  Blockiert die Heizung im Badezimmer bei offenem Fenster/Tür und gibt sie nach
  Schließen wieder frei.
triggers:
  - entity_id:
      - binary_sensor.badezimmer_sensor_tur_contact
    to:
      - "off"
    trigger: state
  - entity_id:
      - binary_sensor.badezimmer_sensor_tur_contact
    to:
      - "on"
    for:
      hours: 0
      minutes: 2
      seconds: 0
    trigger: state
actions:
  - choose:
      - conditions:
          - condition: state
            entity_id: binary_sensor.badezimmer_sensor_tur_contact
            state:
              - "on"
        sequence:
          - target:
              entity_id:
                - input_boolean.badezimmer_tur
            action: input_boolean.turn_on
            data: {}
      - conditions:
          - condition: state
            entity_id: binary_sensor.badezimmer_sensor_tur_contact
            state:
              - "off"
        sequence:
          - target:
              entity_id:
                - input_boolean.badezimmer_tur
            action: input_boolean.turn_off
            data: {}
mode: restart
```

> Hinweis: In dieser Automation ist die Semantik abhängig von deinem Sensor (`on/off`).  
> Wenn dein Kontakt **on = offen** ist, passt es so. Falls bei dir **off = offen**, musst du die `to:`/States entsprechend drehen.

### 3) Globale Abwesenheit automatisch setzen (Wohnung gesamt)

Schaltet die globale Abwesenheit:
- **AUS**, sobald Bewegung in der Wohnung erkannt wird
- **EIN**, wenn 2 Stunden keine Bewegung erkannt wird

```yaml
alias: Globale Abwesenheit automatisch setzen
description: >-
  Schaltet die globale Abwesenheit EIN, wenn X Minuten keine Bewegung erkannt
  wird – und AUS, sobald Bewegung erkannt wird.
triggers:
  - entity_id: binary_sensor.bewegung_wohnung_gesamt
    from: "off"
    to: "on"
    trigger: state
  - entity_id:
      - binary_sensor.bewegung_wohnung_gesamt
    from:
      - "on"
    to:
      - "off"
    for:
      hours: 2
      minutes: 0
      seconds: 0
    trigger: state
conditions: []
actions:
  - choose:
      - conditions:
          - condition: state
            entity_id: binary_sensor.bewegung_wohnung_gesamt
            state: "on"
        sequence:
          - action: input_boolean.turn_off
            target:
              entity_id: input_boolean.wohnung_abwesenheit
          - stop: Bewegung erkannt → Abwesenheit aus
      - conditions:
          - condition: state
            entity_id: binary_sensor.bewegung_wohnung_gesamt
            state: "off"
        sequence:
          - action: input_boolean.turn_on
            target:
              entity_id: input_boolean.wohnung_abwesenheit
            data: {}
mode: restart
```

---

## Lizenz
Frei nutzbar für private Home-Assistant-Installationen.
