# TRV06 – Dynamic Setpoint Heizlogik V3
**Vollautomatische Heizlogik für Tuya/AVATTO TRV06 Thermostate**  
Regelt mit externem Temperatursensor, verhindert Überhitzen, hält den Heizkörper gleichmäßig warm und integriert Fenster- sowie Tag/Nacht-Logik.

---

## 🚀 Funktionen

### 🔥 Präzise Heizregelung über externen Sensor
Der TRV06 regelt nicht über seine unzuverlässige interne Temperatur, sondern über einen externen Raumsensor. Dadurch wird eine viel genauere Raumtemperatur erreicht.

### 🕒 Automatische Tag/Nacht-Temperaturen
Einstellbare Tag- und Nachtzeiten mit eigenen Zieltemperaturen.

### ♨️ Intelligente Betriebsmodi
- **HEAT** – Aufheizen wenn Raum zu kalt  
- **HOLD** – Warmhalten bei erreichter Zieltemperatur  
- **COOL** – Abkühlen bei Überhitzung (verhindert Overshoot)

### 🪟 Fenster-/Tür-Erkennung
Verhindert Energieverlust:  
Offenes Fenster → TRV wird nach einstellbarem Delay ausgeschaltet.

### 🔌 Master EIN/AUS
Globaler Schalter deaktiviert die gesamte Heizlogik.

### 📊 Debug-Logging
Optionale Ausgabe aller relevanten Zustände ins Logbook.

---

## Entitäten eintragen:

- TRV06 Thermostat
- Externer Temp-Sensor
- TRV interne Temperatur
- Fenster-/Türkontakt
- Input Boolean als Master-Schalter

---

## 🔧 Eingabeparameter

| Parameter | Typ | Beschreibung |
|----------|-----|--------------|
| **climate** | climate-Entity | Das gesteuerte TRV06-Thermostat. |
| **room_temp_sensor** | sensor | Externer Raumtemperatursensor. |
| **trv_internal_temp** | sensor | Interne TRV06-Temperatur. |
| **window_sensor** | binary_sensor | Fenster-/Türkontakt. |
| **heating_enabled** | input_boolean | Aktiviert / deaktiviert die Logik. |
| **day_time** | time | Startzeit des Tag-Modus. |
| **night_time** | time | Startzeit des Nacht-Modus. |
| **day_set_temp** | number (°C) | Zieltemperatur am Tag. |
| **night_set_temp** | number (°C) | Zieltemperatur in der Nacht. |
| **hold_offset** | number (°C) | Offset für HOLD-Setpoint. |
| **cool_offset** | number (°C) | Offset für COOL-Setpoint. |
| **overshoot_margin** | number (°C) | Schutz gegen Überhitzen. |
| **window_delay** | number (Sek.) | Verzögerung bei Fenster offen. |
| **debug** | boolean | Debug Ausgaben aktivieren. |

---

## 🧮 Interne Variablen

| Variable | Beschreibung | Berechnung |
|----------|--------------|------------|
| **nowtime** | Aktuelle Uhrzeit | `now().strftime('%H:%M:%S')` |
| **room_temp** | Aktuelle Raumtemperatur | `states(room_temp) | float(999)` |
| **trv_internal** | Interne TRV-Temp | `states(trv_temp) | float(20)` |
| **daytime** | Tag/Nacht aktiv? | `day_start <= nowtime < night_start` |
| **target_temp** | Dynamische Zieltemperatur | `day_set if daytime else night_set` |
| **hold_setpoint** | Warmhalten | `trv_internal + hold_offset` |
| **cool_setpoint** | Abkühlen | `trv_internal + cool_offset` |
| **window_open** | Fensterstatus | `is_state(window_sensor, 'on')` |

---

## ⚙️ Funktionslogik

| Modus | Bedingung | Aktion |
|-------|-----------|--------|
| **HEAT** | Raumtemp < Zieltemp | TRV auf Zieltemp setzen |
| **HOLD** | Ziel erreicht | TRV leicht über interne Temp (warm halten) |
| **COOL** | Raum über Ziel + Margin | TRV leicht unter interner Temp (abkühlen) |
| **FENSTER OFFEN** | Fenster offen | TRV → 5°C + OFF |
| **MASTER OFF** | Master=Off | TRV → 5°C + OFF |

---

## 💡 Tipps zur Nutzung

- **hold_offset = 1.0–1.5** ergibt sehr angenehmes, weiches Heizverhalten.  
- **cool_offset = –2 bis –3** verhindert effektiv Überhitzen.  
- Für maximale Präzision sollte der externe Sensor **nicht zu nah am Heizkörper** hängen.  
- Debug-Logging hilft, den Ablauf der Regelung nachzuvollziehen.

