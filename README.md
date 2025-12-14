# widget.selbsthilfe.io

Ein Widget, um die passende Selbsthilfe-Kontaktstelle in der eigenen Region zu finden.

## Übersicht

Dieses Projekt stellt ein durchsuchbares Widget für Selbsthilfe-Kontaktstellen in Deutschland bereit. Das Widget wird als statische GitHub Pages-Seite gehostet und folgt dem Muster des [map.selbsthilfe.io](https://github.com/Neue-Selbsthilfe/map.selbsthilfe.io) Projekts.

## Funktionen

- **Suchfunktion**: Suche nach Name, Stadt oder Postleitzahl
- **Filteroptionen**: Filterung nach Bundesland und Stadt
- **Responsive Design**: Optimiert für Desktop und mobile Geräte
- **Overlay-Ansicht**: Das Widget öffnet sich in einem Overlay mit iframe
- **Automatische Datenaktualisierung**: Täglicher Import von der Selbsthilfe-Labor API

## Dateien

- `index.html` - Hauptseite mit Button zum Öffnen des Widgets
- `search-widget-iframe.html` - Das eigentliche Widget mit Such- und Filterfunktionen
- `data/` - JSON-Datenquellen (Kontaktstellen, Bundesländer, Postleitzahlen)
- `.github/workflows/fetch-data.yml` - Täglicher Workflow zum Abrufen von API-Daten
- `.github/workflows/deploy-pages.yml` - Deployment-Workflow für GitHub Pages

## Datenquelle

Die Daten werden täglich um 2:00 Uhr UTC von der [selbsthilfe-labor.de](https://selbsthilfe-labor.de) API abgerufen und transformiert. Der Workflow benötigt ein `API_TOKEN` Secret für den Zugriff auf die API.

## Lokale Entwicklung

```bash
# Repository klonen
git clone https://github.com/Neue-Selbsthilfe/widget.selbsthilfe.io.git
cd widget.selbsthilfe.io

# Lokalen Server starten (Python 3)
python3 -m http.server 8000

# Im Browser öffnen: http://localhost:8000
```

## Deployment

Das Projekt wird automatisch auf GitHub Pages deployed, wenn Änderungen auf den `main` Branch gepusht werden.

## Sicherheit

- XSS-Schutz durch HTML-Escaping aller dynamischen Inhalte
- Validierung aller JSON-Daten vor dem Commit
- Sichere API-Token-Verwaltung über GitHub Secrets
