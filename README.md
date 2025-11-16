# TRV06 Universal Heizlogik für Home-Assistant
Universelle Heizlogik für TRV06 Thermostate in Home Assistant mit externer Temperaturregelung, Tag-/Nachtmodus, Hysterese, Fenstererkennung und vollständig einstellbaren Parametern. Kompatibel mit Home Assistant 2025.


Diese Blueprint bietet eine universelle, flexible und vollständig automatisierte Heizlogik für TRV06-Thermostate unter Home Assistant.
Die Regelung basiert ausschließlich auf einem externen Raumtemperatursensor, wodurch eine sehr präzise und stabile Temperatursteuerung erreicht wird.
Die Blueprint ist zu 100% kompatibel mit Home Assistant 2025 und nutzt keinerlei veraltete Services.

⸻

🔧 Funktionen
	•	Externer Raumtemperatursensor als Grundlage der Regelung
	•	Tag-/Nachtmodus mit frei einstellbaren Umschaltzeiten
	•	Automatisches Setzen der Solltemperaturen (Tag & Nacht)
	•	Hysterese-Steuerung
	•	Ein: unter definierter Temperatur
	•	Aus: über definierter Temperatur
	•	Fenster-/Türerkennung mit einstellbarer Verzögerung
	•	Master-Schalter zum Deaktivieren der kompletten Heizlogik
	•	Debug-Logging (optional)
	•	Schutz vor unavailable/unknown-Sensorwerten
	•	Startverzögerung nach Home Assistant-Neustart
	•	Restart-Modus, damit Zustandsänderungen nicht verpasst werden

⸻

📥 Installation
	1.	Repository öffnen
	2.	Die Datei blueprint.yaml herunterladen
	3.	In Home Assistant öffnen:
Einstellungen → Automatisierungen & Szenen → Blueprints → Importieren
	4.	YAML einfügen oder URL importieren
	5.	Blueprint speichern
	6.	Neue Automatisierung aus der Blueprint erstellen

⸻

📌 Benötigte Eingaben

Thermostat (climate) TRV06-Thermostat, das geregelt wird
Externer Temperatursensor (sensor) Raumtemperaturquelle für Hysterese & Steuerung
Kontakt-Sensor (binary_sensor) Fenster-/Türsensor, löst Heizungsabschaltung aus
Master-Schalter (input_boolean) Aktiviert/Deaktiviert die gesamte Heizlogik
Tag-/Nachtzeiten Uhrzeiten für automatische Umschaltung
Solltemperaturen Tag/Nacht Zieltemperaturen
Hysterese-Grenzwerte Wert für Ein- und Ausschaltlogik
Fensterverzögerung Sekunden bis zum Abschalten bei offenem Fenster
Debug-Logging Optional, schreibt Werte ins Logbuch


🧠 Funktionsweise kurz erklärt
	1.	Die Automation startet bei Zeitwechseln, Temperaturänderungen, Fensterstatus oder Home Assistant-Start.
	2.	Bei offenem Fenster wird die Heizung nach einer definierbaren Verzögerung ausgeschaltet.
	3.	Je nach Zeit (Tag/Nacht) wird die passende Solltemperatur gesetzt.
	4.	Die Hysterese entscheidet, ob die Heizung eingeschaltet oder ausgeschaltet wird.
	5.	Der Master-Schalter deaktiviert die gesamte Logik inklusive Thermostat.

Die Regelung nutzt immer den externen Temperatursensor, nicht den internen TRV06-Sensor.

⸻

📝 Hinweise
	•	Empfohlen wird ein guter, zentral platzierter Raumtemperatursensor.
	•	Die Hysterese sollte so gewählt werden, dass das Thermostat nicht taktet.
	•	Die Blueprint kann problemlos auf mehrere Räume angewendet werden.
