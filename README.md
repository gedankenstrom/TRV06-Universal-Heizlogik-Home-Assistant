# TRV06 Universal Heizlogik für Home-Assistant
Universelle Heizlogik für TRV06 Thermostate in Home Assistant mit externer Temperaturregelung, Tag-/Nachtmodus, Hysterese, Fenstererkennung und vollständig einstellbaren Parametern. Kompatibel mit Home Assistant 2025.

## 🔧 Eingaben & Optionen

| Eingabe | Typ | Beschreibung |
|---|---|---|
| Thermostat | `climate` | Zu steuerndes Heiz-Thermostat |
| Raumtemperatur-Sensor | `sensor` | Gelieferte Ist-Temperatur |
| Tür-/Fenstersensor | `binary_sensor` | `on` = offen |
| Heizlogik EIN/AUS | `input_boolean` | Master-Schalter |
| Start TAG | `time` | Standard: 08:00 |
| Start NACHT | `time` | Standard: 22:00 |
| Tag-Temperatur | `number` | Sollwert bei Tag |
| Nacht-Temperatur | `number` | Sollwert bei Nacht |
| Tag – Einschalten unter | `number` | unter diesem Wert → Heizen EIN |
| Tag – Ausschalten über | `number` | über diesem Wert → Heizen AUS |
| Nacht – Einschalten unter | `number` | Nacht-Hysterese EIN |
| Nacht – Ausschalten über | `number` | Nacht-Hysterese AUS |
| Fensterverzögerung | `number` (Sek.) | Standard: 60 s |
| Debug aktivieren | `boolean` | Log-Ausgabe aktivieren |

---

## 🏁 Ablauf-Logik (Kurzfassung)

1. **Home Assistant startet → 3 min Delay**
2. Wenn **Master AUS** → 5 °C + OFF + Stop
3. Wenn **Fenster offen** → Delay → prüfen → 5 °C + OFF
4. Wenn **Tag**:
   - `room_temp < low_day` → ON → 3 s → Set Temp
   - `room_temp > high_day` → 5 °C → OFF
5. Wenn **Nacht**:
   - `room_temp < low_night` → ON → 3 s → Set Temp
   - `room_temp > high_night` → 5 °C → OFF

---

## 📝 Hinweise

- Hysterese-Werte bewusst eng, aber stabil
- funktioniert **ohne climate-mode-Wechsel**
- keine Dauer-Trigger durch Restart-Schutz
- sicher gegen `unknown` / `unavailable` Sensorwerte

---

## ✅ Kompatibel mit

- TRV06 Heizkörper-Thermostaten
- allen Home Assistant `climate`-Geräten
- Fenster-/Türsensoren (binary_sensor)
- jeder Raum-Temperaturquelle (`sensor`)



