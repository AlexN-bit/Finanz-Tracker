# Lerntagebuch – Finanz-Tracker-Projekt

Kurzer Eintrag nach jeder Arbeitssitzung. Ehrlich schreiben – auch Fehler und Umwege
gehören rein, das ist der eigentliche Lernwert.

---

## Vorlage für einen Eintrag (kopieren & ausfüllen)

### [Datum] – [Phase X: Kurzer Titel]

**Was ich gemacht habe:**
-

**Was ich neu gelernt habe:**
-

**Wo ich gestolpert bin & wie ich es gelöst habe:**
- Problem:
- Ursache:
- Lösung:

**Offene Fragen / was ich noch nicht verstehe:**
-

**Nächster Schritt:**
-

---

## Einträge

### 31.07.2026 – Phase 0: Vorbereitung

**Was ich gemacht habe:**
- Recherche HTTP Grundlagen


**Was ich neu gelernt habe:**
- Was ist HTTP? (Hyper Text Transfer Protocol)
  - Regelt wie Client und Server miteinander kommunizieren

- Anfragen-Inhalt:
  - Inhalt im Nachrichtentext übermittelten Informationen

- Anfrage Methoden:
  - GET: Daten anfordern, soll nur Daten abrufen und keine Anfrage-Inhalt enthalten
  - HEAD: verlangt eine Antwort wie eine GET-Anfrage, jedoch ohne Anwortkörper
  - POST: Formulardaten senden (Große sensible Daten werden gesendet)
  - PUT: Ersetzt ale aktuellen Darstellungen de Zielressource durch den Anfrage-Inhalt
  - DELETE: löscht angegebene Ressource
  - CONNECT: etabliert einen Tunnel zum vom Zielressource identifzierten Server
  - OPTIONS: Kommunikationsoptionsen für die Zielressource
  - TRACE: Nachrichtenschleifen-Test des Pfads zur Zielressource
  - PATCH: wendet partielle Modifikationen auf eine Ressource an

- Statuscodes:
  - 200 OK: Anfrage erfolgreich
  - 2xx: Erfolg
  - 3xx: Umleitung
  - 401: Unauthorized
  - 404 Not found: Angefragte Daten kann nciht gefunden werden
  - 4xx: Client Fehler
  - 5xx: Server Fehler

**Wo ich gestolpert bin & wie ich es gelöst habe:**
- Problem:
- Ursache:
- Lösung:

**Offene Fragen / was ich noch nicht verstehe:**
-

**Nächster Schritt:**
- benötigte Packages Installieren

### 04.08.2026 – Phase 0: Vorbereitung

**Was ich gemacht habe:**
- Python 3.14.6 installiert

**Was ich neu gelernt habe:**
- Python hat einen eigenen Versinsmanager veröffentlicht. Dieser musste installiert werden und nun kann man per py, python bestimmte Versionen installieren

**Wo ich gestolpert bin & wie ich es gelöst habe:**
- Problem: Keine Installationsdatei für Python gefunden.
- Ursache: Läuft nun über den Manager 
- Lösung: Verwende jetzt den Manager

**Offene Fragen / was ich noch nicht verstehe:**
-

**Nächster Schritt:**
- Node.js installieren

### 04.08.2026 – Phase 0: Vorbereitung

**Was ich gemacht habe:**
- Node.js Installieren

**Was ich neu gelernt habe:**
- Node.js zu installieren
- Meinen Arbeitsschrit ins git gepusht 

**Wo ich gestolpert bin & wie ich es gelöst habe:**
- Problem: Choclaty scheint nichtfunktioniert zu haben: Node.js ist trozdem richtig installiert
- Ursache: -
- Lösung: -

**Offene Fragen / was ich noch nicht verstehe:**
- Macht es mir auf lange Sicht Probleme?

**Nächster Schritt:**
- Phase 1

### 05.08.2026 – Phase 1: Core-API in Python - Datenmodell & CRUD

**Was ich gemacht habe:**
- Neues Projekt finance-core-api anlegen (NeuenOrdner erstellen)
- Ordner mit virtueller Umgebung und requirments.txt füllen
- requirments installtieren
- Entwurf der Datenmodelle

**Was ich neu gelernt habe:**
-

**Wo ich gestolpert bin & wie ich es gelöst habe:**
- Problem: Wie setze ich die virtuelle Umgebun auf?
- Ursache:
- Lösung: python -m venv venv (Wobei das zweite venv für den namen der Umgebung steht)

**Offene Fragen / was ich noch nicht verstehe:**
-

**Nächster Schritt:**
-