#  Zeitreihenanalyse Arbeitsschritte

## Einleitung

Ziel dieser Analyse ist es, ein geeignetes ARIMA-Modell zu finden, das den zugrunde liegenden datengenerierenden Prozess einer Finanzzeitreihe (z. B. Aktienkurs) möglichst realitätsnah abbildet. 

### Was ist eine Zeitreihe?

Eine **Zeitreihe** ist eine Folge von Beobachtungen, die in regelmäßigen Abständen über die Zeit hinweg aufgezeichnet wird – beispielsweise tägliche Aktienkurse oder monatliche Arbeitslosenzahlen. Der Zweck der Zeitreihenanalyse ist es, **Strukturen wie Trends, Saisonalität und Autokorrelationen zu identifizieren**, um präzise Prognosen für zukünftige Werte zu erstellen.


### Ziel dieser Arbeit

Wir möchten ein automatisiertes Analyseverfahren entwickeln, das:

- eine Zeitreihe auf Stationarität prüft und ggf. transformiert,
- verschiedene ARIMA-Modelle testet und bewertet,
- auf Basis statistischer Kriterien das geeignetste Modell auswählt,
- eine Rolling Forecast durchführt und   
- die Ergebnisse visuell und statistisch bewertet.

Zusätzlich berücksichtigen wir **Modellvalidierung** (z. B. Residuenanalyse), **t-Tests der Parameter**, sowie die **Generierung von Prognosen**.

---

##  Zeitreihenanalyse – Schritt 1: Datenvorbereitung & Stationaritätsprüfung

### Überblick: Warum ist Stationarität wichtig?

Ein zentrales Ziel bei der Zeitreihenanalyse mit ARIMA-Modellen ist die Identifikation und Modellierung des zugrunde liegenden stochastischen Prozesses. ARIMA-Modelle (AutoRegressive Integrated Moving Average) setzen voraus, dass die analysierte Zeitreihe **stationär** ist.

**Stationarität** bedeutet, dass die statistischen Eigenschaften der Zeitreihe – insbesondere der Erwartungswert, die Varianz und die Autokorrelation – über die Zeit hinweg konstant bleiben. Wenn dies nicht gegeben ist (z. B. bei Trends, saisonalen Effekten oder Heteroskedastizität), kann das Modell fehlspezifiziert werden.

---

###  Datenimport & Visualisierung der Originalreihe

Wir importieren zunächst die Aktienkurs-Zeitreihe von Samsung, Daimler und Microsoft („Adjusted Close“-Preise), konvertieren das Datum in ein geeignetes Format und erstellen eine erste Visualisierung der ursprünglichen, **nicht transformierten** Zeitreihe.


**Beobachtung:**  
Die ursprüngliche Zeitreihe zeigt visuell einen starken Trend – sowohl auf- als auch abwärtsgerichtet in unterschiedlichen Phasen. Solche Trends deuten auf **Nichtstationarität** hin.

---

###  Stationaritätstests: ADF, PP und KPSS

Um Stationarität statistisch zu prüfen, wenden wir zwei komplementäre Tests an:

#### Augmented Dickey-Fuller-Test (ADF)
- **Nullhypothese (H₀):** Die Zeitreihe hat eine Einheitwurzel → *nicht stationär*
- **Alternativhypothese (H₁):** Stationarität liegt vor
- Wir lehnen H₀ ab, wenn der **p-Wert < 0.05**

**Testformel:**

$$
\Delta y_t = \alpha + \beta t + \gamma y_{t-1} + \sum_{i=1}^{p} \delta_i \Delta y_{t-i} + \varepsilon_t
$$


#### KPSS-Test (Kwiatkowski-Phillips-Schmidt-Shin)
- **Nullhypothese (H₀):** Die Zeitreihe ist stationär (gegen Trendstationarität)
- **Alternativhypothese (H₁):** Die Zeitreihe ist nicht stationär
- Wir behalten H₀ bei, wenn **p-Wert > 0.05**

#### Phillips-Perron-Test (PP)

Der Phillips-Perron-Test ist eine weitere Methode zur Prüfung auf Einheitwurzeln und ähnelt dem ADF, berücksichtigt aber heteroskedastische und serielle Korrelation in den Residuen auf flexible Weise.

- **Nullhypothese (H₀):** Die Zeitreihe besitzt eine Einheitwurzel → *nicht stationär*  
- **Alternativhypothese (H₁):** Stationarität liegt vor  
- Wir lehnen H₀ ab, wenn der **p-Wert < 0.05**

**Testidee:**  
PP erweitert das klassische Dickey–Fuller-Modell  
$$
\Delta y_t = \alpha + \beta t + \gamma\,y_{t-1} + \varepsilon_t
$$
um eine semi-nonparametrische Korrektur der Teststatistik, um mögliche Autokorrelation und Heteroskedastizität in den Fehlern$\varepsilon_t$zu entfernen, ohne explizit verzögerte Differenzen einzufügen.

**Teststatistik:**  
$$
Z_{\rho} = T\big(\hat{\rho}-1\big) - \tfrac{1}{2} \;\frac{\hat{\sigma}^2_{\Delta\varepsilon}}{\hat{\sigma}^2_{\varepsilon}}
$$  
wobei  

$\hat{\rho}$ der geschätzte AR-Parameter ist,  
- $\hat{\sigma}^2_{\varepsilon}$ die Varianz der Roh-Residuen und  
- $\hat{\sigma}^2_{\Delta\varepsilon}$ die Varianz der Residuen-Differenzen (korrigiert um serielle Korrelation)

**Vorteil gegenüber ADF:**  
Keine manuelle Wahl der Verzögerungen \(p\) nötig – die Korrektur erfolgt implizit über eine Newey-West-artige Schätzung.

**Praxis-Tipp:**  
Vergleiche PP- und ADF-Ergebnisse: Wenn beide zu ähnlichen Entscheidungen kommen, erhöhst Du das Vertrauen in das Testergebnis.

**Kombination beider Tests:**  
Sie ermöglicht eine robustere Beurteilung, da sie aus zwei Perspektiven prüfen.



---

###  Transformationen zur Erreichung von Stationarität

Um die ursprüngliche Reihe in eine stationäre zu überführen, wenden wir mehrere Transformationen an – jede mit einem spezifischen Ziel:

| Transformation                        | Zweck |
|--------------------------------------|-------|
| **1. Differenzierung**:  $y_t - y_{t-1}$  | Entfernt lineare Trends |
| **2. Differenzierung**: $(y_t - y_{t-1}) - (y_{t-1} - y_{t-2})$ | Entfernt quadratische/komplexere Trends |
| **Logarithmierung**: $\log(y_t)$ | Stabilisiert Varianz (z. B. bei exponentiellem Wachstum) |
| **Log-Differenz**: $\log(y_t) - \log(y_{t-1})$ | Kombiniert Trendentfernung und Varianzstabilisierung |
| **Moving Average Residuum**: $y_t - \overline{y}_{t,window}$ | Entfernt gleitenden Mittelwert (Trend) |
| **Exponentielle Glättung** | Entfernt Trend mit höherem Gewicht auf jüngere Werte |
| **HP-Filter (Hodrick-Prescott)** | Trennt Trend- und Zykluskomponente der Reihe |

Nach jeder Transformation führen wir erneut ADF- und KPSS-Tests durch, um den Erfolg zu bewerten.

---

###  Visualisierung der Transformationen

Die transformierten Zeitreihen werden grafisch dargestellt – inklusive der jeweiligen Testergebnisse (ADF & KPSS) in Textboxen.

**Beispiel:**

```text
ADF p = 0.021 → Stationär  
KPSS p = 0.08 → Stationär
```

Dies erlaubt eine schnelle visuelle und numerische Bewertung jeder Transformation.

---

###  Auswahl der besten Transformation

Zur systematischen Auswahl verwenden wir eine **Scoring-Funktion**, die beide Tests kombiniert:

$$
\text{Stationaritätsscore} = p_{\text{ADF}} + (1 - p_{\text{KPSS}})
$$

- Ziel: **Minimaler Score**
- Begründung: Kleine ADF-p-Werte + große KPSS-p-Werte → stationär

Der Name und die Serie der „besten Transformation“ werden gespeichert.

---

###  ACF- und PACF-Plots

Für zwei zentrale Reihen (beste Transformation + Log-Differenz) erstellen wir **ACF- und PACF-Plots mit Konfidenzintervallen**.

#### ACF (Autokorrelationsfunktion):
Zeigt Korrelation von $y_t$ mit $y_{t-k}$. Wichtig für MA-Komponente im ARIMA(p, d, q).

#### PACF (Partielle Autokorrelationsfunktion):
Zeigt "direkten" Effekt des Lags $k$ auf $y_t$, ohne Zwischenschritte. Wichtig für AR-Komponente.

Signifikante Lags außerhalb der Konfidenzgrenzen $±1.96/√n$ deuten auf relevante Modellbestandteile hin.

---

##  Fazit Schritt 1

- Die Zeitreihe wurde erfolgreich transformiert, um Stationarität zu erreichen.
- Durch Kombination von ADF- und KPSS-Tests konnte eine robuste Bewertung vorgenommen werden.
- Die **log-differenzierte Zeitreihe** erwies sich als beste Transformation.
- ACF- und PACF-Plots legen die Grundlage für die spätere Modellauswahl (ARIMA-Identifikation).

Wir sind nun bereit für **Modellidentifikation und Parameterschätzung**.

##  Schritt 2: ARIMA-Modellidentifikation via AIC/BIC

Nachdem wir im ersten Schritt die Zeitreihe erfolgreich in eine **stationäre Form** gebracht haben (z. B. durch Log-Differenzierung), können wir nun ein geeignetes ARIMA-Modell identifizieren.

---

###  Hintergrund: ARIMA(p, d, q)

Ein ARIMA-Modell kombiniert drei Komponenten:

- **AR (p)**: AutoRegressive-Teil → beschreibt, wie der aktuelle Wert von den vorherigen Werten abhängt  
- **I (d)**: Integrated-Teil → beschreibt, wie viele Differenzierungen notwendig sind, um Stationarität zu erreichen  
- **MA (q)**: Moving Average-Teil → beschreibt den Einfluss vergangener Schätzfehler (Residuen)

Die allgemeine Form eines ARIMA(p,d,q)-Modells ist:

$$
\Delta y_t = \alpha + \beta t + \gamma y_{t-1} + \sum_{i=1}^{p} \delta_i \Delta y_{t-i} + \varepsilon_t
$$


Dabei ist:
-$y_t$: aktueller Wert der Zeitreihe  
-$\phi_i$: AR-Koeffizienten  
-$\theta_i$: MA-Koeffizienten  
-$\varepsilon_t$: weiße Rauschkomponente (Zufallsfehler)

---

###  Ziel: Auswahl des besten (p,d,q)-Modells

Um das geeignetste Modell zu finden, wurden alle sinnvollen Kombinationen von p, d und q getestet. Die Auswahl basiert auf:

#### Bewertungskriterien:

| Kriterium | Ziel      | Formel (vereinfacht)                      | Bestrafung für Komplexität? |
|-----------|-----------|-------------------------------------------|------------------------------|
| **AIC**   | Modellgüte | $\text{AIC} = -2 \log(L) + 2k \$         | Ja (milder)                  |
| **BIC**   | Modellgüte | $\text{BIC} = -2 \log(L) + k \log(n) \$ | Ja (stärker)                 |

- $\log(L)$: Log-Likelihood des Modells  
- $k$: Anzahl der geschätzten Parameter  
- $n$: Anzahl der Beobachtungen

Ziel ist es, ein Modell mit möglichst **niedrigem AIC/BIC** zu finden. Dabei gilt:

- **AIC** bevorzugt Modelle mit besserer Vorhersagekraft (weniger Bestrafung für Komplexität)
- **BIC** bevorzugt sparsamere Modelle (mehr Bestrafung für viele Parameter)

---

###  Beispielausgabe: Top 10 Modelle

| p | d | q | AIC     | BIC     |
|---|---|---|---------|---------|
| 2 | 0 | 2 | 1234.56 | 1250.78 |
| 1 | 0 | 1 | 1240.91 | 1251.33 |
| 3 | 0 | 0 | 1243.12 | 1257.01 |
| 2 | 0 | 1 | 1244.88 | 1258.90 |
| 1 | 0 | 2 | 1246.78 | 1260.22 |

Aus dieser Tabelle erkennt man, dass das Modell mit **(p=2, d=0, q=2)** den niedrigsten AIC aufweist und damit aktuell das beste Kandidatenmodell ist.

---

###  Fazit Schritt 2

- Durch eine systematische Suche über alle Modellkombinationen wurde das ARIMA-Modell mit der besten Balance aus **Güte** und **Komplexität** identifiziert.
- Das Kriterium der Wahl war der **Akaike Information Criterion (AIC)**, optional ergänzt durch **BIC** zur Überprüfung.
- Dieses Modell kann nun im nächsten Schritt geschätzt und validiert werden.


##  Schritt 3: Visuelle Forecast-Analyse mit gestuften Trainingsdaten

Nachdem wir mit Hilfe von AIC/BIC ein vielversprechendes ARIMA(p,d,q)-Modell ausgewählt haben (z. B. ARIMA(2,0,0)), wollen wir dieses Modell nun **visuell testen**, und zwar in verschiedenen Phasen der Zeitreihe.

---

###  Ziel: Forecast-Güte über mehrere Zeitabschnitte hinweg beurteilen

Statt das Modell nur einmal auf die gesamte Serie zu trainieren, wird die Zeitreihe stufenweise aufgeteilt:

| Trainingsanteil | Forecastzeitraum | Ziel |
|------------------|------------------|------|
| 40 %             | nächste 15 %     | Frühprognose auf kurzer Datenbasis  
| 55 %             | nächste 15 %     | Stabileres Modell  
| 70 %             | nächste 15 %     | Fast vollständiges Modell  
| 85 %            | nächste 15% | Fast vollständiges Modell 
| 100 %            | +15 % extrapoliert| Finaler Forecast über den Rand hinaus  

So können wir grafisch nachvollziehen, **wie stabil und robust** das Modell über verschiedene Zeitabschnitte funktioniert.

---

###  Methodik

1. **Split der Zeitreihe**  
   Die Serie wird schrittweise in 5 Etappen unterteilt (40 %, 55 %, 70 %, 85 %, 100 %).

2. **Modellanpassung je Etappe**  
   Für jede Teilserie wird das **ARIMA(p,d,q)-Modell** neu geschätzt.

3. **Prognoseberechnung**  
   Für jede Etappe wird ein Forecast über eine feste Länge (z. B. 15 % der Gesamtlänge) berechnet.

4. **Visualisierung**  
  

---

###  Beispielhafte Plotbeschreibung

Wenn z. B. die Zeitreihe 1000 Werte hat, ergibt sich bei 80 % Training und 20 % Forecast:

- **Trainingsbereich**: Werte 0 bis 799  
- **Forecast**: Werte 800 bis 999  
- **Ziel**: Prüfen, ob der Forecast strukturell dem tatsächlichen Verlauf folgt


---

###  Fazit Schritt 3

- Durch die gestufte Visualisierung bekommt man ein Gefühl dafür, **wann das ARIMA-Modell stabil prognostiziert** und wo es evtl. versagt.
- Diese Methode ist **besonders hilfreich**, um **Overfitting bei kurzer Trainingsphase** zu erkennen.
- In Kombination mit der vorherigen AIC/BIC-Auswahl ist das ein robuster Weg, um die Prognosequalität eines Zeitreihenmodells visuell zu prüfen.



##  Schritt 4: Rolling Forecasts auf Originalskala (zurücktransformiert)

Nachdem wir ein geeignetes ARIMA-Modell trainiert und erste Forecasts durchgeführt haben, folgt nun der **entscheidende Praxisschritt**:  
Wir zeigen **rollierende Forecasts** – aber diesmal **auf der realen Preisskala**, nicht auf den log-differenzierten Werten.

---

###  Ziel: Modellgüte auf der ursprünglichen Skala beurteilen

Das Modell wurde auf **log-transformierten und differenzierten Daten** trainiert, da diese Transformation stationär macht.  
Jetzt kehren wir die Transformation wieder um:

1. **Kumulative Summe** der differenzierten Forecasts  
2. **Addition des letzten Log-Wertes** vor dem Forecast  
3. **Exponentialfunktion**, um von$log(X)$zurück zu$X$zu kommen

> Ergebnis: Ein Forecast der tatsächlichen Preisentwicklung 

---

###  Methodik

#### Rolling Forecast-Stufen:

| Trainingsanteil | Forecastzeitraum  | Zielsetzung |
|-----------------|-------------------|-------------|
| 40 %            | nächste 15 %      | Erste Prognose auf kleiner Datenbasis  
| 55 %            | nächste 15 %      | Etwas robuster  
| 70 %            | nächste 15 %      | Fast vollständige Datenlage  
| 85 %            | nächste 15 %      | Fast vollständige Datenlage 
| 100 %           | +15 % extrapoliert| Finaler Forecast über den Rand hinaus   

#### Vorgehen pro Stufe:

- **Modelltraining** auf einem festen Anteil der geloggten Serie
- **Forecast** über die nächsten 15 % (ab Trainingsende)
- **Zurücktransformation**:
  - Letzter Log-Wert + kumulierte Differenzen
  - Dann: `exp()` anwenden → Originalwerte
- **Plot-Vergleich**:
  - Hellgrau = vollständige Originalserie
  - Rot = Forecast (auf Originalskala)
  - Blau (gestrichelt) = Tatsächliche Entwicklung (sofern bekannt)

---

###  Visualisierungsziel

Ein Plot pro Stufe zeigt:

- Wie **gut das Modell zukünftige Entwicklungen approximiert**
- Ob es **Trendwenden erkennt**
- Ob es **überschätzt oder unterschätzt**
- Wo es z. B. **zu langsam reagiert**

Die letzte Grafik (100 %) zeigt die echte **Zukunftsprognose** – also was wir erwarten würden, wenn der bisherige Verlauf anhielte.


---

## Darstellungen unserer Vorhersagen

### **Daimler**
![Daimler](/data_analytics/ergebnisse/daimler_unified_time_series_stock_price_plot.png)

### **Microsoft**
![Microsoft](/data_analytics/ergebnisse/microsoft_unified_time_series_stock_price_plot.png)

### **Samsung**
![Samsung](/data_analytics/ergebnisse/samsung_unified_time_series_stock_price_plot.png)

---



---

 
## Schritt 5: Residuenanalyse & Modell-Diagnose (ARIMA)

Ein statistisches Modell wie ARIMA ist nur dann sinnvoll, wenn seine **Fehler (Residuen)** bestimmten Annahmen genügen.  
Die Analyse der Residuen zeigt uns, ob unser Modell sauber gearbeitet hat – oder ob es noch systematische Muster oder Schwächen gibt.

---

### Ziel der Residuenanalyse:

Ein gutes Modell hinterlässt **"weißes Rauschen"** – das heißt:

- Fehler sind zufällig verteilt
- Es gibt **keine Autokorrelation**
- Die Fehler sind **normalverteilt**
- Die Varianz ist konstant
- Der Mittelwert der Fehler ist nahe **null**

---

### Wichtige Tests zur Residuenanalyse

#### 1. Ljung-Box Test

Der Ljung-Box-Test prüft, ob es **Autokorrelation** in den Residuen gibt – also ob heutige Fehler mit vergangenen Fehlern zusammenhängen.

- **Nullhypothese**: Es gibt **keine Autokorrelation** → gut!
- **Interpretation**:  
  - Wenn **p-Wert > 0.05** → Fehler sind unkorreliert (✓)
  - Wenn **p-Wert ≤ 0.05** → Es gibt Korrelation (✗), Modell evtl. unzureichend

#### 2. Jarque-Bera Test

Der Jarque-Bera-Test prüft, ob die Residuen **normalverteilt** sind.

- **Nullhypothese**: Die Residuen sind **normalverteilt**
- **Interpretation**:  
  - Wenn **p-Wert > 0.05** → Normalverteilung gegeben (✓)
  - Wenn **p-Wert ≤ 0.05** → Abweichung von Normalverteilung (✗), Modellfehler oder Transformation nötig

---

### Weitere Plots zur Diagnose

| Plot                         | Beschreibung                                 | Ziel                                         |
|------------------------------|----------------------------------------------|----------------------------------------------|
| **Residuen-Zeitreihe**       | Fehler über die Zeit                         | Keine offensichtlichen Muster, keine Trends |
| **Histogramm + Normalverteilung** | Vergleich Fehlerverteilung vs. Normalverteilung | Möglichst symmetrisch und glockenförmig     |
| **Q-Q-Plot (Quantil-Quantil)** | Prüft Normalverteilung grafisch              | Punkte sollten auf Diagonale liegen         |

---

### Statistische Modell-Zusammenfassung (`model_fit.summary()`)

- Zeigt **Koeffizienten** mit **t-Statistiken**
- Wenn `|t| > 2`, dann sind die Parameter häufig **statistisch signifikant**
- AIC und BIC helfen bei Modellvergleich

---

## RESIDUENANALYSE - ZUSAMMENFASSUNG

**SAMSUNG:**  
- Ljung-Box Test (p-Wert): 0.9722  
  → ✓ Keine Autokorrelation  
- Jarque-Bera Test (p-Wert): 0.0000  
  → ✗ Keine Normalverteilung  
- Residuen Mittelwert: 0.000002  
- Residuen Std: 0.0215  

**DAIMLER:**  
- Ljung-Box Test (p-Wert): 0.0470  
  → ✗ Autokorrelation vorhanden  
- Jarque-Bera Test (p-Wert): 0.0000  
  → ✗ Keine Normalverteilung  
- Residuen Mittelwert: 0.000214  
- Residuen Std: 0.0213  

**MICROSOFT:**  
- Ljung-Box Test (p-Wert): 0.0000  
  → ✗ Autokorrelation vorhanden  
- Jarque-Bera Test (p-Wert): 0.0000  
  → ✗ Keine Normalverteilung  
- Residuen Mittelwert: 0.000447  
- Residuen Std: 0.0212  

---

## INTERPRETATION

- **Samsung:**  
  Residuen scheinen unkorreliert, aber nicht normalverteilt. Das Modell beschreibt die Dynamik gut, könnte aber bei Verteilung der Fehler verbessert werden (z. B. durch Transformationen oder robustere Verfahren).

- **Daimler & Microsoft:**  
  Beide Modelle zeigen **Autokorrelation** und **keine Normalverteilung** der Residuen. Das deutet darauf hin, dass hier noch **systematische Muster** in den Daten nicht erfasst wurden.  
  → Empfehlung: Modellierung (p,d,q) überdenken oder alternative Modelle wie SARIMA oder GARCH in Betracht ziehen.

---

## Fazit Schritt 5

- Die **Residuenanalyse zeigt**, ob die statistischen Annahmen des ARIMA-Modells erfüllt sind.
- Nur wenn die Fehler zufällig, unkorreliert und normalverteilt sind, ist die Prognose statistisch **zuverlässig**.
- Abweichungen (wie bei Daimler/Microsoft) zeigen: Modell ggf. **nicht ausreichend angepasst**.

---

