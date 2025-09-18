# 🌍 ecoMaps – ECO-Routing für Bremerhaven

[![Live Demo](https://img.shields.io/badge/demo-online-brightgreen)](https://ecomaps.informatik.hs-bremerhaven.de/)

**ecoMaps** ist ein Masterprojekt der Hochschule Bremerhaven (2025) im Bereich *Informatik – Vertrauenswürdige Systeme*.  
Das Ziel: **nachhaltigere Mobilität** durch CO₂-optimierte Routenplanung.  
Während viele Routenplaner nur nach Distanz oder Zeit optimieren, ermöglicht ecoMaps eine dritte Dimension: die **ökologische Effizienz**.  

👉 **Live ausprobieren:** [ecoMaps Demo](https://ecomaps.informatik.hs-bremerhaven.de/)

---

## 🌱 Motivation

Unsere Motivation war es, ein Bewusstsein für nachhaltige Mobilität zu schaffen.  
Mit ecoMaps können Nutzer:innen zwischen drei Routenarten wählen:

- 🏎 **Schnell** → kürzeste Reisezeit  
- 📏 **Kurz** → geringste Distanz  
- 🌿 **Ökologisch** → geringste Emissionen (CO₂)  

Dazu wird die Open-Source-Verkehrssimulation **[SUMO](https://www.eclipse.org/sumo/)** (Simulation of Urban Mobility) eingesetzt, die reale Verkehrsnetze simulieren und mit verschiedenen Gewichtungen (Distanz, Zeit, Emissionen) optimieren kann.

---

## 🏗️ Architektur – Überblick

ecoMaps ist modular aufgebaut und trennt **Frontend** und **Backend** klar voneinander.  
Die Kommunikation erfolgt über eine REST-API mit einheitlichem **Response-Kontrakt** (distance, time, co₂, steps).

### 🔹 Frontend
- **Technologien:** HTML, CSS, JavaScript (statisch ausgeliefert via Apache oder einfachem Webserver)  
- **Karten-Framework:** Leaflet 1.9.4 mit OSM-Standardtiles und optionalen Overlays (Fahrradwege, ÖPNV)  
- **Funktionalitäten:**
  - Eingabe von Start- und Zielpunkten inkl. Auto-Vervollständigung (Debounce)  
  - Routing-Optionen (schnell, kurz, ökologisch – auch kombinierbar)  
  - Anzeige von Distanz, Reisezeit und CO₂-Werten  
  - Responsives UI (Mobile & Desktop)  

Um Wartbarkeit und Erweiterbarkeit zu sichern, wurde das Frontend in Manager-Klassen strukturiert:  
- **MapManager** → verwaltet Leaflet (Basemaps, Overlays, Marker, Polylines, fitBounds)  
- **UIManager** → steuert das Panel, Routen-Cards, CO₂-Box und Systemmeldungen  
- **FormManager** → übernimmt Eingaben, Validierung, Debounce und Button-Handling  
- **RouteController** → orchestriert alle Komponenten, ruft Services auf und rendert die Ergebnisse  

Services für die API-Anbindung:  
- **GeocodingService** → `/geocode/search`, `/geocode/reverse`  
- **RoutingService** → `/fastest/tci`, `/shortest/tci`, `/eco/tci`  

---

### 🔹 Backend
- **Technologien:** Python, FastAPI + Uvicorn  
- **Routing-Bibliotheken:** SUMO, DUAROUTER, sumolib, TraCI  
- **Architektur:** containerbasiert mit Docker, Reverse-Proxy via Traefik  

Die API stellt drei Hauptendpunkte bereit:  
- `/fastest` → berechnet die schnellste Route nach Reisezeit  
- `/shortest` → berechnet die kürzeste Route nach Distanz  
- `/eco` → berechnet die Route mit den geringsten CO₂-Emissionen  

**Response-Kontrakt (einheitlich für alle Endpunkte):**
```json
{
  "steps": [[lat, lon], [lat, lon], ...],
  "distance": 5800,
  "time": 720,
  "co2": 180
}
