# Finanz-Tracker: Lernprojekt-Anleitung

**Ziel:** Du baust einen vollständigen Finanz-Tracker selbst – Schritt für Schritt, mit
mehreren Programmiersprachen, die über APIs miteinander kommunizieren. Diese Anleitung
gibt dir Aufgaben, Konzepte und Ressourcen, aber **keinen fertigen Code**. Das Schreiben
und Debuggen ist der Teil, bei dem du am meisten lernst.

---

## Warum diese Architektur?

Statt alles in Python zu bauen, besteht das Projekt aus **drei Services in drei Sprachen**,
die ausschließlich über REST-APIs miteinander reden – genau wie in echten Firmen üblich:

```
┌─────────────────────┐      HTTP/JSON      ┌──────────────────────┐
│  Frontend (React/TS) │ ───────────────────▶ │  Core-API (Python)   │
│  Browser-UI           │ ◀─────────────────── │  FastAPI + SQLite    │
└─────────────────────┘                       └──────────┬───────────┘
                                                           │ HTTP/JSON
                                                           ▼
                                               ┌──────────────────────┐
                                               │  Analytics-Service    │
                                               │  (Go oder Node.js)    │
                                               │  Berechnet Reports    │
                                               └──────────────────────┘
```

- **Core-API (Python/FastAPI):** verwaltet Konten, Kategorien, Transaktionen, CSV-Import
- **Analytics-Service (Go oder Node.js – deine Wahl):** holt sich Daten von der Core-API
  per HTTP, berechnet Auswertungen (z. B. Ausgabentrends) und liefert sie als eigene API
- **Frontend (TypeScript/React):** ruft beide APIs auf und zeigt sie im Browser an

**Das lernst du dabei konkret über APIs:**
- Wie man eine REST-API *anbietet* (Python)
- Wie man eine REST-API *konsumiert* – aus einem Backend heraus (Go/Node → Python) UND aus einem Browser heraus (React → beide APIs)
- Warum Sprache X für Service Y egal ist, solange sich alle an den API-Vertrag (JSON-Schema) halten
- Authentifizierung zwischen Services (API-Keys, später JWT)
- Fehlerbehandlung, wenn ein Service down ist oder falsche Daten liefert

---

## Phase 0: Vorbereitung (bevor du eine Zeile Code schreibst)

**Lernziele:** HTTP-Grundlagen, REST-Konventionen, JSON

**Aufgaben:**
1. Recherchiere die Bedeutung der HTTP-Methoden `GET`, `POST`, `PUT`, `DELETE` und wofür man sie jeweils nutzt
2. Recherchiere HTTP-Statuscodes: Was bedeuten `200`, `201`, `400`, `401`, `404`, `500`?
3. Installiere ein Tool zum manuellen Testen von APIs: [Postman](https://www.postman.com/) oder `curl` (Kommandozeile) oder die [VS Code REST Client Extension](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)
4. Installiere: Python 3.11+, Node.js 20+, Go 1.22+ (nur falls du dich für Go entscheidest)

**Selbsttest-Fragen:**
- Was ist der Unterschied zwischen `PUT` und `PATCH`?
- Warum nutzt man `POST` statt `GET`, um Daten zu erstellen?
- Was bedeutet "REST" eigentlich (die 6 Constraints, grob)?

**Ressourcen:**
- [MDN: HTTP-Methoden](https://developer.mozilla.org/de/docs/Web/HTTP/Methods)
- [FastAPI-Dokumentation](https://fastapi.tiangolo.com/) (Tutorial-Sektion durchlesen, noch nicht coden)

---

## Phase 1: Core-API in Python – Datenmodell & CRUD

**Lernziele:** FastAPI-Grundlagen, ORMs, Datenbank-Design, CRUD-Pattern

**Aufgaben:**
1. Lege ein neues Projekt an: `finance-core-api/`, virtuelle Umgebung, `requirements.txt` mit `fastapi`, `uvicorn`, `sqlmodel`
2. Entwirf dein Datenmodell selbst (bevor du Code schreibst!) für:
   - `Account` (Konto): Name, Typ, Startsaldo
   - `Category` (Kategorie): Name, Typ (Einnahme/Ausgabe)
   - `Transaction` (Buchung): Betrag, Datum, Beschreibung, Verknüpfung zu Konto und Kategorie
3. Implementiere die Models mit SQLModel
4. Baue CRUD-Endpoints (Create/Read/Update/Delete) für alle drei Ressourcen
5. Teste jeden Endpoint mit `curl` oder Postman, bevor du zum nächsten übergehst

**API-Vertrag, den du umsetzen sollst** (das ist dein "Ziel-Interface"):

| Methode | Pfad                  | Zweck                          |
|---------|------------------------|--------------------------------|
| POST    | `/accounts/`           | Konto anlegen                  |
| GET     | `/accounts/`           | Alle Konten auflisten          |
| GET     | `/accounts/{id}`       | Einzelnes Konto abrufen        |
| DELETE  | `/accounts/{id}`       | Konto löschen                  |
| POST    | `/categories/`         | Kategorie anlegen               |
| GET     | `/categories/`         | Alle Kategorien auflisten       |
| POST    | `/transactions/`       | Transaktion anlegen             |
| GET     | `/transactions/`       | Transaktionen (mit Filtern) auflisten |

**Selbsttest-Fragen:**
- Warum solltest du für "Daten erstellen" (Request) und "Daten lesen" (Response) unterschiedliche Pydantic-Modelle nutzen, statt eines für alles?
- Was passiert, wenn du eine Transaktion für ein nicht-existierendes Konto anlegen willst? Wie fängst du das ab (welcher HTTP-Statuscode)?
- Wie stellst du sicher, dass niemand eine `id` beim Erstellen selbst vorgeben kann?

**Definition of Done:**
- Alle Endpoints aus der Tabelle funktionieren
- `/docs` (Swagger UI) zeigt eine vollständige, korrekte API-Dokumentation
- Falsche Anfragen (z. B. fehlendes Feld) geben sinnvolle Fehlermeldungen mit Statuscode 400/422 zurück

**Ressourcen:**
- [FastAPI Tutorial: SQL Databases](https://fastapi.tiangolo.com/tutorial/sql-databases/)
- [SQLModel-Dokumentation](https://sqlmodel.tiangolo.com/)

---

## Phase 2: CSV-Import (Python)

**Lernziele:** Datei-Uploads über APIs, Datenverarbeitung mit pandas, Fehlerbehandlung bei Massendaten

**Aufgaben:**
1. Recherchiere, wie FastAPI Datei-Uploads (`UploadFile`) entgegennimmt
2. Baue einen Endpoint `POST /import/csv`, der eine CSV-Datei + eine `account_id` entgegennimmt
3. Nutze `pandas`, um die CSV zu parsen
4. Baue ein Mapping-System, damit unterschiedliche Bank-Formate (unterschiedliche Spaltennamen) unterstützt werden können
5. Baue eine Duplikat-Erkennung: Importierst du dieselbe Datei zweimal, sollen keine doppelten Buchungen entstehen
6. Gib am Ende eine Zusammenfassung zurück: wie viele Zeilen importiert, wie viele übersprungen, welche Fehler

**Knifflige Punkte, über die du selbst stolpern solltest (zum Lernen!):**
- Deutsche Banken schreiben Beträge oft als `1.234,56` – wie parst du das zu einer Zahl?
- CSV-Dateien haben unterschiedliche Trennzeichen (`,` vs `;`) und Kodierungen (`utf-8` vs `latin1`) – wie machst du das konfigurierbar?
- Was ist ein sinnvoller "Schlüssel", um zwei Buchungen als Duplikate zu erkennen?

**Selbsttest-Fragen:**
- Warum braucht FastAPI für Datei-Uploads ein zusätzliches Paket (`python-multipart`)?
- Wie unterscheidet sich eine `multipart/form-data`-Anfrage von einer JSON-Anfrage?

**Ressourcen:**
- [FastAPI: Request Files](https://fastapi.tiangolo.com/tutorial/request-files/)
- [pandas: read_csv-Dokumentation](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html)

---

## Phase 3: Analytics-Service in Go ODER Node.js (neue Sprache!)

Das ist der Kern der Lernaufgabe: ein **zweiter Service in einer anderen Sprache**, der
sich Daten von deiner Python-API über HTTP holt und eigene Auswertungen liefert.

**Lernziele:** HTTP-Client in einer neuen Sprache, API-Konsumierung, Cross-Language-Kommunikation, Fehlerbehandlung bei Netzwerk-Calls

**Aufgaben:**
1. Wähle Go oder Node.js/TypeScript (Go, wenn du auch mal eine statisch typisierte, kompilierte Sprache lernen willst)
2. Baue einen eigenen kleinen Webserver (Go: `net/http` oder `gin`; Node: `express`)
3. Baue einen Endpoint `GET /reports/monthly?account_id=1&month=2026-07`, der:
   - Per HTTP-Request die Transaktionen von deiner Python-API abruft (`GET http://localhost:8000/transactions/?account_id=1&date_from=...`)
   - Die Daten selbst aggregiert (z. B. Summe pro Kategorie, größte Ausgabe, Durchschnitt pro Tag)
   - Das Ergebnis als eigenes JSON zurückgibt
4. Implementiere Fehlerbehandlung: Was passiert, wenn die Python-API nicht erreichbar ist oder einen Fehler zurückgibt?
5. Miss die Zeit: Wie lange dauert ein Analytics-Request, wenn er erst die Python-API aufrufen muss?

**Selbsttest-Fragen:**
- Wie unterscheidet sich das Aufrufen einer API in Go/Node von FastAPI (das die API *bereitstellt*)?
- Was passiert bei einem Timeout – wie fängst du das ab, damit dein Service nicht einfach abstürzt?
- Warum ist es sinnvoll, dass der Analytics-Service *nicht* direkt auf die Datenbank der Core-API zugreift, sondern nur über die API geht?

**Ressourcen (Go):**
- [Go: net/http Client](https://pkg.go.dev/net/http#Client)
- [Go by Example: HTTP Clients](https://gobyexample.com/http-clients)

**Ressourcen (Node.js/TypeScript):**
- [Node.js: fetch() (nativ seit Node 18)](https://nodejs.org/en/learn/getting-started/fetch)
- [Express-Dokumentation](https://expressjs.com/de/)

---

## Phase 4: Frontend in TypeScript/React (dritte Sprache/Umgebung)

**Lernziele:** API-Calls aus dem Browser, State-Management, async/await, TypeScript-Typen für API-Antworten

**Aufgaben:**
1. Setze ein React-Projekt mit TypeScript auf (z. B. mit Vite)
2. Definiere TypeScript-Interfaces, die genau zu den JSON-Antworten deiner APIs passen (z. B. `interface Transaction { id: number; amount: number; ... }`)
3. Baue eine Kontoübersicht-Seite: ruft `GET /accounts/` von der Core-API auf und zeigt die Liste an
4. Baue ein Formular zum Anlegen einer neuen Transaktion (`POST /transactions/`)
5. Baue eine Dashboard-Seite, die den Analytics-Service aufruft und die Auswertung als Diagramm anzeigt (z. B. mit `recharts` oder `chart.js`)
6. Behandle Ladezustände und Fehler (was zeigst du an, während die Anfrage läuft? Was, wenn sie fehlschlägt?)

**Selbsttest-Fragen:**
- Warum ist `fetch()` im Browser asynchron – was würde passieren, wenn es synchron wäre?
- Was ist CORS, und warum wirst du wahrscheinlich einen CORS-Fehler bekommen, sobald dein Frontend (Port 5173) die Python-API (Port 8000) aufruft? Wie löst man das in FastAPI (`CORSMiddleware`)?
- Warum lohnt sich TypeScript hier – was passiert, wenn sich das API-Format ändert und dein Frontend-Code nicht angepasst wird?

**Ressourcen:**
- [React-Dokumentation: Fetching Data](https://react.dev/learn/synchronizing-with-effects)
- [FastAPI: CORS](https://fastapi.tiangolo.com/tutorial/cors/)
- [TypeScript-Handbuch](https://www.typescriptlang.org/docs/handbook/intro.html)

---

## Phase 5: Absicherung zwischen den Services

**Lernziele:** API-Authentifizierung, Umgebungsvariablen, Secrets-Handling

**Aufgaben:**
1. Baue einen einfachen API-Key-Mechanismus: Die Core-API akzeptiert Requests nur mit einem Header `X-API-Key: <geheimer-wert>`
2. Der Analytics-Service muss diesen Key bei jedem Request an die Core-API mitschicken
3. Später (optional, fortgeschritten): Baue echtes User-Login mit JWT-Tokens für das Frontend
4. Speichere alle Secrets in `.env`-Dateien, niemals im Code

**Selbsttest-Fragen:**
- Was ist der Unterschied zwischen einem API-Key (Service-zu-Service) und einem JWT-Token (User-zu-Service)?
- Warum sollte ein API-Key niemals im Frontend-Code (Browser!) sichtbar sein?

**Ressourcen:**
- [FastAPI: Security](https://fastapi.tiangolo.com/tutorial/security/)
- [JWT.io – Einführung](https://jwt.io/introduction)

---

## Phase 6: Deployment mit Docker (alles zusammenbringen)

**Lernziele:** Containerisierung, Multi-Service-Orchestrierung, Netzwerk zwischen Containern

**Aufgaben:**
1. Schreibe für jeden Service ein eigenes `Dockerfile`
2. Schreibe eine `docker-compose.yml`, die alle drei Services startet und miteinander vernetzt
3. Wichtig zu verstehen: Innerhalb von Docker sprechen sich Services über ihre **Service-Namen** an (nicht `localhost`) – z. B. ruft der Analytics-Service `http://core-api:8000` auf, nicht `http://localhost:8000`
4. Teste: Funktioniert der komplette Datenfluss (Frontend → Analytics → Core-API) innerhalb von Docker?

**Selbsttest-Fragen:**
- Warum funktioniert `localhost` zwischen zwei Containern nicht so, wie du es erwartest?
- Was macht `docker-compose up` eigentlich im Hintergrund?

**Ressourcen:**
- [Docker-Dokumentation: Compose](https://docs.docker.com/compose/)

---

## Empfohlene Reihenfolge & Zeitrahmen (Richtwert)

| Phase | Inhalt                          | Neue Sprache/Technologie   | Ca. Zeitaufwand |
|-------|----------------------------------|----------------------------|------------------|
| 0     | Grundlagen                      | –                           | 1-2 Std          |
| 1     | Core-API CRUD                    | Python/FastAPI              | 1-2 Tage         |
| 2     | CSV-Import                       | Python/pandas                | 0.5-1 Tag        |
| 3     | Analytics-Service                | Go oder Node.js/TypeScript   | 1-2 Tage         |
| 4     | Frontend                         | TypeScript/React             | 2-3 Tage         |
| 5     | API-Absicherung                  | Security-Konzepte             | 0.5-1 Tag        |
| 6     | Docker-Deployment                | Docker/Compose                | 1 Tag            |

## Wie du diese Anleitung nutzen solltest

1. Arbeite Phase für Phase, überspringe nichts – jede Phase baut auf der API der vorherigen auf
2. Schreibe **zuerst** den API-Vertrag (welche Endpoints, welche Felder) auf Papier/in Markdown, bevor du Code schreibst
3. Wenn du feststeckst: Frag mich gezielt zu einem Konzept oder Fehler (z. B. "Wie parse ich deutsche Zahlenformate in Go?") – ich gebe dir dann Erklärungen und Beispiele, aber du schreibst den Code für dein Projekt selbst
4. Committe deinen Code nach jeder abgeschlossenen Aufgabe in Git – das gibt dir Kontrollpunkte, zu denen du zurückkehren kannst
