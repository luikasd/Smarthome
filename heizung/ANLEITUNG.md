# Heizung: ein Fenster pro Raum

**Was neu ist**

- **Übersicht nur zum Anschauen.** Für jeden Raum siehst du die Temperatur jetzt und das Ziel, dazu den Modus (Wohlfühl, Abgesenkt, Keiner da, Fenster offen, von Hand) und wann die Heizzeit wechselt. Auf der Übersicht gibt es keine Plus/Minus-Buttons mehr. Beim Scrollen verstellt sich also nichts.
- **Pro Raum ein eigenes Fenster.** Tippst du auf einen Raum, öffnet sich sein Fenster. Darin sind die drei Temperaturen *Wohlfühl*, *Abgesenkt* und *Keiner da*, die Heizzeiten (der Wochenplan) und „von Hand“ für eine kurze Änderung.
- **Neu: „Keiner da“ pro Raum.** Bisher gab es nur einen gemeinsamen Wert („Temperatur wenn keiner da“). Jetzt hat jeder Raum einen eigenen, im Kinderzimmer heißt er „Keine Kinder da“. Solange ein Raum keinen eigenen Wert hat, gilt weiter der gemeinsame.

## Einrichten (einmalig)

### 1. Fünf Helfer anlegen

Einstellungen → Geräte & Dienste → Helfer → **Helfer erstellen** → **Zahl**

| Name | wird zu |
|---|---|
| Heizung Bad abwesend | `input_number.heizung_bad_abwesend` |
| Heizung Kueche abwesend | `input_number.heizung_kueche_abwesend` |
| Heizung Wohnzimmer abwesend | `input_number.heizung_wohnzimmer_abwesend` |
| Heizung Schlafzimmer abwesend | `input_number.heizung_schlafzimmer_abwesend` |
| Heizung Kinderzimmer abwesend | `input_number.heizung_kinderzimmer_abwesend` |

Bei jedem Helfer: Minimum **5**, Maximum **25**, Schrittweite **0,5**, Maßeinheit **°C**.

> „Kueche“ bitte mit **ue** schreiben. Mit „ü“ würde Home Assistant `…_kuche_…` daraus machen, und die Automation fände den Helfer nicht. Den Anzeigenamen kannst du danach in „Küche“ ändern, die Entitäts-ID bleibt dabei gleich.

Danach **einmal** `startwerte_keiner_da.yaml` ausführen (Anleitung steht in der Datei). Die neuen Werte bekommen dann deine bisherige gemeinsame Temperatur und starten nicht bei 5 °C.

### 2. Automation ersetzen

Einstellungen → Automationen → **Heizung: Steuerung** → ⋮ → **In YAML bearbeiten** → alles durch `automation_heizung_steuerung.yaml` ersetzen → Speichern.

Geändert ist nur:
- Die Temperatur für „keiner da“ kommt jetzt aus dem Helfer des Raums. Gibt es den nicht, gilt wie bisher der gemeinsame Wert, sonst 16 °C.
- Die fünf neuen Helfer lösen eine Neuberechnung aus, wenn du sie verstellst.

### 3. Neues Dashboard anlegen

Einstellungen → Dashboards → **Dashboard hinzufügen** → **Neues Dashboard von Grund auf**. Titel: **Heizung**, die URL wird dabei automatisch `dashboard-heizung`.
Dann das Dashboard öffnen → ✏️ → ⋮ → **Raw-Konfigurationseditor** → alles markieren und durch `dashboard_heizung.yaml` ersetzen → Speichern.

Die URL muss genau `dashboard-heizung` heißen, sonst funktionieren die Zurück-Pfeile in den Raum-Fenstern nicht.

### 4. Raumplan auf das neue Dashboard zeigen lassen

Bei der Kachel **Heizung** im Raumplan an beiden Stellen
`navigation_path: /lovelace/heizung` → `navigation_path: /dashboard-heizung/uebersicht`

### 5. Aufräumen (optional)

- Alte Heizung-Ansicht im Haupt-Dashboard löschen, wenn dir das neue gefällt.
- Einstellungen → Dashboards → Heizung → „In Seitenleiste anzeigen“ aus, falls du es nur über den Raumplan öffnen willst.

## Heizzeiten ändern

Raum-Fenster → **Heizzeiten** antippen → rechts oben ⚙️ → im Wochenplan Blöcke ziehen, neue anlegen oder antippen und löschen → **Aktualisieren**.
