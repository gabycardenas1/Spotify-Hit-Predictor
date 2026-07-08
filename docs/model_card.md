# Model Card — Spotify Hit Predictor

## Predicción de popularidad de canciones mediante Machine Learning

| Elemento | Detalle |
|---|---|
| Proyecto | Spotify Hit Predictor |
| Repositorio | `spotify-tracks-ml` |
| Tipo de problema | Clasificación supervisada binaria |
| Variable objetivo | `popular` |
| Definición de clase positiva | `popular = 1` si `popularity >= 50` |
| Área de aplicación | Industria musical, analítica de canciones y apoyo a decisiones de promoción |
| Organización simulada | Sello discográfico independiente |
| Fecha de evaluación final | 30 de junio de 2026 |
| Unidad de análisis final | Canción única identificada mediante `track_id` |
| Modelo final | HistGradientBoostingClassifier |
| Estado | Evaluado sobre conjunto test y registrado en MLflow |

Documento de transparencia, desempeño, uso responsable, limitaciones, fairness y trazabilidad del modelo.

---

# 1. Información general del modelo

| Campo | Descripción |
|---|---|
| Nombre del modelo | Spotify Hit Predictor |
| Versión | 1.0 |
| Tipo de tarea | Clasificación supervisada binaria |
| Algoritmo seleccionado | HistGradientBoostingClassifier |
| Líder del proyecto | Gabriela Cárdenas — Project Owner |
| Objetivo del modelo | Estimar la probabilidad de que una canción alcance popularidad mayor o igual a 50 en Spotify |
| Salida esperada | Probabilidad de popularidad y clasificación binaria según un umbral de decisión |
| Usuario principal | Equipo A&R de un sello discográfico independiente |
| Uso principal | Priorización de canciones para revisión humana, scouting y análisis comercial |

---

# 2. Propósito del modelo

El modelo busca apoyar a un sello discográfico independiente en la priorización de canciones que podrían tener potencial de popularidad.

El sistema analiza características de audio, duración, explicitud, ritmo y géneros musicales para estimar la probabilidad de que una canción alcance una popularidad igual o superior a 50 en Spotify.

Su finalidad es reducir incertidumbre y organizar un catálogo de canciones para revisión por parte de equipos A&R, marketing y estrategia musical.

El modelo no reemplaza la evaluación artística, comercial ni estratégica de las personas responsables. Su resultado debe utilizarse únicamente como apoyo para priorizar revisión humana.

---

# 3. Usuarios previstos

| Usuario | Uso previsto |
|---|---|
| Equipo A&R | Priorizar canciones para revisión humana |
| Analistas musicales | Explorar patrones asociados con popularidad |
| Equipo comercial | Definir campañas de scouting o promoción |
| Equipo de marketing | Identificar grupos de canciones con potencial promocional |
| Equipo técnico | Monitorear desempeño, fairness y nuevas versiones |
| Líder de negocio | Comparar impacto, volumen de revisión y riesgos |

El modelo no está diseñado para realizar decisiones automáticas de inversión, contratación, exclusión de artistas o distribución musical.

---

# 4. Datos utilizados

El proyecto utiliza un dataset académico de canciones de Spotify con información de popularidad, metadatos musicales, características de audio y género.

## 4.1 Unidad de análisis

| Etapa | Unidad de análisis |
|---|---|
| Dataset original | Registro canción-género |
| Dataset procesado | Canción única identificada por `track_id` |

## 4.2 Tamaño de los datos

| Etapa | Registros |
|---|---:|
| Dataset original | 114,000 |
| Dataset procesado final | 89,730 |
| Train | 62,808 |
| Validation | 13,462 |
| Test | 13,460 |

La división se realizó de manera estratificada mediante `random_state = 42`.

## 4.3 Variables de referencia

Las siguientes variables se mantienen como referencia, pero no se usan como variables predictoras:

- `track_id`
- `artists`
- `album_name`
- `track_name`
- `popularity`

## 4.4 Variables predictoras

### Características musicales y técnicas

- `duration_ms`
- `explicit`
- `danceability`
- `energy`
- `key`
- `loudness`
- `mode`
- `speechiness`
- `acousticness`
- `instrumentalness`
- `liveness`
- `valence`
- `tempo`
- `time_signature`

### Variables derivadas

| Variable | Descripción |
|---|---|
| `genres` | Lista de géneros asociados a una canción |
| `rhythm_missing` | Indica ausencia técnica de tempo o compás |
| `energy_tempo` | Interacción entre energía y tempo |
| `is_long_track` | Indica si la canción dura 10 minutos o más |

## 4.5 Variable objetivo

```text
popular = 1, si popularity >= 50
popular = 0, si popularity < 50
````

La clase positiva representa canciones con popularidad igual o superior a 50.

---

# 5. Preparación de datos

El pipeline de preparación incluyó las siguientes acciones:

1. Eliminación de columnas de índice no informativas.
2. Eliminación de un registro inválido sin información útil de canción.
3. Consolidación de registros repetidos mediante `track_id`.
4. Agregación de múltiples géneros en una lista separada por `|`.
5. Exclusión de 10 canciones ambiguas cercanas al umbral de popularidad.
6. Conversión de valores iguales a cero en `tempo` y `time_signature` a valores faltantes técnicos.
7. Imputación de valores faltantes mediante mediana.
8. Escalado de variables numéricas.
9. Codificación multi-etiqueta de géneros musicales.
10. División estratificada de train, validation y test.

El modelo final utiliza 131 features: variables numéricas procesadas y variables derivadas de la codificación de géneros.

---

# 6. Modelo seleccionado

Se compararon modelos baseline y candidatos de clasificación. El modelo final fue seleccionado considerando desempeño técnico, capacidad de ranking, recall de canciones populares, F1-score e impacto de negocio ilustrativo.

| Elemento                  | Valor                          |
| ------------------------- | ------------------------------ |
| Modelo                    | HistGradientBoostingClassifier |
| Método de tuning          | RandomizedSearchCV             |
| Configuraciones evaluadas | 12                             |
| Validación cruzada        | 3 folds                        |
| Métrica de optimización   | PR-AUC                         |
| `learning_rate`           | 0.09589075535858266            |
| `max_iter`                | 337                            |
| `max_leaf_nodes`          | 52                             |
| `min_samples_leaf`        | 11                             |
| `l2_regularization`       | 0.014765043713594356           |
| `class_weight`            | balanced                       |
| `random_state`            | 42                             |
| Variables numéricas       | 17                             |
| Géneros codificados       | 114                            |
| Features totales          | 131                            |

---

# 7. Estrategias de decisión

El modelo entrega una probabilidad de popularidad. La clasificación final depende del umbral elegido.

| Estrategia     | Umbral | Uso recomendado                                                        |
| -------------- | -----: | ---------------------------------------------------------------------- |
| Equilibrada    |   0.55 | Priorización regular cuando el equipo A&R tiene capacidad limitada     |
| Descubrimiento |   0.35 | Campañas de scouting, exploración amplia y revisión de mayor cobertura |

La estrategia equilibrada prioriza un balance entre precision y recall.

La estrategia de descubrimiento prioriza recall, por lo que detecta más canciones populares, pero incrementa los falsos positivos y el volumen de canciones que requieren revisión humana.

---

# 8. Desempeño final en test

La evaluación final se realizó una sola vez sobre el conjunto test, después de definir el modelo y los umbrales usando train y validation.

## 8.1 Métricas globales

| Estrategia                   | Accuracy | Precision | Recall | F1-score | ROC-AUC | PR-AUC |
| ---------------------------- | -------: | --------: | -----: | -------: | ------: | -----: |
| Equilibrada — umbral 0.55    |   80.13% |    55.95% | 74.84% |   64.03% |   0.870 |  0.686 |
| Descubrimiento — umbral 0.35 |   70.29% |    43.76% | 90.35% |   58.96% |   0.870 |  0.686 |

## 8.2 Interpretación

### Estrategia equilibrada

La estrategia equilibrada alcanza un F1-score de 64.03%, con precision de 55.95% y recall de 74.84%.

Se recomienda para operación regular, porque permite detectar una proporción alta de canciones populares sin generar un volumen excesivo de revisiones.

### Estrategia de descubrimiento

La estrategia de descubrimiento alcanza un recall de 90.35%, por lo que identifica aproximadamente 9 de cada 10 canciones populares.

Sin embargo, su precision baja a 43.76%, porque incorpora más canciones que luego podrían no alcanzar la popularidad esperada.

Se recomienda para convocatorias, exploración amplia o campañas de scouting donde se desea reducir el riesgo de dejar pasar posibles éxitos.

---

# 9. Resultados por clase

## 9.1 Estrategia equilibrada — umbral 0.55

| Clase | Significado        | Precision | Recall | F1-score | Soporte |
| ----- | ------------------ | --------: | -----: | -------: | ------: |
| `0`   | Canción no popular |    91.31% | 81.77% |   86.28% |  10,280 |
| `1`   | Canción popular    |    55.95% | 74.84% |   64.03% |   3,180 |

## 9.2 Estrategia de descubrimiento — umbral 0.35

| Clase | Significado        | Precision | Recall | F1-score | Soporte |
| ----- | ------------------ | --------: | -----: | -------: | ------: |
| `0`   | Canción no popular |    95.55% | 64.09% |   76.72% |  10,280 |
| `1`   | Canción popular    |    43.76% | 90.35% |   58.96% |   3,180 |

La clase más relevante para el problema de negocio es `popular = 1`, porque representa canciones que podrían justificar revisión adicional, promoción o análisis comercial.

---

# 10. Matrices de confusión finales

## 10.1 Estrategia equilibrada — umbral 0.55

| Valor real / Predicción | No popular | Popular |
| ----------------------- | ---------: | ------: |
| No popular              |      8,406 |   1,874 |
| Popular                 |        800 |   2,380 |

| Indicador               | Valor |
| ----------------------- | ----: |
| Verdaderos positivos    | 2,380 |
| Falsos positivos        | 1,874 |
| Falsos negativos        |   800 |
| Verdaderos negativos    | 8,406 |
| Canciones para revisión | 4,254 |

## 10.2 Estrategia de descubrimiento — umbral 0.35

| Valor real / Predicción | No popular | Popular |
| ----------------------- | ---------: | ------: |
| No popular              |      6,588 |   3,692 |
| Popular                 |        307 |   2,873 |

| Indicador               | Valor |
| ----------------------- | ----: |
| Verdaderos positivos    | 2,873 |
| Falsos positivos        | 3,692 |
| Falsos negativos        |   307 |
| Verdaderos negativos    | 6,588 |
| Canciones para revisión | 6,565 |

---

# 11. Análisis de errores

| Tipo de error  | Descripción                                                                                                 | Impacto potencial                                                            |
| -------------- | ----------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| Falso positivo | El modelo predice que una canción será popular, pero no alcanza popularidad mayor o igual a 50              | Puede generar inversión o revisión adicional en canciones con bajo retorno   |
| Falso negativo | El modelo predice que una canción no será popular, pero realmente sí alcanza popularidad mayor o igual a 50 | Puede hacer que una canción con potencial quede fuera de campañas o revisión |

El falso negativo es especialmente relevante porque representa una oportunidad potencialmente perdida.

Sin embargo, los falsos positivos también deben controlarse debido a la capacidad limitada del equipo A&R para revisar, promover o invertir en canciones.

---

# 12. Impacto de negocio ilustrativo

Los valores monetarios utilizados son supuestos académicos. No representan retornos reales, ingresos garantizados ni decisiones financieras reales.

| Resultado          | Valor ilustrativo |
| ------------------ | ----------------: |
| Verdadero positivo |          +$10,000 |
| Falso positivo     |           -$2,000 |
| Falso negativo     |           -$8,000 |
| Verdadero negativo |                $0 |

| Estrategia            | Impacto estimado | Canciones para revisión |
| --------------------- | ---------------: | ----------------------: |
| Equilibrada — 0.55    |      $13,652,000 |                   4,254 |
| Descubrimiento — 0.35 |      $18,890,000 |                   6,565 |

La estrategia de descubrimiento genera mayor impacto estimado bajo estos supuestos, porque detecta más canciones populares. Sin embargo, requiere una capacidad de revisión humana considerablemente mayor.

---

# 13. Fairness y riesgos de cobertura

La evaluación de fairness se realizó sobre el conjunto test mediante dos enfoques:

1. Evaluación individual por género musical.
2. Evaluación agrupada entre segmentos Mainstream y Latino / emergente.

Los resultados se interpretan como riesgos de cobertura del modelo. No representan evidencia automática de discriminación, ya que los grupos corresponden a segmentos musicales y tienen tasas base de popularidad distintas.

## 13.1 Fairness por género individual

Se evaluaron 76 géneros con soporte suficiente.

Para evitar métricas inestables, se consideraron únicamente géneros con:

* Al menos 100 canciones.
* Al menos 20 canciones populares reales.

La estrategia de descubrimiento puede reducir el F1-score en algunos géneros, porque prioriza recall y acepta más falsos positivos.

| Género     | Cambio de F1 con estrategia de descubrimiento |
| ---------- | --------------------------------------------: |
| hardcore   |                    -11.66 puntos porcentuales |
| show-tunes |                    -11.43 puntos porcentuales |
| folk       |                     -9.84 puntos porcentuales |
| swedish    |                     -9.25 puntos porcentuales |
| j-rock     |                     -8.82 puntos porcentuales |

## 13.2 Fairness agrupado: Mainstream vs. Latino / emergente

Los grupos se construyeron únicamente con géneros disponibles en el dataset.

| Grupo              | Géneros incluidos                                   |
| ------------------ | --------------------------------------------------- |
| Mainstream         | `pop`, `edm`, `hip-hop`, `rock`                     |
| Latino / emergente | `afrobeat`, `indie`, `latino`, `reggaeton`, `salsa` |

Se excluyeron 56 canciones presentes en ambos grupos para evitar superposición.

| Grupo              | Canciones | Populares reales |
| ------------------ | --------: | ---------------: |
| Mainstream         |       521 |              246 |
| Latino / emergente |       563 |               91 |

### Resultados con estrategia equilibrada

| Grupo              | Tasa predicción positiva | Precision | Recall | F1-score |
| ------------------ | -----------------------: | --------: | -----: | -------: |
| Mainstream         |                   66.41% |    66.18% | 93.09% |   77.36% |
| Latino / emergente |                   23.09% |    47.69% | 68.13% |   56.11% |

### Resultados con estrategia de descubrimiento

| Grupo              | Tasa predicción positiva | Precision | Recall | F1-score |
| ------------------ | -----------------------: | --------: | -----: | -------: |
| Mainstream         |                   82.34% |    56.64% | 98.78% |   72.00% |
| Latino / emergente |                   34.81% |    37.24% | 80.22% |   50.87% |

## 13.3 Brechas observadas

| Estrategia            | Brecha precision | Brecha recall | Brecha F1 | Brecha tasa de predicción positiva |
| --------------------- | ---------------: | ------------: | --------: | ---------------------------------: |
| Equilibrada — 0.55    |        +18.49 pp |     +24.96 pp | +21.26 pp |                          +43.32 pp |
| Descubrimiento — 0.35 |        +19.40 pp |     +18.56 pp | +21.13 pp |                          +47.53 pp |

Ambas estrategias presentan una brecha de recall superior a 10 puntos porcentuales.

Esto indica que el modelo detecta canciones populares del grupo Mainstream con mayor frecuencia que canciones populares del grupo Latino / emergente.

---

# 14. Medidas de mitigación

Para reducir riesgos de cobertura y uso inadecuado del modelo, se recomienda:

1. Mantener revisión humana obligatoria antes de invertir, descartar o promover una canción.
2. Monitorear precision, recall y F1-score por género y por segmento musical en cada nueva versión.
3. Revisar manualmente canciones Latino / emergente con probabilidades cercanas al umbral.
4. Incorporar mayor representación histórica de géneros con menor desempeño.
5. Usar el umbral de descubrimiento cuando se requiera mayor cobertura de canciones potencialmente populares.
6. No utilizar el modelo como criterio único de inversión, contratación o exclusión.
7. Revisar el modelo cuando existan cambios importantes en tendencias musicales, comportamiento de usuarios o composición del catálogo.

---

# 15. Consideraciones éticas

El modelo utiliza datos musicales y de popularidad, no datos personales directos de oyentes o usuarios.

Sin embargo, puede influir en decisiones comerciales relacionadas con artistas, géneros y canciones. Por ello se consideran los siguientes principios:

* Transparencia sobre qué predice el modelo y qué variables utiliza.
* Revisión humana antes de decisiones relevantes.
* Prevención de sesgos contra géneros menos representados.
* Protección de diversidad creativa y musical.
* No interpretar popularidad como sinónimo absoluto de calidad artística.
* Documentación de limitaciones, riesgos y supuestos.
* Uso académico y analítico dentro del alcance definido.

La decisión ética vinculada al proyecto es:

> No se usarán datos personales de usuarios ni historial individual de escucha. El modelo se limita a atributos agregados de canciones y artistas, y su resultado funciona únicamente como apoyo para priorizar revisión humana.

---

# 16. Cumplimiento y manejo responsable de datos

| Principio                    | Estado  | Aplicación al proyecto                                                                     |
| ---------------------------- | ------- | ------------------------------------------------------------------------------------------ |
| Legalidad                    | Cumple  | Dataset utilizado con fines académicos y fuente documentada                                |
| Finalidad                    | Cumple  | Uso exclusivo para predicción académica de popularidad                                     |
| Minimización                 | Cumple  | Se usan variables necesarias para modelar                                                  |
| Calidad y exactitud          | Parcial | La popularidad puede cambiar con el tiempo; el dataset representa una fotografía histórica |
| Transparencia                | Cumple  | Model Card, ML Canvas, diccionario y Decision Log documentan uso, límites y variables      |
| Seguridad y confidencialidad | Cumple  | No se incluyen tokens, secretos ni credenciales en GitHub                                  |

El proyecto no utiliza datos personales directos como nombres de usuarios, correos electrónicos, documentos, direcciones, historial individual de escucha o datos sensibles.

---

# 17. Uso previsto

El modelo puede utilizarse para:

* Priorizar canciones para revisión humana.
* Apoyar campañas de scouting.
* Analizar patrones asociados con popularidad.
* Explorar catálogos musicales.
* Complementar dashboards, reportes y análisis de negocio.
* Generar listas de canciones ordenadas por probabilidad de popularidad.

---

# 18. Usos no recomendados

El modelo no debe utilizarse para:

* Decidir automáticamente qué artistas reciben inversión.
* Rechazar canciones únicamente por la predicción.
* Sustituir el criterio artístico, creativo o comercial.
* Garantizar éxito comercial.
* Evaluar talento individual exclusivamente con variables numéricas.
* Tomar decisiones contractuales o de distribución sin revisión humana.
* Inferir atributos personales de artistas o usuarios.
* Aplicarse fuera del contexto del dataset sin validación adicional.

---

# 19. Limitaciones

* La popularidad en Spotify cambia con el tiempo.
* El umbral `popularity >= 50` es una decisión analítica del proyecto.
* La popularidad depende de factores no incluidos en el modelo, como marketing, playlists, viralidad, redes sociales, comunidad de fans, colaboraciones y contexto cultural.
* Algunos géneros pueden estar mejor representados que otros.
* El modelo aprende patrones históricos, por lo que podría no anticipar nuevas tendencias musicales.
* El modelo no mide calidad artística, originalidad ni valor cultural.
* Los impactos monetarios son ilustrativos y no equivalen a ingresos reales.
* Las diferencias por género deben monitorearse en nuevas versiones.
* El modelo puede requerir recalibración cuando cambie el catálogo o el comportamiento de popularidad.

---

# 20. Trazabilidad y artefactos

| Artefacto                    | Ubicación                                                         |
| ---------------------------- | ----------------------------------------------------------------- |
| Dataset procesado            | `data/processed/spotify_model_dataset.csv`                        |
| Notebook de limpieza         | `notebooks/02_data_cleaning_feature_engineering.ipynb`            |
| Notebook de modelado         | `notebooks/03_modeling_baseline.ipynb`                            |
| Notebook de evaluación final | `notebooks/04_final_test_evaluation.ipynb`                        |
| Modelo final                 | `models/hgb_spotify_final.joblib`                                 |
| Preprocesador numérico       | `models/numeric_preprocessor_final.joblib`                        |
| Codificador de géneros       | `models/genre_encoder_final.joblib`                               |
| Metadata del modelo          | `models/model_metadata.json`                                      |
| Métricas finales             | `reports/tables/final_test_metrics.csv`                           |
| Impacto de negocio           | `reports/tables/final_business_impact.csv`                        |
| Fairness por género          | `reports/tables/fairness_comparison_test.csv`                     |
| Fairness agrupado            | `reports/tables/fairness_group_results_test.csv`                  |
| Brechas de fairness agrupado | `reports/tables/fairness_group_gaps_test.csv`                     |
| Matrices de confusión        | `reports/figures/final_confusion_matrices_test_spotify_style.png` |
| Registro de experimentación  | MLflow local con `mlflow.db`                                      |

## Registro de MLflow

| Campo                 | Valor                              |
| --------------------- | ---------------------------------- |
| Experimento           | `spotify_hit_predictor`            |
| Estado de ejecución   | `FINISHED`                         |
| Run ID                | `284bf9b4a80247e1873d4db72a3f7888` |
| ROC-AUC test          | 0.86982                            |
| PR-AUC test           | 0.686026                           |
| F1 equilibrado        | 0.640301                           |
| Recall descubrimiento | 0.903459                           |

---

# 21. Conclusión

Spotify Hit Predictor es un modelo de clasificación binaria que apoya la priorización de canciones según su probabilidad estimada de alcanzar una popularidad igual o superior a 50 en Spotify.

El modelo final obtuvo un ROC-AUC de 0.870 y PR-AUC de 0.686 sobre test. La estrategia equilibrada ofrece un F1-score de 64.03%, mientras que la estrategia de descubrimiento logra un recall de 90.35%.

El modelo puede aportar valor al proceso de revisión y priorización musical, siempre que sea utilizado como apoyo y no como mecanismo automático de inversión.

La evaluación de fairness identificó brechas relevantes entre segmentos Mainstream y Latino / emergente. Por ello, la recomendación principal es mantener revisión humana, monitorear métricas por grupo y evitar decisiones automáticas de exclusión o asignación de recursos.
