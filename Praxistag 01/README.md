# Praxistag 1 · Musterlösung

[Workflow herunterladen](praxistag-1-musterloesung.json)

Passend zu „Anfragen mit n8n sortieren“ auf dem Miro-Kursboard. Die Lösung enthält den vollständigen Ablauf mit sechs Übungsanfragen und den Regeln aus den Arbeitsfolien.

## Starten

1. Die JSON-Datei herunterladen und in einen neuen n8n-Workflow über **Import from File…** importieren. Alternativ den vollständigen JSON-Inhalt auf den leeren Canvas kopieren.
2. **Execute workflow** klicken.
3. Die vier letzten Edit-Fields-Nodes öffnen und deren Output vergleichen.

Keine Credentials oder zusätzlichen Dienste nötig. Der Workflow enthält ausschließlich Übungsdaten, sendet keine Nachrichten und ruft keine externen APIs auf. Er startet manuell und ist inaktiv gespeichert; eine Veröffentlichung ist für die Übung nicht nötig.

## Ablauf

**Manual Trigger → Code → Remove Duplicates → Switch → vier Edit Fields**

- **Code:** Run Once for All Items. Erzeugt dieselben sechs Items wie der Startcode im Board.
- **Remove Duplicates:** Remove Items Repeated Within Current Input; Compare = Selected Fields; Fields To Compare = `id`; Remove Other Fields = aus. Die erste A01 bleibt erhalten. Deduplizierung gilt pro Ausführung, sodass erneutes Testen wieder alle fünf eindeutigen Anfragen liefert.
- **Switch:** Drei String-Regeln auf `thema`: `Technik`, `Rechnung`, `Sonstiges`. Fallback Output = Extra Output. Auch ein unbekanntes Thema landet zur Prüfung. Die Schreibweise wird exakt verglichen.
- **Edit Fields:** Include Other Input Fields = an. Ergänzt `team` und `status`; `id`, `thema` und `text` bleiben erhalten.

| Ausgang | IDs im Ausgangsbeispiel | team | status |
| --- | --- | --- | --- |
| Support | A01, A03 | Support | zugeordnet |
| Buchhaltung | A02 | Buchhaltung | zugeordnet |
| Service | A05 | Service | zugeordnet |
| Zur Prüfung | A04 | offen | prüfen |

Sechs eingehende Items ergeben fünf eindeutige Anfragen: vier zugeordnet, eine zur Prüfung.

## Die vier Tests der Folie

Vor jedem Test die ursprünglichen sechs Anfragen im Code wiederherstellen und dann nur die angegebene Änderung vornehmen. Den **gesamten Workflow** erneut ausführen. Ein leerer Switch-Ausgang kann korrekt sein.

| Test | Änderung im Code | Erwartung |
| --- | --- | --- |
| 1 | Keine | Support A01/A03; Buchhaltung A02; Service A05; Prüfung A04; insgesamt fünf Items |
| 2 | A04: `thema: 'Technik'` | A04 geht zusätzlich zum Support; Prüfung bleibt leer |
| 3 | A04: `thema: 'Vertrieb'` | A04 geht zur Prüfung; das unbekannte Thema bleibt im Output sichtbar |
| 4 | A03: `id: 'A06'` | Support enthält A01 und A06; die doppelte A01 wird weiterhin entfernt |

Die Erweiterungen mit zweiter Datenquelle oder KI bleiben als eigenständige Wahlaufgaben offen.

## Auf dem Kursserver geprüft

Am 08.10.2026 wurden alle vier Tests auf dem Kursserver erfolgreich ausgeführt und die Node-Ausgaben im n8n-Editor geprüft:

| Test | Geprüftes Ergebnis |
| --- | --- |
| Ausgangsdaten | 6 → 5 Items; alle vier Ausgänge und alle fünf Ergebnisfelder stimmen |
| A04 = Technik | Support enthält A01, A03 und A04; Prüfung leer |
| A04 = Vertrieb | A04 mit Thema Vertrieb, team offen, status prüfen |
| A03 → A06 | Support enthält A01 und A06 |
| Ausgangsdaten wiederhergestellt | Abschließender Lauf erfolgreich |

Die Importdatei wurde mit dem anschließend heruntergeladenen Server-Export abgeglichen: Node-Typen, Versionen, wirksame Parameter und Verbindungen stimmen überein. Node-Versionen: Manual Trigger 1, Code 2, Remove Duplicates 2, Switch 3.4, Edit Fields 3.4. Die expliziten Standardwerte in der Importdatei werden beim Server-Export teilweise weggelassen. Die Datei enthält keine Server-ID und keine angehefteten Testdaten.

## Quellen

- [n8n: Remove Duplicates](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.removeduplicates/)
- [n8n: Switch](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.switch/)
