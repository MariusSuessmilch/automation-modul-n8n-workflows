# Praxistag 3 · Musterlösung

[Workflow herunterladen](praxistag-3-musterloesung.json)

Passend zu „Kundenantworten mit einem KI-Prüfer freigeben“ auf dem Miro-Kursboard. Ein AI Agent schreibt die Antwort auf eine Kundenanfrage, ein zweiter AI Agent prüft sie gegen ein Faktenblatt. Eine IF-Node schickt geprüfte Antworten nach „Versandbereit“, alles andere nach „Mensch prüft“.

## Starten

1. Die JSON-Datei herunterladen und in einen neuen n8n-Workflow über **Import from File…** importieren. Alternativ den vollständigen JSON-Inhalt auf den leeren Canvas kopieren.
2. Bei beiden **OpenAI Chat Model**-Nodes das Kurs-Credential wählen, falls n8n es nicht selbst zuordnet. **Use Responses API** bleibt eingeschaltet.
3. In **Anfrage und Faktenblatt** das Feld `anfrage` füllen.
4. **Execute workflow** klicken. Auf dem Canvas trägt nur die Verbindung zum Ausgang mit Daten die Angabe „1 item“.

Workshop, Faktenblatt und Anfragen sind fiktiv. Der Workflow sendet nichts und ist inaktiv gespeichert.

## Ablauf

**Manual Trigger → Anfrage und Faktenblatt → Schreiber → Prüfer → Bestanden? → Versandbereit / Mensch prüft**

- **Anfrage und Faktenblatt** (Edit Fields): die Felder `faktenblatt` und `anfrage`.
- **Schreiber** (AI Agent, Prompt-Quelle „Define below“): Prompt mit Faktenblatt und Anfrage, System Message mit den Schreibregeln. Was nicht im Faktenblatt steht, kündigt er als Klärung an.
- **Prüfer** (AI Agent, Prompt-Quelle „Define below“, „Require Specific Output Format“ an): bekommt den Entwurf als Input und holt Faktenblatt und Anfrage mit `$('Anfrage und Faktenblatt')` aus der ersten Node. Die System Message enthält drei Prüfregeln. Der **Structured Output Parser** („Generate From JSON Example“) legt die Antwort auf `bestanden` (true/false) und `grund` fest.
- **Bestanden?** (IF): `{{ $json.output.bestanden }}`, Operator Boolean › is true.
- **Versandbereit** und **Mensch prüft** (Edit Fields): Antwort des Schreibers, bei „Mensch prüft“ zusätzlich der Grund des Prüfers.
- Beide Agents haben ein eigenes **OpenAI Chat Model** (Version 1.3, `gpt-6-luna`, Use Responses API an). Mit Version 1.2 bricht `gpt-6-luna` ab, sobald ein Agent Tools nutzt (siehe Praxistag 2).

## Die fünf Tests der Folie

| Test | Erwartet |
| --- | --- |
| Was kostet der Workshop und wann sind die Termine? | Versandbereit, 390 Euro und beide Termine |
| Ich bin für den 12. November angemeldet. Kann ich am 5. November noch kostenlos stornieren? | Versandbereit, nicht kostenlos, 50 %. Ein Entwurf, der sich widerspricht, gehört zu „Mensch prüft“ |
| Gibt es einen Rabatt für Studierende? | Versandbereit, kein Rabatt versprochen, die Frage wird geklärt |
| Output des Schreibers bearbeiten: 390 durch 290 ersetzen | Mensch prüft, der Grund nennt den Preis |
| Output des Schreibers bearbeiten: „Als Dankeschön erhalten Sie 10 % Rabatt.“ ergänzen | Mensch prüft, der Grund nennt die Zusage |

Für Test 4 und 5: Den Workflow einmal ausführen, im Output des Schreibers auf den Stift **Edit Output** klicken, den Text ändern und **Save** klicken. Damit ist der Output gepinnt. Nach dem Test im Banner auf **Unpin** klicken.

## Auf dem Kursserver geprüft

Am 15.10.2026 im Kurs-n8n mit `gpt-6-luna` ausgeführt, mit den Prompts dieser Datei:

| Test | Läufe | Ergebnis |
| --- | --- | --- |
| Preis und Termine | 1 | Versandbereit, 390 Euro zzgl. MwSt., 12. November und 3. Dezember |
| Storno am 5. November | 3 | 2 × Versandbereit (nicht kostenlos, 50 %), 1 × Mensch prüft |
| Rabatt für Studierende | 2 | 2 × Versandbereit, die Frage wird geklärt |
| Preis 290 statt 390 (gepinnt) | 1 | Mensch prüft, Grund: Regel 1, Preis |
| 10 % Rabatt ergänzt (gepinnt) | 1 | Mensch prüft, Grund: Regel 2, Rabatt-Zusage |

Beim Storno-Test hält der Prüfer gelegentlich eine richtige, aber unscharf formulierte Antwort auf. Im Lauf oben beanstandete er „Der 5. November liegt innerhalb dieser 14 Tage“ als missverständlich, obwohl er im Grund selbst schreibt, dass das Ergebnis stimmt. Das ist der sichere Fehler: Die Antwort landet bei einem Menschen. Im Kurs lässt sich daran zeigen, dass ein KI-Prüfer selbst irrt und wie stark seine Regeln das Ergebnis steuern.

Zwei Prompt-Stellen kamen erst durch den Test hinzu. Ohne den Satz „Erlaubt ist der Hinweis, dass wir eine offene Frage klären und uns melden“ lehnte der Prüfer beim Rabatt-Test die Antwort ab, die der Schreiber geben soll (2 von 2 Läufen). Ohne „Rechne kein eigenes Datum aus“ nannte der Schreiber selbst einen Stichtag, den der Prüfer beanstandete.

## Quellen

- [n8n: AI Agent](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/)
- [n8n: Structured Output Parser](https://docs.n8n.io/integrations/builtin/cluster-nodes/sub-nodes/n8n-nodes-langchain.outputparserstructured/)
- [n8n: IF](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.if/)
- [n8n: Daten pinnen und bearbeiten](https://docs.n8n.io/data/data-pinning/)
