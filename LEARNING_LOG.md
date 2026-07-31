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

### Beispiel-Eintrag (kannst du löschen)

### 2026-07-31 – Phase 1: Core-API Grundgerüst

**Was ich gemacht habe:**
- FastAPI-Projekt aufgesetzt, Account-Model mit SQLModel definiert
- POST- und GET-Endpoint für Accounts gebaut und mit curl getestet

**Was ich neu gelernt habe:**
- `Depends()` in FastAPI ist Dependency Injection – die Funktion wird automatisch
  aufgerufen und das Ergebnis in den Endpoint eingespeist
- SQLModel braucht `table=True`, um wirklich eine DB-Tabelle zu erzeugen, sonst ist
  es nur ein Pydantic-Validierungsmodell

**Wo ich gestolpert bin & wie ich es gelöst habe:**
- Problem: Server startete nicht, Fehler "Table already defined"
- Ursache: Ich hatte das Model versehentlich zweimal importiert
- Lösung: Import-Statements bereinigt, `__init__.py` korrekt genutzt

**Offene Fragen / was ich noch nicht verstehe:**
- Wie genau unterscheidet sich `session.commit()` von `session.flush()`?

**Nächster Schritt:**
- Category- und Transaction-Endpoints nach demselben Muster bauen
