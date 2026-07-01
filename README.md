# Gebaeudebrueter in Berlin - Staging

Dieses Repository ist die Preview-Umgebung fuer GitHub Pages.
Hier landet jede neu erzeugte Kartenversion zuerst zur manuellen Qualitaetspruefung.
Erst nach Freigabe wird dieselbe Version ins Live-Repository uebernommen.

## Rolle im Deployment-Prozess

- Stage-Gate zwischen Generator und Live
- Versionierte Auslieferung unter docs/generated/<sha>/
- Manuelle Sichtpruefung vor Live-Freigabe

GitHub Pages liest aus:

- Branch: main
- Ordner: docs

## Struktur in diesem Repository

- docs/GebaeudebrueterMultiMarkers.html
	- aktive Staging-Karte
- docs/generated/<sha>/assets/
	- versionsierte CSS/JS-Assets pro Build

## End-to-End Ablauf

### 1) Stage im Generator-Repository ausfuehren

Empfohlen (Wrapper):

```powershell
./run_pipeline.ps1 -Mode stage -Verbose
```

Alternativ direkt:

```powershell
python scripts/run_full_pipeline.py --verbose stage
```

Ergebnis in diesem Staging-Repository:

- docs/GebaeudebrueterMultiMarkers.html wird aktualisiert
- docs/generated/<sha>/assets wird erzeugt/aktualisiert
- Commit Update map <sha> wird nach origin/main gepusht

### 2) Manuelle Staging-Pruefung

Pruefen auf der Staging-Pages-URL:

- Marker/Anzahl plausibel
- Filter und Legende funktionieren
- Popups (Inhalt/Links) korrekt
- Layout auf Desktop und Mobile ok

Wenn etwas nicht passt:

- Kein Live-Publishing starten
- Korrektur im Generator-Repository vornehmen
- Stage erneut ausfuehren

### 3) Live-Freigabe aus dem Generator-Repository

Die zu veroeffentlichende SHA ist der Ordnername unter:

- docs/generated/<sha>

Danach im Generator-Repository:

```powershell
./run_pipeline.ps1 -Mode publish-live -Sha aabf0eb -Verbose
```

Wichtig:

- Keine Platzhalter mit spitzen Klammern eingeben.
- Richtig: -Sha aabf0eb
- Falsch: -Sha <sha>

## Quick Commands

SHA-Ordner auflisten (im Generator-Repository):

```powershell
Get-ChildItem ../gebaeudebrueter-in-berlin-staging/docs/generated -Directory | Select-Object -ExpandProperty Name
```

Neueste SHA ermitteln und direkt publish-live starten:

```powershell
$sha = (Get-ChildItem ../gebaeudebrueter-in-berlin-staging/docs/generated -Directory | Sort-Object LastWriteTime -Descending | Select-Object -First 1 -ExpandProperty Name)
./run_pipeline.ps1 -Mode publish-live -Sha $sha -Verbose
```

## Betriebsregeln

- Stage und Live sind strikt getrennt
- Live wird nie automatisch durch stage ausgeloest
- Jede Live-Version muss vorher in Staging geprueft sein
