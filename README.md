# TRV06 – Dynamic Setpoint Heizlogik (Sensor + Abwesenheit)

Diese Blueprint-Automatisierung für Home Assistant bietet eine vollständig dynamische Heizlogik für das AVATTO TRV06.  
Sie nutzt externe Temperatursensoren, Abwesenheitsmodi, Fensterkontakte sowie intelligente Offsets, um präzise und effiziente Heizungssteuerung zu ermöglichen.

---

## 🚀 Funktionen

- Dynamische Setpoint-Berechnung basierend auf realer Raumtemperatur  
- Tag-/Nacht-Modus mit individuellen Solltemperaturen  
- HOLD- und COOL-Offsets zur Vermeidung von Overshoot  
- Fenster-Offen-Logik mit Verzögerung  
- **Globaler & Raum-spezifischer Abwesenheitsmodus (eigene Schalter erforderlich!)**  
- Kompatibel für Multiroom-Szenarien  
- Optionales Debug-Logging aller Entscheidungen

---

## ⚠️ WICHTIG: Abwesenheitsschalter & Automationen

Damit die Heizlogik korrekt arbeiten kann, müssen **zwei Arten von Abwesenheitsschaltern im Home Assistant angelegt werden**:

### **1. Globaler Abwesenheits-Schalter (`input_boolean`)**
Beispiel:  
- `input_boolean.wohnung_abwesend`

Dieser Schalter dient als *globaler Zustand* deiner Wohnung.  
Der Blueprint senkt die Zieltemperatur ab, sobald dieser Schalter auf **ON** steht.

Typische Automationen:
- **Anwesenheitserkennung per Smartphone**  
- **Alle Personen weg → Abwesenheit ON**  
- **Jemand kommt heim → Abwesenheit OFF**

### **2. Raum-spezifischer Abwesenheits-Schalter (`input_boolean`)**
Beispiel:  
- `input_boolean.buero_away`  
- `input_boolean.schlafzimmer_away`

Damit kann jeder Raum individuell abgesenkt werden – unabhängig von der globalen Abwesenheit.

Typische Automationen:
- Raum wird lange nicht betreten → Raum-Abwesenheit ON  
- Bewegung im Raum → Raum-Abwesenheit OFF  
- Zeitbasierte Reduktion (z. B. tagsüber im Schlafzimmer)

Ohne diese Schalter arbeitet der Blueprint **nicht vollständig**, also unbedingt anlegen!

---

## 🧩 Eingabeparameter / Konfiguration

| Kategorie | Parameter | Beschreibung |
|----------|-----------|--------------|
| **Grund-Entitäten** | Thermostat (climate) | TRV06 Climate-Entity |
| | Externer Raumtemperatursensor | Misst reale Raumtemperatur |
| | TRV interne Temperatur | Sensorwert des TRV06 |
| | Fenster-/Türkontakt | Schaltet Heizung bei offenem Fenster ab |
| | Master EIN/AUS | Aktiviert/Deaktiviert die gesamte Logik |
| **Tag/Nacht-Zeiten** | Start Tag | Uhrzeit für Tagbetrieb |
| | Start Nacht | Uhrzeit für Nachtbetrieb |
| | Tag-Solltemperatur | Zieltemperatur tagsüber |
| | Nacht-Solltemperatur | Zieltemperatur nachts |
| **Offsets** | HOLD Offset | TRV Setpoint zum Halten |
| | COOL Offset | TRV Setpoint zum Abkühlen |
| | Overshoot-Marge | Bereich über Zieltemperatur |
| **Fensterlogik** | Fenster-Verzögerung | Zeit bis zum Abschalten |
| **Globale Abwesenheit** | Away Mode | input_boolean für Abwesenheit |
| | Away Offset | Absenkung bei globaler Abwesenheit |
| **Raum-Abwesenheit** | Room Away Mode | Raum-spezifischer Abwesenheitsschalter |
| | Room Away Offset | Zusätzliche Absenkung |
| **Debug** | Debug aktivieren | Loggt alle Berechnungen |

---

## 🔥 Funktionsübersicht

### **1. Master OFF**
Wenn der Master-Schalter deaktiviert ist:  
→ Setzt TRV auf **5°C** und schaltet es aus.

---

### **2. Fenster offen**
Wenn Fenster geöffnet wird:

1. Warten (Fenster-Verzögerungszeit)  
2. Erneute Prüfung  
3. TRV auf **5°C** → Thermostat AUS  

---

### **3. Normalbetrieb – Dynamische Setpoints**

Der Algorithmus berechnet:

- Zieltemperatur (Tag/Nacht + Abwesenheit)  
- HOLD-Setpoint (intern + Offset)  
- COOL-Setpoint (intern + Offset)

| Bedingung | Aktion |
|-----------|--------|
| Raumtemp < Zieltemp | **HEIZEN** → Setpoint = target_temp |
| Raumtemp zwischen Ziel & Overshoot | **HALTEN** → Setpoint = hold_setpoint |
| Raumtemp > Ziel + Overshoot | **COOL** → Setpoint = cool_setpoint |

---

## 🧪 Debug-Modus

Wenn aktiviert, schreibt die Automation Logeinträge mit:

- Raumtemperatur  
- TRV intern  
- berechnete Zieltemperaturen  
- aktiver Modus (HEAT / HOLD / COOL)  
- Abwesenheit  
- Zeitstempel  

---

## 📘 Beispielwerte

| Einstellung | Beispiel |
|------------|----------|
| Tagtemperatur | 22°C |
| Nachttemperatur | 18°C |
| HOLD Offset | +1°C |
| COOL Offset | –1°C |
| Overshoot | 0.1°C |
| Fenster Delay | 60s |

---

## 📄 Lizenz

Frei verwendbar. Verbesserungen und Pull Requests sind willkommen!  

---

## ❤️ Support

Wenn dir der Blueprint hilft, freue ich mich über ein ⭐ auf GitHub!
