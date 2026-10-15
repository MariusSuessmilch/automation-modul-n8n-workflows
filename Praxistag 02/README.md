# Praxistag 2 · Musterlösung

[Workflow herunterladen](praxistag-2-musterloesung.json)

Passend zu „Reisekosten mit einem KI-Agenten umrechnen“ auf dem Miro-Kursboard. Ein AI Agent beantwortet im Chat Fragen zu Reisekosten: Er holt aktuelle Wechselkurse per HTTP Request und rechnet mit dem Calculator.

## Starten

1. Die JSON-Datei herunterladen und in einen neuen n8n-Workflow über **Import from File…** importieren. Alternativ den vollständigen JSON-Inhalt auf den leeren Canvas kopieren.
2. Im **OpenAI Chat Model** das Kurs-Credential wählen, falls n8n es nicht selbst zuordnet. **Use Responses API** bleibt eingeschaltet.
3. Unten **Open chat** öffnen und die Testfragen stellen.
4. In den **Logs** neben dem Chat nachsehen, welche Tools der Agent aufgerufen hat.

Die Wechselkurse kommen von [Frankfurter](https://frankfurter.dev/): kostenlos, ohne API-Schlüssel, täglich aktualisiert. Der Workflow sendet keine Nachrichten und ist inaktiv gespeichert.

## Ablauf

**When chat message received → AI Agent**, unten am Agenten:

- **OpenAI Chat Model** (Version 1.3): Modell `gpt-6-luna`, Kurs-Credential, Use Responses API an. Ohne Responses API lehnt OpenAI Tools mit `gpt-6-luna` ab („Function tools with reasoning_effort are not supported for gpt-6-luna in /v1/chat/completions“). Eine neu eingefügte Node hat die Option schon eingeschaltet.
- **HTTP Request** (als Tool): Method GET, Authentication None. Die URL setzt das Modell selbst (Sternsymbol am Feld, „Defined automatically by the model“).
- **Calculator:** ohne Einstellungen.

Die System Message des Agenten sagt, wofür jedes Tool da ist, nennt die Kursadresse `https://api.frankfurter.dev/v2/rate/GBP/EUR` und legt fest, was der Agent nicht tut (buchen, bestellen).

## Die fünf Testfragen der Folie

| Frage | Erwartete Tools |
| --- | --- |
| Wie viel Euro ist ein Pfund heute wert? | HTTP Request, Antwort mit Kurs und Datum; oft rundet der Agent danach mit dem Calculator |
| Was sind 19 % von 240 Euro? | nur Calculator, 45,60 Euro |
| Wie viel Euro sind 186,40 GBP? | HTTP Request, dann Calculator |
| Wie viel Euro sind 42,50 USD plus 38,90 CHF? | zwei Kurse, dann Calculator |
| Buche mir ein Hotel in London. | kein Tool, der Agent lehnt ab |

## Auf dem Kursserver geprüft

Am 15.10.2026 im Kurs-n8n mit `gpt-6-luna` ausgeführt, je eine Frage im Chat, Tools in den Logs abgelesen:

| Frage | Antwort des Agenten | Tools laut Logs |
| --- | --- | --- |
| Pfund heute | 1 GBP = 1,1835 EUR, Stand 15.10.2026 | HTTP Request, Calculator |
| 19 % von 240 Euro | 45,60 € | Calculator |
| 186,40 GBP | 220,60 EUR | HTTP Request, Calculator |
| 42,50 USD plus 38,90 CHF | 37,95 € + 41,74 €, zusammen 79,68 € | 2 × HTTP Request, 3 × Calculator |
| Hotel buchen | lehnt ab | keins |

Die Kurse ändern sich täglich, die Beträge also auch. Mit der OpenAI-Chat-Model-Node 1.2 (Chat Completions) brach schon die erste Frage mit dem Fehler oben ab.

## Quellen

- [n8n: AI Agent](https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/)
- [n8n: Calculator](https://docs.n8n.io/integrations/builtin/cluster-nodes/sub-nodes/n8n-nodes-langchain.toolcalculator/)
- [n8n: Werte vom Modell füllen lassen](https://docs.n8n.io/advanced-ai/examples/using-the-fromai-function/)
- [Frankfurter: Wechselkurs-API](https://frankfurter.dev/)
