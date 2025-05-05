# Verhaltens- und Arbeitsregeln (Code of Conduct)

## Git-Workflow

### Branching-Strategie
Wir arbeiten mit folgender Branch-Struktur:
- **main**: Produktionsbranch, enthält nur stabile, getestete Versionen
- **develop**: Integrationsbranch für Feature-Entwicklung
- **feature/andrew**: Persönlicher Entwicklungsbranch für Andrew
- **feature/kai**: Persönlicher Entwicklungsbranch für Kai
- **feature/fabian**: Persönlicher Entwicklungsbranch für Fabian

### Workflow-Regeln
1. Jedes Teammitglied arbeitet ausschließlich in seinem eigenen Feature-Branch.
2. Code darf nur über Pull Requests in den develop-Branch integriert werden.
3. Der develop-Branch wird nur in den main-Branch gemergt, wenn alle Features vollständig getestet sind.
4. Vor jedem Merge in develop muss der eigene Branch aktualisiert werden (`git pull origin develop`).
5. Commit-Nachrichten müssen aussagekräftig sein und den Inhalt der Änderung kurz beschreiben.