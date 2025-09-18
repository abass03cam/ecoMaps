# 🌍 ecoMaps – ECO-Routing für Bremerhaven

[![Live Demo](https://img.shields.io/badge/demo-online-brightgreen)](https://ecomaps.informatik.hs-bremerhaven.de/)

**ecoMaps** ist ein Masterprojekt der Hochschule Bremerhaven im Bereich *Informatik – Vertrauenswürdige Systeme*.  
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
- **Technologien:** HTML, CSS, JavaScript  
- **Frameworks:** Leaflet 1.9.4 (OpenStreetMap-Basiskarten, Overlays für Radwege und ÖPNV)  
- **Struktur:** MapManager, UIManager, FormManager, RouteController  
- **Besonderheiten:** Debounce-Suche, responsives UI, flexible Konfiguration über `config.js`  

### 🔹 Backend
- **Technologien:** Python, FastAPI + Uvicorn  
- **Routing-Bibliotheken:** SUMO, DUAROUTER, sumolib, TraCI  
- **Architektur:** containerbasiert mit Docker, abgesichert über Traefik (Reverse Proxy, CORS-Handling, TLS)  
- **API-Endpunkte:**
  - `/fastest` → schnellste Route (Zeit)  
  - `/shortest` → kürzeste Route (Distanz)  
  - `/eco` → CO₂-optimierte Route (Emissionen)  

**Response-Kontrakt (einheitlich für alle Endpunkte):**
```json
{
  "steps": [[lat, lon], [lat, lon], ...],
  "distance": 5800,
  "time": 720,
  "co2": 180
}
