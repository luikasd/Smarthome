# Kamera Keller: großes Bild und Schwenken

**Was neu ist**

- Wenn du in der Keller-Ansicht auf das Kamerabild tippst, öffnet sich eine **eigene Seite „Kamera Keller“** statt des kleinen Pop-up-Fensters.
- Dort ist das Bild groß, und darunter sind drei Buttons: **Links**, **Geradeaus** und **Rechts**.
  - Links/Rechts dreht die Kamera pro Tipp ein Stück (ca. 0,6 Sekunden). Wenn du mehrmals tippst, dreht sie entsprechend weiter.
  - Geradeaus startet die Kalibrierung. Die Kamera dreht sich dabei einmal ganz herum und schaut danach wieder geradeaus. Vorher kommt eine Rückfrage.

## Warum das große Bild bisher nicht kam

Das liegt nicht am Browser-Cache. Die offizielle Tuya-Integration holt das Video über die **Tuya-Cloud**, und dabei bekommt jeder Aufruf eine neue Stream-Adresse. Die Keller-Ansicht zeigt schon einen Live-Stream. Tippst du darauf, will das Pop-up einen **zweiten** Stream von derselben Kamera aufbauen. Damit kommt die Tuya-Cloud nicht zurecht, und es gibt einen Fehler (oder es dauert ewig).

Die Tuya-App verbindet sich direkt mit der Kamera, nicht über diesen Cloud-Stream. Deshalb ist das Bild dort sofort da.

Die neue Seite löst das: Wenn du dorthin wechselst, wird die Keller-Ansicht geschlossen und ihr Stream beendet. Es läuft also immer nur **ein** Stream.

## Warum ein Zusatz nötig ist

Die offizielle Tuya-Integration in Home Assistant hat **keine** Schwenk-Steuerung. Das habe ich im aktuellen Quellcode nachgeprüft: Für Kameras gibt es dort nur Schalter wie Nachtsicht oder Bewegungserkennung, aber nichts zum Drehen. Die kostenlose HACS-Integration **Tuya Local** kann es dagegen. Sie bringt für Tuya-Schwenkkameras fertige Buttons mit („PTZ left“, „PTZ right“, „PTZ stop“, „PTZ calibration“).

Die offizielle Tuya-Integration bleibt trotzdem drin, das Videobild kommt weiter von ihr. Tuya Local ist nur fürs Drehen da.

## Einrichten (einmalig)

### 1. Tuya Local installieren

1. HACS → Suche **„Tuya Local“** → Herunterladen → Home Assistant neu starten.
2. Einstellungen → Geräte & Dienste → **Integration hinzufügen** → **Tuya Local**.
3. Anmeldung über die **Smart Life / Tuya App (QR-Code)** wählen. Dann holt sich Tuya Local den lokalen Schlüssel der Kamera selbst.
4. Die Kamera im Keller auswählen. Wenn Tuya Local fragt, welcher Gerätetyp es ist, nimm den Vorschlag mit **„PTZ“** oder **„Camera“** im Namen.
5. Als Namen **„Kamera Keller“** eingeben.

### 2. Entitäten nachschauen

Einstellungen → Geräte & Dienste → Tuya Local → Gerät „Kamera Keller“. Unter **Konfiguration** stehen die PTZ-Buttons. Mit dem Namen „Kamera Keller“ sollten sie so heißen:

| Button | erwartete entity_id |
|---|---|
| PTZ left | `button.kamera_keller_ptz_left` |
| PTZ right | `button.kamera_keller_ptz_right` |
| PTZ stop | `button.kamera_keller_ptz_stop` |
| PTZ calibration | `button.kamera_keller_ptz_calibration` |

Am besten einmal direkt auf **PTZ right** drücken und dann auf **PTZ stop**. Wenn die Kamera sich dreht und wieder anhält, passt alles.

Heißen sie bei dir anders, trag die richtigen Namen im Skript unter `variables:` ein (Zeilen mit `# TODO`).

### 3. Skript anlegen

Einstellungen → Automationen & Szenen → **Skripte** → Skript erstellen → ⋮ → **In YAML bearbeiten** → Inhalt von `script_kamera_keller_schwenken.yaml` ab `alias:` einfügen → Speichern. Als Skript-ID **`kamera_keller_schwenken`** eintragen.

### 4. Dashboard

In `dashboard_kamera_keller.yaml` stehen zwei Teile:

- **Teil 1** (neue Seite): Dashboard → ✏️ → ⋮ → **Raw-Konfigurationseditor** → ganz unten als letzte Ansicht unter `views:` einfügen.
  - `camera.kamera_keller` durch die echte entity_id deiner Kellerkamera ersetzen. Die findest du in deiner Keller-Ansicht in der Kamerakarte unter `entity:`.
  - `back_path: /lovelace/keller` auf den Pfad deiner Keller-Ansicht ändern (steht dort unter `path:`), damit der Zurück-Pfeil dorthin führt.
- **Teil 2**: In der Keller-Ansicht die Kamerakarte bearbeiten und die `tap_action` durch das Navigieren zur neuen Seite ersetzen.

## Wenn's nicht passt

- **Dreht pro Tipp zu wenig oder zu viel:** Im Skript `schritt_sekunden: 0.6` anpassen (z. B. 0.3 oder 1.0).
- **Tuya Local findet keine PTZ-Buttons:** Dann kennt Tuya Local das Kameramodell noch nicht. Schick mir das Modell aus der Tuya-App (Gerät → ✏️ → Geräteinformationen), dann suchen wir weiter.
- **Bild lädt auch auf der neuen Seite langsam:** Das liegt an der Tuya-Cloud, die braucht oft 5–15 Sekunden. Viel schneller und ohne Cloud geht es, wenn die Kamera **RTSP oder ONVIF** kann. Schau in der Tuya-App in den Kamera-Einstellungen nach. Mit der ONVIF-Integration hättest du dann ein lokales Bild, das sofort da ist, und auch Schwenken über `onvif.ptz`, ganz ohne Tuya Local.
