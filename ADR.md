# Architecture Decision Records (ADR)

Kurze Notizen zu wichtigen Design-Entscheidungen im Projekt. Jede Entscheidung bekommt
einen eigenen Eintrag. Ziel: in 6 Monaten (oder für jemand anderen) nachvollziehbar
machen, *warum* etwas so gebaut wurde, nicht nur *was* gebaut wurde.

---

## Vorlage (kopieren für jede neue Entscheidung)

## ADR-XXX: [Kurzer Titel der Entscheidung]

**Datum:** [Datum]
**Status:** Vorgeschlagen | Entschieden | Später verworfen

**Kontext:**
Welches Problem oder welche Frage stand im Raum?

**Optionen, die ich abgewogen habe:**
1. Option A – Vor-/Nachteile
2. Option B – Vor-/Nachteile

**Entscheidung:**
Wofür ich mich entschieden habe.

**Begründung:**
Warum diese Option und nicht die andere(n).

**Konsequenzen:**
Was folgt daraus (auch Nachteile, die ich bewusst in Kauf nehme).

---

## Beispiel (kannst du löschen)

## ADR-001: Analytics-Service in Go statt Node.js

**Datum:** 2026-08-01
**Status:** Entschieden

**Kontext:**
Der Analytics-Service soll Daten von der Core-API abrufen und aggregieren. Ich kenne
JavaScript bereits gut, Go noch gar nicht.

**Optionen, die ich abgewogen habe:**
1. Node.js/TypeScript – schnell umsetzbar, aber keine neue Sprache gelernt
2. Go – steilere Lernkurve, aber statische Typisierung und Kompilierung sind neue
   Konzepte für mich, und Go wird oft für genau solche kleinen API-Services genutzt

**Entscheidung:**
Go.

**Begründung:**
Das Projekt soll bewusst zum Lernen unterschiedlicher Paradigmen dienen. Node.js kenne
ich schon aus dem Frontend, Go bringt mir etwas komplett Neues (Typsystem, Concurrency
über Goroutines, Kompilierung).

**Konsequenzen:**
- Ich brauche mehr Zeit für Phase 3 als geplant, weil ich Go-Syntax erst lernen muss
- Dafür verstehe ich am Ende den Unterschied zwischen interpretierten und kompilierten
  Sprachen praktisch, nicht nur theoretisch
