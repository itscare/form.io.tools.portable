# form.io Wochenstatistik – Portable Version

Diese Version enthält alles, was zum Ausführen nötig ist:

- **Node.js 22** (Ordner `node`): muss **nicht** installiert werden
- **Abhängigkeiten** (Ordner `node_modules`)
- **Die Anwendung**: Dashboard, Abruf-Skripte, CLI-Werkzeuge

Enthalten sind **keine** Zugangsdaten, **keine** Datenbank und **keine** Reports.

---

## 1. Einrichtung

1. Diesen Ordner an einen beliebigen Ort entpacken, z. B. `C:\FormioStats`.
   Der Pfad sollte keine Sonderzeichen enthalten.

2. Für jede Umgebung eine Zugangsdatei anlegen:
   - `.env.example` kopieren und umbenennen, z. B. zu `.env.rps.prod`
   - Mit einem Texteditor öffnen und ausfüllen:

     ```ini
     FORMIO_BASE_URL=https://...    # Adresse des form.io-Projekts
     FORMIO_API_KEY=...             # oder FORMIO_EMAIL + FORMIO_PASSWORD
     ```

   - Bei Bedarf Zeitraum (`FORMIO_DATE_FROM` / `FORMIO_DATE_TO`) und Proxy setzen.

   **Namensschema:** `.env.<kunde>.<umgebung>`, z. B. `.env.rps.prod`, `.env.he.prod`, `.env.bw.prod`.
   Der Kundenname (`rps`, `he`, `bw`) bestimmt die Zuordnung im Dashboard.

   > **Hinweis:** Die Datei enthält Zugangsdaten. Nicht per E-Mail oder in Chats weitergeben.

---

## 2. Statistik abrufen

Doppelklick auf **`Statistik-abrufen.bat`** und die Umgebung eingeben (z. B. `rps.prod`).

Ergebnis:

- Konsolentabelle
- CSV-Datei im Ordner `Reports`
- Datenbank `stats.db` im Projektordner (wird beim ersten Abruf angelegt)

Der Abruf sollte für jeden Kunden regelmäßig laufen, z. B. über die Windows-Aufgabenplanung:

- Programm: `<Ordner>\node\node.exe`
- Argumente: `formio-weekly-stats.js --env=rps.prod`
- Starten in: `<Ordner>`

---

## 3. Dashboard starten

Doppelklick auf **`Dashboard-starten.bat`**, danach im Browser öffnen:

```
http://localhost:3000
```

- Das Dashboard liest nur aus `stats.db`. Ohne vorherigen Abruf bleibt es leer.
- Die Live-Funktionen (Formularliste, Submission-Download, Formulardefinition, Stage-Konfiguration) benötigen die jeweiligen Zugangsdateien `.env.rps.prod` usw.
- **Anderer Port:** In `Dashboard-starten.bat` die Zeile `set "PORT=3000"` ändern.

> **Achtung:** Das Dashboard hat keine eigene Anmeldung. Nur im vertrauenswürdigen Netz betreiben oder den Zugriff anderweitig absichern.

---

## 4. Weitere Befehle (Konsole)

Doppelklick auf **`Konsole-oeffnen.bat`**. Dort ist `node` bereits die mitgelieferte Version.

| Befehl | Zweck |
|---|---|
| `node formio-get-submission.js --env=rps.prod --form=<pfad> --id=<id>` | Einzelne Submission als JSON speichern (Ordner `submissions`) |
| `node formio-export-forms.js --env=rps.prod [--out=<ordner>]` | Formulardefinitionen und Aktionen exportieren |
| `node formio-export-stage.js --env=rps.prod [--out=<ordner>]` | Stage-Konfiguration exportieren |
| `node formio-delete-old-submissions.js --env=rps.prod --days=<X> --form=<pfad>` | Alte Submissions löschen. Ohne `--execute` nur Trockenlauf; vor dem Löschen wird gesichert |
| `node formio-update-year.js --env=rps.prod [--year=<jahr>]` | Abruf für alle Monate eines Jahres |
| `node formio-promote-forms.js --from=<env> --to=<env> [--dir=<ordner>] --dry-run` | Formulare zwischen Umgebungen übernehmen (nur mit exportierten Formularen) |

Die Befehle benötigen die passende Zugangsdatei (`.env.<kunde>.<umgebung>`). Ausgabeordner legen die Skripte selbst an.

---

## 5. Fehlerbehebung

| Meldung / Problem | Lösung |
|---|---|
| „Die Datei .env.<name> fehlt“ | Zugangsdatei nach Schritt 1 anlegen |
| Abbruch mit HTTP 401 / 403 | API-Key oder Login in der `.env`-Datei prüfen |
| Keine Verbindung oder Zeitüberschreitung im Firmennetz | `FORMIO_PROXY_URL` setzen |
| Dashboard zeigt keine Daten | Zuerst `Statistik-abrufen.bat` ausführen |
| Port 3000 ist belegt | Anderen Port in `Dashboard-starten.bat` eintragen |

---

## 6. Ordnerinhalt

| Eintrag | Inhalt |
|---|---|
| `node\` | Node.js-Laufzeit (portable, nicht installieren) |
| `node_modules\` | Abhängigkeiten |
| `lib\` | Datenbank- und form.io-Client-Code |
| `public\` | Dashboard-Oberfläche |
| `*.js` | Abruf- und CLI-Skripte, `server.js` |
| `*.bat` | Startdateien |
| `.env.example` | Vorlage für Zugangsdaten |
| `Reports\` | CSV-Ausgaben (entsteht beim ersten Abruf) |
| `stats.db` | Datenbank (entsteht beim ersten Abruf) |
