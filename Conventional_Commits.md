# Commit-Konvention für Zeitreihenanalyse-Projekt

Diese Datei definiert die Regeln für Commit-Nachrichten in unserem Projekt. Wir folgen dem Conventional Commits Standard, angepasst an die Bedürfnisse unseres Zeitreihenanalyse-Projekts.

## Struktur der Commit-Nachrichten

Jede Commit-Nachricht muss folgende Struktur haben:

```
<typ>[optionaler scope]: <beschreibung>

[optionaler body]

[optionale footer]
```

## Typen (Präfixe)

Die folgenden Typen müssen für die Commit-Nachrichten verwendet werden:

- **feat**: Fügt ein neues Feature hinzu (korreliert mit MINOR in Semantic Versioning)
- **fix**: Behebt einen Bug (korreliert mit PATCH in Semantic Versioning)
- **docs**: Änderungen an der Dokumentation
- **style**: Formatierungen, fehlende Semikolons, etc; keine Codeänderungen
- **refactor**: Code-Änderungen, die weder Bugs beheben noch Features hinzufügen
- **perf**: Verbesserungen der Performance
- **test**: Hinzufügen oder Korrigieren von Tests
- **build**: Änderungen am Build-System oder externen Abhängigkeiten
- **ci**: Änderungen an CI-Konfigurationsdateien und Skripten
- **chore**: Andere Änderungen, die keine Quellcodedateien ändern
- **data**: Änderungen an Datensätzen oder das Hinzufügen neuer Daten
- **analysis**: Updates an Analyseergebnissen oder Methoden

## Scope

Der Scope gibt den Kontext der Änderung an und wird in Klammern gesetzt. Für unser Projekt definieren wir folgende Scopes:

- **zeitreihe1**: Änderungen an der ersten Zeitreihe
- **zeitreihe2**: Änderungen an der zweiten Zeitreihe
- **zeitreihe3**: Änderungen an der dritten Zeitreihe
- **viz**: Änderungen an Visualisierungen
- **model**: Änderungen an statistischen Modellen
- **doc**: Änderungen an der Dokumentation
- **data**: Änderungen an Daten
- **env**: Änderungen an der Entwicklungsumgebung

## Breaking Changes

Breaking Changes müssen mit einem Ausrufezeichen nach dem Typ/Scope gekennzeichnet werden, z.B.:

```
feat(model)!: ändere Standardparameter für ARIMA-Modell
```

Alternativ kann ein BREAKING CHANGE Footer verwendet werden:

```
feat(model): ändere Standardparameter für ARIMA-Modell

BREAKING CHANGE: Die Standardparameter wurden geändert, was die bestehenden Modellvoraussagen beeinflusst.
```

## Beispiele

### Feature hinzufügen
```
feat(zeitreihe1): füge saisonale Dekomposition hinzu
```

### Bug fix
```
fix(viz): korrigiere Y-Achsenbeschriftung in Trendgrafik
```

### Dokumentation aktualisieren
```
docs: aktualisiere Projektbeschreibung in README
```

### Neue Daten hinzufügen
```
data(zeitreihe2): füge Daten für Q1 2024 hinzu
```

### Formatierungen
```
style(notebooks): vereinheitliche Code-Formatierung
```

### Breaking Change
```
feat(model)!: ändere Basismodell von ARIMA zu SARIMA
```

## Branching-Workflow

Gemäß unserem Code of Conduct befolgen wir folgende Branching-Strategie:
1. Jedes Teammitglied arbeitet in seinem eigenen Branch (`feature/andrew`, `feature/kai`, `feature/fabian`)
2. Änderungen werden erst in den `develop`-Branch und dann in den `main`-Branch gemergt
3. Commits sollten regelmäßig und mit aussagekräftigen Nachrichten nach dieser Konvention erfolgen

## Weitere Regeln

1. Die Beschreibung sollte kurz und prägnant sein (nicht mehr als 72 Zeichen)
2. Die Beschreibung sollte im Imperativ formuliert sein ("ändere", nicht "ändert" oder "geändert")
3. Der Body sollte Details zur Änderung und Begründungen erklären
4. Bei komplexen Änderungen sollte ein ausführlicher Body verwendet werden
5. Footer können genutzt werden, um auf Issues zu verweisen (Refs: #123)
6. Commit früh und oft mit klaren, spezifischen Änderungen