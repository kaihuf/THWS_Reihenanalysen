```mermaid
classDiagram

    namespace overall_preparation.ipynb {
        class DataImport {
            +download_stock_data()
        }
        
        class DataPreparation {
            +download_stock_data()
        }
    }
    
    namespace overall_analysis.ipynb {
        class StationaritätsTest {
            +stationaritaets_tests()
            +ADF-Test
            +PP-Test
            +KPSS-Test
        }
        
        class TransformationZurStationarität {
            +stationaritaets_transformationen
            +Differenzierung
            +Erste Differenz
            +Zweite Differenz
            +Logarithmische Transformation
            +Moving Average
            +HP-Filter (Hodrick-Prescott-Filter)
            +Simple Exponential Smoothing
        }
        
        class TransformationenAufStationaritätTesten {
            +teste_vorhandene_transformationen()
        }
        
        class ACFundPACFAnalyse {
            +acf_pacf_werte()
            +ACF
            +PACF
        }
        
        class AutoARIMAModell {
            +auto_arima_simple()
        }
        
        class BerechnenDerNächstenPerioden {
            +auto_arima_simple()
        }
        
        class Modelliagnose {
            +residual_analysis()
            +Ljung-Box Test / Portmanteau Test
            +Jarque-Bera Test
            +Residuenanalyse
        }
        
        class TStatistiken {
            +t-statistiken()
        }
    }
    
    %% Verbindungen
    DataImport --> DataPreparation
    DataPreparation --> StationaritätsTest
    StationaritätsTest --> TransformationZurStationarität
    TransformationZurStationarität --> TransformationenAufStationaritätTesten
    TransformationenAufStationaritätTesten --> ACFundPACFAnalyse
    ACFundPACFAnalyse --> AutoARIMAModell
    AutoARIMAModell --> BerechnenDerNächstenPerioden
    BerechnenDerNächstenPerioden --> Modelliagnose
    Modelliagnose --> TStatistiken
```