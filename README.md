# TRV06 – Dynamic Setpoint Heating Logic (Sensor + Away + Live Status)

Dieser Home-Assistant-Blueprint bietet eine intelligente Heizlogik für das  
**AVATTO TRV06 Thermostat**, basierend auf:

- externen Temperatursensoren  
- Tag-/Nachtprofil  
- globaler & raumspezifischer Abwesenheit  
- Fenster-Offen-Erkennung  
- TRV-internen Temperatur-Offsets  
- dynamischen Setpoints: **HEIZEN / HALTEN / COOL**  
- Live-Statusausgabe in `input_text`

---

## 🚀 Features

### 🔥 Dynamischer Setpoint
Automatische Entscheidung zwischen:
- **HEIZEN** → Zieltemperatur erreichen  
- **HALTEN** → Temperatur stabil halten  
- **COOL** → leicht abkühlen, Overshoot vermeiden  

### 🌗 Tag-/Nachtbetrieb  
- Tagsüber: volle Regelung + Abwesenheit aktiv  
- Nacht-Setpoint wirkt stabil und ohne Eingriffe

### 🧭 Anwesenheitslogik
- **Global Away** (z. B. Wohnung verlassen)
- **Room Away** (z. B. Raum längere Zeit unbenutzt)

### 🪟 Fenster-Offen-Logik
- Verzögerung konfigurierbar  
- TRV schaltet auf 5°C + OFF  
- Statusmeldung: `AUS – Fenster/Tür offen`

### 📝 Live-Status
Wird in `input_text` gespeichert, z. B.:

- `HEIZEN – 22.0°C`
- `HALTEN – 21.0°C (bis 22.1°C)`
- `COOL – 19.0°C (ab 22.1°C)`
- `AUS – Master OFF`
- `AUS – Fenster/Tür offen`

---

## 🧩 Voraussetzungen

| Typ | Beschreibung |
|-----|--------------|
| `climate.xxx` | Dein TRV06 |
| Sensor | Raumtemperatur |
| Sensor | TRV interne Temperatur |
| `binary_sensor` | Fenster-/Türkontakt |
| `input_boolean` | Globaler Away |
| `input_boolean` | Raum-Away |
| `input_boolean` | Master EIN/AUS |
| `input_text` | Ausgabe des Live-Status |

---

## 🧪 Debugging

Optionaler Debug-Modus loggt:

- Raumtemperatur  
- TRV-intern  
- Zieltemperatur  
- HOLD / COOL Setpoints  
- Anwesenheitsstatus  
- Zeitprofil (Tag/Nacht)  
- Fensterstatus  

---

## 📄 Changelog

### **v1.0.0**
- Erstes vollständiges Release  
- Abwesenheit wirkt nur tagsüber  
- Verbessertes COOL-Verhalten  
- Überarbeitete Live-Statusausgabe  
- Stabilere Setpoint-Berechnung  

---

## ❤️ Support
Wenn dir der Blueprint hilft, gib dem Projekt gerne ein ⭐ auf GitHub!
