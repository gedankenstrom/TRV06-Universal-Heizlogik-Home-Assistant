# TRV06 – Dynamic Setpoint Heizlogik (Sensor + Abwesenheit + Live-Status)

Diese Blueprint-Automatisierung für Home Assistant bietet eine vollständig dynamische Heizlogik für das AVATTO TRV06.  
Sie nutzt externe Temperatursensoren, Abwesenheitsmodi, Fensterkontakte, intelligente Offsets – und NEU: eine Live-Status-Ausgabe in einem `input_text`.

---

## 🚀 Funktionen

- Dynamische Setpoint-Berechnung basierend auf realer Raumtemperatur  
- Tag-/Nacht-Modus mit individuellen Solltemperaturen  
- HOLD- und COOL-Offsets zur Vermeidung von Overshoot  
- Fenster-Offen-Logik mit Verzögerung  
- **Globaler & Raum-spezifischer Abwesenheitsmodus (eigene Schalter erforderlich!)**  
- LIVE-Statusausgabe des Heizmodus in einem input_text  
- Kompatibel für Multiroom-Szenarien  
- Optionales Debug-Logging aller Entscheidungen  

---

## ⚠️ WICHTIG: Abwesenheitsschalter & Automationen

Damit die Heizlogik korrekt arbeiten kann, müssen **zwei Arten von Abwesenheitsschaltern** im Home Assistant existieren:

### **1. Globaler Abwesenheits-Schalter (`input_boolean`)**
Beispiel: `input_boolean.wohnung_abwesend`

Dieser steuert die *gesamte Wohnung*.  
Der Blueprint senkt die Zieltemperatur ab, sobald dieser Schalter **ON** ist.

Typische Automationen:
- Anwesenheitserkennung über Smartphone  
- Alle Personen weg → Away ON  
- Eine Person kommt → Away OFF  

---

### **2. Raum-spezifischer Abwesenheitsschalter (`input_boolean`)**
Beispiel: `input_boolean.schlafzimmer_away`

Dieser steuert einzelne Räume unabhängig vom globalen Status.

Typische Automationen:
- Raum länger nicht betreten → Away ON  
- Bewegung erkannt → Away OFF  
- Zeitbasierte Steuerung je Raum  

**Ohne diese Schalter arbeitet der Blueprint nicht vollständig.**

---

## 🧩 Eingabeparameter / Konfiguration

| Kategorie | Parameter | Beschreibung |
|----------|-----------|--------------|
| **Grund-Entitäten** | Thermostat (climate) | TRV06 Climate-Entity |
| | Externer Raumtemperatursensor | Misst reale Raumtemperatur |
| | TRV interne Temperatur | Sensor im Thermostat |
| | Fenster-/Türkontakt | Schaltet Heizung bei offenem Fenster aus |
| | Master EIN/AUS | Aktiviert/Deaktiviert die gesamte Logik |
| **Live-Status** | Status-Textausgabe | `input_text` für Live-Heizmodus |
| **Tag/Nacht-Zeiten** | Start Tag | Uhrzeit für Tagbetrieb |
| | Start Nacht | Uhrzeit für Nachtbetrieb |
| | Tag-Solltemperatur | Tagesziel |
| | Nacht-Solltemperatur | Nachtziel |
| **Offsets** | HOLD Offset | TRV-Setpoint zum Halten |
| | COOL Offset | TRV-Setpoint zum Abkühlen |
| | Overshoot-Marge | Bereich über Zieltemperatur |
| **Fensterlogik** | Fenster-Verzögerung | Zeit bis zur Abschaltung |
| **Globale Abwesenheit** | Away Mode | Globale Abwesenheit |
| | Away Offset | Absenkung bei globaler Abwesenheit |
| **Raum-Abwesenheit** | Room Away Mode | Raum-Abwesenheit |
| | Room Away Offset | Zusätzliche Absenkung |
| **Debug** | Debug aktivieren | Loggt die vollständige Entscheidung |

---

## 🔥 Funktionsübersicht

### **1. Master OFF**
Wenn Master-Schalter aus:  
→ TRV auf 5°C  
→ Thermostat komplett ausgeschaltet  
→ Live-Status zeigt „MASTER AUS“

---

### **2. Fenster offen**
Wenn Fenster offen erkannt:

1. Verzögerung  
2. erneute Prüfung  
3. TRV auf 5°C  
4. Thermostat AUS  
5. Live-Status: „Fenster offen – AUS“

---

### **3. Normalbetrieb: Dynamische Setpoints**

Der Blueprint berechnet:

- Zieltemperatur (inkl. Tag/Nacht + Away-Absenkungen)  
- HOLD-Setpoint (Halten)  
- COOL-Setpoint (Abkühlen)  

| Bedingung | Aktion |
|-----------|--------|
| Raumtemp < Zieltemp | **HEIZEN** → TRV = target_temp |
| Ziel ≤ Raumtemp < Ziel+Overshoot | **HALTEN** → TRV = hold_setpoint |
| Raumtemp > Ziel + Overshoot | **ABKÜHLEN** → TRV = cool_setpoint |

Der aktive Modus wird **live in das input_text geschrieben**.

---

## 🧪 Debug-Modus

Wenn aktiviert, schreibt der Blueprint Einträge über:

- Raumtemperatur  
- TRV-Temperatur  
- Zieltemperatur  
- HOLD / COOL Setpoints  
- Zeit (Tag/Nacht)  
- Fensterstatus  
- Away / Room Away  
- Aktueller Modus  

---

## 📘 Beispielwerte

| Einstellung | Beispiel |
|------------|----------|
| Tagtemperatur | 22°C |
| Nachttemperatur | 18°C |
| HOLD Offset | 1°C |
| COOL Offset | -1°C |
| Overshoot | 0.1°C |
| Fenster Delay | 60s |

---

## 🧡 Live-Status (input_text)

Der Blueprint schreibt automatisch Werte wie:

- `HEIZEN – Ziel 22°C | TRV 21.5°C`  
- `HALTEN – Ziel 21°C | HOLD 22°C`  
- `ABKÜHLEN – Ziel 22°C | COOL 20°C`  
- `MASTER AUS – Raum 18°C`  
- `FENSTER OFFEN – Aus`  

Dieser Status kann im Dashboard angezeigt werden.

---

## 📄 Lizenz

Frei verwendbar. Verbesserungen und Pull Requests sind willkommen!  

---

## ❤️ Support

Wenn dir der Blueprint hilft, freue ich mich über ein ⭐ auf GitHub!
