# 🚀 TRV06 – Dynamic Setpoint Heizlogik  
### *Intelligente, sensorbasierte Heizungssteuerung für AVATTO TRV06 Thermostate (Home Assistant)*

[![Blueprint](https://img.shields.io/badge/Home%20Assistant-Blueprint-blue)]()  
[![Version](https://img.shields.io/badge/Version-1.0.0-green)]()  
[![Maintained](https://img.shields.io/badge/Maintained-yes-success)]()  

Dieses Blueprint implementiert eine **vollautomatische Heizlogik** für AVATTO TRV06 Thermostate.  
Es ersetzt die ungenaue TRV-Temperaturmessung durch *externe Sensorwerte*, berücksichtigt Fensterkontakte, Tageszeiten, Abwesenheit und erzeugt eine **Live-Statusanzeige** fürs Dashboard.

---

## 🌟 Features

### 🔥 Intelligente Temperaturregelung
- Externer Raumtemperatursensor als **präzise Hauptquelle**  
- TRV-interne Temperatur zur Berechnung dynamischer Offsets  
- Automatische Betriebsmodi:
  - **HEIZEN**  
  - **HALTEN** (HOLD-Offset)  
  - **COOL** (COOL-Offset)  

### 🕒 Tag-/Nacht-Logik
- Freie Startzeiten  
- Eigene Zieltemperatur für Tag & Nacht  

### 🚪 Fenster-Offen-Steuerung
- Verzögerung einstellbar  
- Danach: TRV auf **5°C** & **AUS**

### 🏠 Abwesenheit
- Global-Away (gesamte Wohnung)  
- Room-Away (nur Raum)  
- Global-Away überschreibt Room-Away  
- Absenkung nur tagsüber  

### ⚙️ Master EIN/AUS
- Globaler Override für das gesamte System  

### 📊 Live-Status
Zustandsanzeige per `input_text`:

HEIZEN • 22.0°C
HALTEN • 20.8°C (bis 22.2°C)
COOL • 19.0°C (ab 22.2°C)
AUS • Fenster/Tür offen
AUS • Master OFF

### 🐛 Debug-Modus
Ausführliche Logbuch-Ausgaben zur Analyse.

---

## 📥 Benötigte Entitäten

| Zweck | Typ | Beispiel |
|-------|------|----------|
| Thermostat | `climate` | AVATTO TRV06 |
| Raumtemperatur | `sensor` | Zigbee/BLE Sensor |
| TRV interne Temperatur | `sensor` | TRV06 internal |
| Fensterkontakt | `binary_sensor` | Fenster/Tür |
| Master EIN/AUS | `input_boolean` | heating_enabled |
| Statusausgabe | `input_text` | heiz_status |
| Global-Away | `input_boolean` | away_mode |
| Room-Away | `input_boolean` | room_away |

---

## 🛠️ Changelog

### v1.0.0
- Erstveröffentlichung  
- Volle Heizlogik + Fensterlogik  
- HOLD / COOL Offsets  
- Global-/Room-Away  
- Live-Status  
- Debug-Modus  

---

## 📄 Lizenz

MIT License  
