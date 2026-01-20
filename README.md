# n8n Course Level 2 – Notes & Workflows (Completed)

Dieses Repo dokumentiert meine Ergebnisse aus dem **n8n Course Level 2** (Text Course). Schwerpunkt des Kurses ist **Arbeiten mit Daten** in n8n: Datenstruktur verstehen, Daten transformieren, Daten aus unterschiedlichen Quellen zusammenführen und robuste Workflows mit Fehlerbehandlung bauen.

---

## Kurs-Überblick

**Level 2** richtet sich an alle, die nach Level 1 tiefer einsteigen und **komplexere Business-Prozesse** automatisieren wollen. Man arbeitet praxisnah mit kurzen Übungen und baut am Ende einen mehrteiligen Business-Workflow nach Anleitung.

**Voraussetzungen (aus dem Kurs):**

* n8n (Self-hosted)
* eine **Unique ID** + Kurs-Credentials (werden nach Registrierung per Mail bereitgestellt)
* Basic n8n-Kenntnisse + grundlegendes JavaScript-Verständnis



## Was ich im Kurs gelernt habe

### 1) n8n Datenmodell sauber verstehen

* n8n verarbeitet Daten als **Items** (im Kern: ein Array von Objekten).
* Relevante Praxis: Daten korrekt strukturieren, Daten referenzieren, Transformationen per **Code Node** (JavaScript). ([n8n Docs][2])

### 2) Daten-Typen verarbeiten (HTML/XML, Date/Time, Binary)

* Umgang mit **HTML**/**XML** (z.B. Extraktion via CSS-Selector mit HTML Node, XML Node).
* Umwandlungen rund um Zeit/Formate sowie **Binary Data** (z.B. Dateien/Attachments). ([n8n Docs][3])

### 3) Merging & Splitting von Daten

* Zusammenführen von Daten aus mehreren Systemen (z.B. CRM/DB/Spreadsheet) und Aufteilen/Aggregieren, damit jeder Node „itemweise“ arbeiten kann. ([n8n Docs][4])

### 4) Fehlerbehandlung mit Error Workflows

* Aufbau eines **Error Workflows** mit **Error Trigger** und Verknüpfung als Error Workflow in den Workflow-Settings. ([n8n Docs][5])



## Business Use Case (Kursprojekt)

Im Kursprojekt geht es darum, einen (realistischen) Prozess zu automatisieren:

1. Daten aus mehreren Quellen holen und kombinieren
2. Sortieren / Datumsformate anpassen
3. Dateien (binary) generieren
4. Benachrichtigungen per Email & Discord verschicken ([n8n Docs][6])

Dazu werden **3 Workflows** gebaut. ([n8n Docs][6])



## Workflows in diesem Repo

### Workflow 1: Merging data (Airtable + REST Countries)

**Ziel:** Airtable-`customers` um `region` und `subregion` anreichern.

* Airtable: `customers` listen
* REST Countries API: `https://restcountries.com/v3.1/all?fields=name,region,subregion`
* Merge by Key: `customerCountry` (Airtable) ↔ `name.common` (API)
* Airtable Update: Felder `region` und `subregion` schreiben ([n8n Docs][7])



### Workflow 2: Generating reports (mehrstufig)

**Ziel:** Daten aus einem Kurs-API-Endpunkt + Airtable mergen, Reports/Dateien erzeugen und Benachrichtigungen senden.

**Part 1 – Getting data from different sources**

* HTTP Request: Kurs-Dataset-URL, **Header Auth** via Credentials + zusätzlicher Header `unique_id`
* Airtable: `customers` listen
* Merge by Key: `customerID`
* Sort: nach `orderPrice` absteigend ([n8n Docs][8])

**Part 2 – Generating file for regional sales**

* IF: nur `Americas`
* Convert to File: JSON → Binary (pro Item eine Datei)
* Email Node: Datei(en) als Attachment senden
* Discord Node: Statusmeldung in `#course-level-two` ([n8n Docs][8])

**Part 3 – Generating files for total sales**

* Loop Over Items: Batch-Verarbeitung (z.B. 5er-Chunks)
* Set: Felder wie `customerEmail`, `customerRegion`, `customerSince`, `orderPrice`
* Date & Time: Datumsformat z.B. `MM/DD/YYYY`
* Convert to File: CSV-Export, Dateiname via Expression (runIndex)
* Discord Node: Statusmeldung ([n8n Docs][8])



### Workflow 3: Monitoring workflow errors (Error Workflow)

**Ziel:** Fehler im Hauptworkflow automatisch melden.

* Neuer Workflow mit **Error Trigger**
* Discord Node: Meldung mit Workflow-Name, Error Message, letzter Node, Execution URL
* Diesen Workflow als **Error Workflow** für den Hauptworkflow setzen ([n8n Docs][9])

---

## Quellen (Offizielle Kurs-Doku)

* n8n Course Level 2 (Übersicht): ([n8n Docs][1])
* Kapitel (Data Types, Merge/Split, Error Workflows): ([n8n Docs][2])
* Kursprojekt / Workflows 1–3: ([n8n Docs][6])

---

[1]: https://docs.n8n.io/courses/level-two/?utm_source=chatgpt.com "Level two: Introduction"
[2]: https://docs.n8n.io/courses/level-two/chapter-1/?utm_source=chatgpt.com "Understanding the data structure"
[3]: https://docs.n8n.io/courses/level-two/chapter-2/?utm_source=chatgpt.com "Processing different data types"
[4]: https://docs.n8n.io/courses/level-two/chapter-3/?utm_source=chatgpt.com "Merging and splitting data"
[5]: https://docs.n8n.io/courses/level-two/chapter-4/?utm_source=chatgpt.com "Dealing with errors in workflows"
[6]: https://docs.n8n.io/courses/level-two/chapter-5/chapter-5.0/ "Use case | n8n Docs  "
[7]: https://docs.n8n.io/courses/level-two/chapter-5/chapter-5.1/ "Workflow 1 | n8n Docs  "
[8]: https://docs.n8n.io/courses/level-two/chapter-5/chapter-5.2/ "Workflow 2 | n8n Docs  "
[9]: https://docs.n8n.io/courses/level-two/chapter-5/chapter-5.3/ "Workflow 3 | n8n Docs  "
