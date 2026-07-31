# Grundlegende Idee

In diesem Projekt soll eine App enstehen, welche Bankdaten aus CSV Dateien ausliest Kategorisiert und anschließend darstellt. Dabei möchte ich unterschiedliche Programmiersprachen verwende, welche per API miteinander kommunizieren.

---

## Finanz-Tracker-Lernanleitung

In dieser Datei habe ih per Claud AI ein Step by Step Guide erstellt, welcher die zu programmierbaren Schritte erklärt und Lernziele definiert. Er gibt mir jedoch nicht den Code vor.

---

## LEARNING_LOG (DevLog)

Hier werden nach jeder Session eingetragen was gemacht wurde, was ich gelernt habe und auf welche Probleme ich gestoßen bin und wie ich diese gelöst habe. 

---

## ADR (Archtecture Decision Records)

Kurze struckturierte Notizen zu Design-Entscheidungen

---

## Architektur

Projekt soll aus drei Servicen unterschiedlicher Sprachen bestehen, welche per Rest API miteinander kommunizieren. 
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


