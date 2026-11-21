# Heizung: ein Fenster pro Raum

**Was neu ist**

- **Die Heizung-Seite ist nur noch zum Anschauen.** Pro Raum siehst du eine Zeile, zum Beispiel „Bad · 20,5 °C → 21 °C · Wohlfühl · Heizzeit bis 22:00“. Darunter ist eine Kachel pro Raum, und Antippen öffnet das Fenster des Raums. Die Plus/Minus-Buttons und Schieberegler sind von der Seite verschwunden, beim Scrollen verstellt sich also nichts mehr. Auch „Automatik“ schaltet beim Antippen nicht mehr direkt um, sondern öffnet erst die Detailansicht.
- **Jeder Raum hat ein eigenes Fenster.** Darin findest du:
  - Wohlfühl, Abgesenkt und **Keiner da**
  - die Heizzeit (Wochenplan)
  - „von Hand“ mit Plus/Minus
  - Fenster-offen- und Handbetrieb-Hinweis
  - Ventil und Wärmebedarf
- **Allgemein** (Raumthermometer, Vorheizen vor dem Wecker, Schnellheizen) hat ebenfalls ein eigenes Fenster.
- **Neu: „Keiner da“ pro Raum.** Bisher gab es nur den gemeinsamen Wert „Wenn keiner da“. Jetzt hat jeder Raum einen eigenen, im Kinderzimmer heißt er „Keine Kinder da“. Solange es den Wert für einen Raum noch nicht gibt, gilt der gemeinsame.
- **Raumplan:** Tippst du im Grundriss auf eine Temperatur, öffnet sich das Fenster des Raums. Wenn du sie gedrückt hältst, kommt wie bisher der Thermostat-Regler.
- Licht, Saugroboter und alles andere im Raumplan sind unverändert.

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

Danach **einmal** `startwerte_keiner_da.yaml` ausführen (Anleitung steht in der Datei). Die neuen Werte bekommen dann deine bisherige Temperatur „Wenn keiner da“ und starten nicht bei 5 °C.

### 2. Automation ersetzen

Einstellungen → Automationen → **Heizung: Steuerung** → ⋮ → **In YAML bearbeiten** → alles durch `automation_heizung_steuerung.yaml` ersetzen → Speichern.

Geändert ist nur:
- Die Temperatur für „keiner da“ kommt jetzt aus dem Helfer des Raums. Gibt es den nicht, gilt wie bisher „Wenn keiner da“, sonst 16 °C.
- Die fünf neuen Helfer lösen eine Neuberechnung aus, wenn du sie verstellst.

### 3. Dashboard ersetzen

Dashboard mit dem Raumplan öffnen → ✏️ Bearbeiten → ⋮ → **Raw-Konfigurationseditor** → alles markieren und durch `dashboard/haupt_dashboard.yaml` ersetzen → Speichern.

Die Heizung-Kachel im Raumplan musst du nicht anfassen, sie führt weiter nach `/lovelace/heizung`.

**Zurück zum alten Stand:** Genauso vorgehen, aber `dashboard/haupt_dashboard_vorher.yaml` einfügen.

## Heizzeiten ändern

Raum-Fenster → **Heizzeit** antippen → rechts oben ⚙️ → im Wochenplan Zeitblöcke ziehen, neue anlegen oder antippen und löschen → **Aktualisieren**.
