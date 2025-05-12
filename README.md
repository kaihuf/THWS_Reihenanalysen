# Zeitreihenanalyse-Projekt

## Überblick
Dieses Repository enthält die Ergebnisse und den Code unserer Zeitreihenanalyse im Rahmen des Schwerpunkts Business Analytics (BBA) bei Herrn Menden.

## Team
- Fabian Niebelschütz
- Andrew Barnhoorn
- Kai Hufnagel

## Projektbeschreibung
In diesem Projekt führen wir eine umfassende Zeitreihenanalyse durch, um [kurze Beschreibung des Ziels, z.B. "Muster und Trends in wirtschaftlichen Daten zu identifizieren und Prognosemodelle zu entwickeln"]. Die Analyse umfasst verschiedene statistische Methoden und Visualisierungstechniken, angewendet auf drei unterschiedliche Zeitreihen:

1. **Zeitreihe 1**:
2. **Zeitreihe 2**:
3. **Zeitreihe 3**:

## Inhalte
- `data/`: Enthält die für die Analyse verwendeten Datensätze
  - `zeitreihe1/`: Daten für die erste Zeitreihe
  - `zeitreihe2/`: Daten für die zweite Zeitreihe
  - `zeitreihe3/`: Daten für die dritte Zeitreihe
- `notebooks/`: Jupyter Notebooks mit der durchgeführten Analyse
  - `02_zeitreihe1_analysis.ipynb`: Detailanalyse der ersten Zeitreihe
  - `03_zeitreihe2_analysis.ipynb`: Detailanalyse der zweiten Zeitreihe
  - `04_zeitreihe3_analysis.ipynb`: Detailanalyse der dritten Zeitreihe
- `results/`: Visualisierungen und Ergebnisse der Analyse
- `docs/`: Weiterführende Dokumentation

## Zeitplan
- **05.05.2025**: Responsitory erstellen & Präsentieren
- **12.05.2025**: Data Preperation & Analyse der Zeitreihen
- **19.05.2025**: Analysen Visualisieren & Ergebnisse Präsentieren
- **Abgabe**: [19.05.2025]

## Prozess darstellung unserer Modell erstellung

## Zeitreihenanalyse-Prozess

```mermaid
flowchart TD
    classDef phase fill:#f9f9f9,stroke:#333,stroke-width:2px
    classDef step fill:white,stroke:#666,stroke-width:1px
    
    P1["1. Data Preparation"] --> P1S1["Datensatz laden (Closing)"]
    P1S1 --> P1S2["Maximale Jahreszahl ermitteln"]
    P1S2 --> P1S3["Auf leere Stellen prüfen"]
    P1S3 --> P1S4["Dividendenzeilen entfernen"]
    
    P1S4 --> P2["2. Stationaritätstests"]
    P2 --> P2S1["ADF-Test"]
    P2 --> P2S2["PP-Test"]
    P2 --> P2S3["KPSS-Test"]
    P2S1 & P2S2 & P2S3 --> P2S4["Ergebnis: Meist nicht stationär"]
    
    P2S4 --> P3["3. Transformation zur Stationarität"]
    
    P3 --> P3S1["Differenzierung 1. und 2. Ordnung"]
    P3 --> P3S2["Log. Transformation"]
    P3 --> P3S3["Log. differenzierte Transformation"]
    P3 --> P3S4["Moving average"]
    P3 --> P3S5["Simple exponential smoothing"]
    P3 --> P3S6["HP-Filter"]
    
    P3S1 & P3S2 & P3S3 & P3S4 & P3S5 & P3S6 --> P3S7["Erneute Stationaritätstests"]
    P3S7 --> P3S8["Beste Transformation auswählen"]
    
    P3S8 --> P4["4. Korrelationsanalyse"]
    P4 --> P4S1["ACF berechnen"]
    P4 --> P4S2["PACF berechnen"]
    P4S1 & P4S2 --> P4S3["Interpretation der Ergebnisse"]
    
    P4S3 --> P5["5. Modellspezifikationen"]
    P5 --> P5S1["Auto-ARIMA Funktion anwenden"]
    P5S1 --> P5S2["Ergebnisse interpretieren"]
    P5S2 --> P5S3["Moving Average aufbauen"]
    P5S3 --> P5S4["K-Fold für verschiedene Modelle"]
    
    class P1,P2,P3,P4,P5 phase
    class P1S1,P1S2,P1S3,P1S4,P2S1,P2S2,P2S3,P2S4,P3S1,P3S2,P3S3,P3S4,P3S5,P3S6,P3S7,P3S8,P4S1,P4S2,P4S3,P5S1,P5S2,P5S3,P5S4 step
```

