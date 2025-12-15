# TRV06 – Dynamic Setpoint Heizlogik  
**Sensor-basiert · Abwesenheit · Lüften · Live-Status**

## 🔥 Überblick

Dieses Home-Assistant-Blueprint implementiert eine **vollautomatische, intelligente Heizlogik** für **AVATTO TRV06 Thermostate**.

Im Gegensatz zur internen TRV-Regelung basiert die Steuerung auf einem **externen Raumtemperatursensor**, kombiniert mit:

- Zeitprofilen (Morgen / Mittag / Nacht)
- Dynamischen HOLD- und COOL-Offsets
- Fenster- und Lüftungserkennung
- Globaler und lokaler Abwesenheit
- Master-Schalter für die komplette Heizlogik
- Live-Statusausgabe für Dashboards
- Optionalem Debug-Logging

> **Hinweis:**  
> Room-Away wird automatisch ignoriert, wenn Global-Away aktiv ist.  
> Nachts gibt es keine zusätzliche Absenkung – es gilt ausschließlich die Nacht-Temperatur.

---

## ✨ Features

### 🌡️ Intelligente Temperaturregelung

- Externer Raumfühler als **alleinige Entscheidungsgrundlage**
- Dynamische TRV-Setpoints:
  - **HEIZEN** → Zieltemperatur
  - **HALTEN** → `TRV_intern + Hold-Offset`
  - **COOL** → `TRV_intern + Cool-Offset`
- Overshoot-Schutz über frei definierbare Marge

### ⏰ Zeitbasierte Modi

- Morgen
- Mittag
- Nacht
- Frei konfigurierbare Startzeiten und Solltemperaturen

### 🏠 Abwesenheit (optional)

- Global Away (Wohnung)
- Room Away (einzelner Raum)
- Wirkt **nur tagsüber**
- Priorität:
  1. Global Away
  2. Room Away
- Individuelle Absenkungen

### 🚪 Lüften / Fenster offen

- Externer Helfer (`input_boolean` oder `binary_sensor`)
- Bei Aktivierung:
  - Setpoint auf 5 °C
  - Thermostat AUS
- Debounce und Verzögerungen bewusst **außerhalb** des Blueprints

### 🧠 Master-Logik

- Globaler Heizung EIN/AUS-Schalter
- OFF = hartes Abschalten inkl. Sicherheits-Setpoint

### 📟 Live-Statusausgabe

- Klarer Status im `input_text`
- Ideal für Lovelace-Dashboards

Beispiele:
- `HEIZEN • 22.0 °C`
- `HALTEN • 21.2 °C`
- `COOL • 19.5 °C`
- `AUS • Lüften`
- `AUS • Master OFF`

### 🐞 Debug-Modus

- Vollständige Entscheidungslogik im Logbuch
- Ideal zum Verstehen und Feinjustieren

---

## 🔧 Voraussetzungen

- AVATTO TRV06 Thermostat (`climate`)
- Externer Raumtemperatursensor (`sensor`)
- Benötigte Helfer:
  - `input_boolean` (Master, Away, Room-Away)
  - `input_text` (Statusanzeige)
  - Optional: `binary_sensor` oder `input_boolean` für Lüften

---

## 🧩 Benötigte Entitäten

### Pflicht

- Climate-Entity des TRV06  
- Externer Raumtemperatursensor  
- TRV interne Temperatur  
- Master-Schalter (`input_boolean`)  
- Status-Text (`input_text`)  

### Optional

- Lüften-Blocker
- Global Away
- Room Away

---

## ⚙️ Logik-Prioritäten

1. Debug Logging (optional)  
2. Master OFF  
3. Lüften / Fenster  
4. Normalbetrieb  
   - Heizen  
   - Halten  
   - Cool  

---

## 🧠 Entscheidungslogik (vereinfacht)

```text
Master OFF?
 └─► TRV AUS

Lüften aktiv?
 └─► TRV AUS

Raum < Ziel?
 └─► HEIZEN

Raum ≥ Ziel und < Ziel + Overshoot?
 └─► HALTEN

Raum ≥ Ziel + Overshoot?
 └─► COOL
```

---

## 🛠️ Typische Einsatzszenarien

- Ungenaue TRV-Messung direkt am Heizkörper
- Mehrere Heizkörper und ein externer Raumfühler
- Abwesenheitsabsenkung ohne Zeitpläne
- Saubere Lüftungserkennung
- Heizstatus im Dashboard

---

## 📌 Hinweise & Best Practices

- Fenster- und Tür-Erkennung **immer extern**
- Nacht-Temperatur bewusst wählen (keine Away-Absenkung)
- HOLD- und COOL-Offsets schrittweise feinjustieren
- Debug-Modus nur temporär aktivieren

---

## 📄 Lizenz

Frei nutzbar für private Home-Assistant-Installationen.  
