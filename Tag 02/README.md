# Tag 2 · Erste Schritte mit n8n

Abgleich mit dem [Miro-Kursboard](https://miro.com/app/board/uXjVH5lK5XY=/) vom 07.09.2026. Die Theke verlinkt dieses Repository. Die folgenden Dateien decken die aktuellen Demos und Übungen ab.

## Aktuelle Board-Demos und Übungen

Die kurzen Dateinamen entsprechen den Importverweisen auf dem Board. Datei öffnen, **Download raw file** wählen und in n8n über **Import from File…** importieren.

| Board-Inhalt | Importdatei | Ablauf |
| --- | --- | --- |
| IF: Wenn-Dann-Logik | [tag2-logic-if.json](tag2-logic-if.json) | Manual Trigger → The Decoder RSS → Titel enthält KI? → zwei Edit Fields mit `kategorie` |
| Switch: Mehr als zwei Wege | [tag2-logic-switch.json](tag2-logic-switch.json) | RSS → Kategorien-Array prüfen → Forschung / Short News / Praxis / Fallback; setzt `ordner` |
| Merge: Zwei Quellen, ein Strom | [tag2-logic-merge.json](tag2-logic-merge.json) | Schedule 08:00 → The Decoder + t3n → Merge (Append) |
| Aggregate: Viele Items bündeln | [tag2-logic-aggregate.json](tag2-logic-aggregate.json) | Manual Trigger → The Decoder RSS → Aggregate → ein gemeinsamer Mailtext |
| Live-Demo: Webhook in Aktion | [tag2-webhook-demo.json](tag2-webhook-demo.json) | GET `/gruss?name=Anna` → Edit Fields setzt `antwort` |
| Live-Demo: Send Email mit SMTP einrichten | [tag2-smtp-demo.json](tag2-smtp-demo.json) | Manual Trigger → Send Email an dein Testpostfach |
| Übung: E-Mail Workflow | [Tagesgruß-Mail](Tag%202%20%C2%B7%20%C3%9Cbung%20E-Mail-Workflow%20%E2%80%93%20Tagesgru%C3%9F-Mail%20%288%3A00%29.json) | Schedule 08:00 → Send Email mit festem Text |
| Live-Demo: Die erste API abrufen | [tag2-http-request-demo.json](tag2-http-request-demo.json) | Manual Trigger → HTTP GET `https://randomuser.me/api/` |
| Übung: Den JSON-Body lesen | [API-Response lesen](Tag%202%20%C2%B7%20%C3%9Cbung%20%E2%80%93%20API-Response%20lesen.json) | JSON mit `results[0]` und `info` untersuchen; zusätzliche Response-Optionen bleiben aus |
| Nachmittagsübung Tag 2 | [tag2-nachmittagsuebung.json](tag2-nachmittagsuebung.json) | Schedule 08:00 → RSS → IF (Titel enthält OpenAI) → zwei Edit Fields mit `meldung` |

Die HTTP-Demo war als „Nodes in Aktion 1“ funktional bereits vorhanden. Die zusätzliche Importdatei trägt den auf dem Board genannten Namen. Die sieben anderen kurzen Dateien ergänzen bislang fehlende aktuelle Workflows. Die bisherigen IF- und Split-Out-Extras verwenden andere Beispieldaten und ersetzen die Board-Demos nicht.

## Testen und Aufgaben

- **Logik und RSS:** Keine Credentials nötig. RSS und HTTP brauchen Internetzugang; Feed-Inhalte und Item-Zahlen ändern sich. Ein leerer IF- oder Switch-Ausgang kann korrekt sein. Prüfe die eingehenden Titel bzw. Kategorien. IF durchsucht den Titel ohne Beachtung der Groß-/Kleinschreibung; Switch prüft die Kategorien mit ihrer genauen Schreibweise.
- **IF:** Suchwort von `KI` auf `Agent` ändern und die beiden Ausgänge vergleichen. Edit Fields setzt nur `kategorie`; die ursprünglichen Titel stehen im Input.
- **Switch:** Nur die erste passende Regel erhält das Item. Ergänze eine Kategorie mit **Array → contains**, links `{{ $json.categories }}`. Nach dem Hinzufügen einer Regel den Fallback wieder mit „Ins Sammelfach“ verbinden. Edit Fields setzt nur `ordner` und legt keine echten Ordner an.
- **Merge:** Im t3n-Node die URL durch `https://the-decoder.com/feed/` ersetzen. Output-Anzahl = Input 1 + Input 2. Append entfernt keine Duplikate.
- **Aggregate:** `All Item Data (Into a Single List)`, Ausgabefeld `artikel`, `Include → Specified Fields`, `Fields To Include → title,link`. RSS liefert viele Items; Aggregate liefert ein Item mit einer Liste. Die letzte Edit-Fields-Node erzeugt `anzahl`, `betreff` und `mailtext`. Prüfe: RSS-Item-Anzahl = Länge von `artikel` = `anzahl`; am Ende genau ein Item. Keine Credentials nötig, kein Mailversand und kein LLM-Aufruf. Für einen späteren Mail- oder LLM-Schritt muss der gesamte Text bzw. die Liste als Input verwendet werden. Bei LLMs das Kontextlimit beachten.
- **Split Out (Zusatzmaterial):** Das bisherige Beispiel bleibt als `tag2-logic-split-out.json` verfügbar; die Board-Folie behandelt seit 20.09.2026 Aggregate.
- **Webhook:** Einen eigenen Path pro Gruppe setzen (z. B. `gruss-gruppe-1`). Zuerst **Listen for test event**, dann die angezeigte Test-URL mit `?name=Anna` aufrufen. `antwort` im Node-Output prüfen; der Browser zeigt die Empfangsbestätigung. Vor einem weiteren Test erneut lauschen.
- **SMTP:** Eigenes SMTP-Credential auswählen; Absender und Empfänger ersetzen. Erst nach Kontrolle des Empfängers ausführen. Eingang und Spam prüfen. Ohne bestätigten Empfang ist die Zustellung nicht verifiziert.
- **Schedule:** Zeitzone ist `Europe/Berlin`. **Execute workflow** startet sofort, auch außerhalb von 08:00. Alle Dateien sind inaktiv gespeichert. Für die Übungen ist kein Publish nötig; regelmäßiger Betrieb erfordert später die Veröffentlichung/Aktivierung.

### Nachmittagsübung: Ausbaustufen

1. **Zwei Feeds:** Einen zweiten RSS Read direkt mit dem Schedule Trigger verbinden (`https://the-decoder.com/feed/`). Beide RSS-Ausgänge an getrennte Merge-Eingänge anschließen, **Append** wählen und Merge vor IF setzen. Item-Zahlen beider Feeds, des Merge und beider IF-Ausgänge vergleichen.
2. **Kategorien aufteilen:** Zwischen RSS Read und IF einen Split Out auf `categories` setzen. **Include → All Other Fields** wählen, damit `title` für IF und `meldung` erhalten bleibt. Ein Artikel mit mehreren Kategorien erzeugt mehrere Items; ein leeres Array liefert keine Kategorie-Items. Diese Item-Zahl ist deshalb keine Artikelanzahl.

Zum gezielten Testen der beiden IF-Zweige vorübergehend einen Manual Trigger und Edit Fields vor IF verwenden: erst `title = OpenAI-Test`, dann `title = Anderer Artikel`. Beide müssen jeweils im passenden Ausgang eine gefüllte `meldung` erzeugen.

## Bereits vorhandene Beispiele

Die fünf „Nodes in Aktion“-Dateien bleiben als Ablaufbeispiele erhalten:

1. Auf Knopfdruck Daten holen
2. Die tägliche Info-Mail
3. Vom Formular in die Tabelle
4. Wichtige Mails ans Team melden
5. Anfragen ans richtige Team

Slack und Google Sheets sind laut Board hier Beispiele. Die betreffenden Dateien benötigen eigene Credentials, Zielkanäle bzw. Tabellen und Feldzuordnungen; ihre Existenz bedeutet nicht, dass diese Integrationen fertig eingerichtet oder live geprüft sind.

Die älteren Extras „If Node Demo“, „Split Out Beispiel“ und „Ausblick – Liste abarbeiten und zusammenführen“ bleiben verfügbar. Im verlinkten Zahlen-IF-Demo wurden beide fehlenden Ausgangsverbindungen ergänzt und der Vergleichswert als Zahl gesetzt. Für die aktuelle Merge-Übung bitte `tag2-logic-merge.json` verwenden.

Die Tagesgruß-Mail entspricht jetzt dem festen Text auf dem Board und verwendet `Europe/Berlin`. „API-Response lesen“ zeigt jetzt direkt den JSON-Body; das frühere Verhalten mit `body`, `headers` und `statusCode` ist als optionale Erweiterung in der Workflow-Notiz erklärt.

## Aggregate: geprüfter Beispielablauf (20.09.2026)

[n8n-Beispiel im Kursordner Tag 02](https://n8n.srv1077415.hstgr.cloud/workflow/1gK27tlAQ4dFNJxC). Manueller Test erfolgreich: 10 RSS-Items → 1 Aggregate-Item → 1 Mailtext-Item mit `anzahl = 10`. Die Zahl der Artikel hängt vom Feed ab. Aggregate bündelt Daten; es fasst Texte nicht inhaltlich zusammen. Viele nachfolgende Nodes verarbeiten jedes eingehende Item separat. Das Bündeln ermöglicht eine gemeinsame Verarbeitung, sofern die nächste Node die Liste bzw. den gemeinsamen Text nutzt.

Quelle: [n8n Aggregate](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.aggregate/).
