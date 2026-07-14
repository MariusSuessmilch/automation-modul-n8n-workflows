# n8n-Workflows · Automation Modul

Alle Beispiel-Workflows aus dem Automation-Modul der KI-Manager-Ausbildung, sortiert nach Kurstag. Jede Datei ist ein vollständiger n8n-Workflow zum Importieren.

## So importierst du einen Workflow

1. Öffne die gewünschte `.json`-Datei hier auf GitHub und lade sie herunter (Button **Download raw file** oben rechts in der Dateiansicht).
2. In n8n einen neuen, leeren Workflow anlegen.
3. Menü oben rechts (**⋮**) → **Import from File…** → die heruntergeladene Datei wählen.
4. Speichern. Fertig.

Alternativ ohne Download: Dateiinhalt auf GitHub kopieren (Button **Copy raw file**), in n8n auf den leeren Canvas klicken und mit `Strg+V` / `Cmd+V` einfügen.

## Was ist drin?

| Ordner | Inhalt |
| --- | --- |
| Tag 01 | Mini-Übung: dein erster Workflow (Manual Trigger + Edit Fields) |
| Tag 02 | Die 5 „Nodes in Aktion"-Demos, IF- und Split-Out-Extras, Tagesgruß-Mail, API-Response lesen |
| Tag 03 | API-Keys-Demo (3 Varianten), Datenquellen (OpenWeather, RSS, Google Sheets), NASA-APOD-Übung |
| Tag 04 | E-Mail-Klassifizierung mit KI (Basis + Ausbau mit Switch-Routing) |
| Tag 05 | Zielbild der Claude-in-Chrome-Demo, Recherche-Agent (AI Agent Node + Tools) |
| Tag 06 | Multi-Agent-Content-Team (19 Nodes, Korrektur-Schleife + Human-in-the-Loop), Feedback-Formular |
| Tag 07 | Memory-Demos: Kurzzeit (Session) vs. Langzeit (persistente Data Table) |

## Hinweise

- **Credentials:** Die Workflows referenzieren die Zugänge des Kurs-Servers (z. B. „KIMA OpenAi account", „SerpAPI"). In einer eigenen n8n-Instanz musst du nach dem Import eigene Credentials anlegen und in den betroffenen Nodes auswählen. Die Dateien enthalten **keine** API-Keys oder Passwörter.
- **Send-Email-Nodes** sind bewusst ohne SMTP-Zugang gespeichert und zeigen nach dem Import ein Warndreieck, bis du einen einträgst.
- Die NASA-Übung nutzt den öffentlichen `DEMO_KEY` von [api.nasa.gov](https://api.nasa.gov) — für mehr als ein paar Testaufrufe dort einen eigenen (kostenlosen) Key holen.
- Die KI-Workflows (Tag 4–7) sind auf `gpt-5.4-mini` eingestellt; jedes andere Chat-Modell funktioniert genauso.
- **Platzhalter:** `deine@mail.de` (Mail-Empfänger), `DEIN_OPENWEATHER_KEY` (API-Keys-Demo Tag 3) und `DEINE_GOOGLE_SHEET_ID` (Sheets-Workflows Tag 2) ersetzt du nach dem Import durch eigene Werte. Einen kostenlosen OpenWeather-Key gibt es auf [openweathermap.org](https://openweathermap.org/api).
- Der Langzeit-Memory-Workflow (Tag 7) referenziert eine n8n **Data Table** namens `kundennotizen` — lege sie in deiner Instanz an (Spalten `kunde`, `notiz`) und wähle sie in den beiden Data-Table-Tools neu aus.
