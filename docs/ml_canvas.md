# ML Canvas — Spotify Hit Predictor

## 1. Nombre del proyecto

**Spotify Hit Predictor**

Sistema de apoyo para estimar si una canción tiene potencial de alcanzar una popularidad igual o superior a 50 en Spotify.

---

## 2. Problema de negocio

Un equipo A&R o sello discográfico debe revisar muchas canciones antes de decidir cuáles reciben promoción, inversión, distribución o seguimiento comercial.

Revisar todo el catálogo manualmente requiere tiempo, presupuesto y criterio especializado. El problema es que algunas canciones con potencial pueden quedar fuera de revisión, mientras que otras reciben atención sin llegar a tener un desempeño relevante.

El proyecto busca reducir esa incertidumbre mediante un modelo que priorice canciones con mayor probabilidad de ser populares.

---

## 3. Usuario final

| Usuario | Necesidad |
|---|---|
| Equipo A&R | Priorizar canciones para revisión humana |
| Analista musical | Explorar patrones relacionados con popularidad |
| Equipo comercial | Definir campañas de scouting o promoción |
| Líder de negocio | Comparar impacto, volumen de revisión y riesgos |

---

## 4. Objetivo

Predecir si una canción alcanzará una popularidad igual o superior a 50 en Spotify, usando características de audio, duración, explicitud y géneros musicales.

La predicción se utiliza como apoyo para ordenar canciones según su potencial de popularidad.

---

## 5. Predicción

| Elemento | Definición |
|---|---|
| Tipo de predicción | Clasificación binaria |
| Variable objetivo | `popular` |
| Clase positiva | Popularidad mayor o igual a 50 |
| Salida del modelo | Probabilidad de que una canción sea popular |
| Decisión final | Clasificación según un umbral de probabilidad |

---

## 6. Estrategias de uso

| Estrategia | Umbral | Uso principal |
|---|---:|---|
| Equilibrada | 0.55 | Priorización regular cuando A&R tiene capacidad limitada |
| Descubrimiento | 0.35 | Scouting amplio o campañas donde se desea detectar más canciones potencialmente populares |

La estrategia equilibrada prioriza un balance entre precision y recall.

La estrategia de descubrimiento prioriza recall, por lo que detecta más canciones populares, pero también incrementa los falsos positivos y el volumen de revisión humana.

---

## 7. Datos

### Fuente de datos

Dataset académico de canciones de Spotify con información de audio, popularidad y género.

### Unidad de análisis

| Etapa | Unidad |
|---|---|
| Dataset original | Registro canción-género |
| Dataset final | Canción única identificada mediante `track_id` |

### Tamaño de datos

| Etapa | Registros |
|---|---:|
| Dataset original | 114,000 |
| Dataset final procesado | 89,730 |
| Train | 62,808 |
| Validation | 13,462 |
| Test | 13,460 |

---

## 8. Variables utilizadas

### Variables de audio

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
| `genres` | Géneros asociados a la canción |
| `rhythm_missing` | Identifica ausencia técnica de tempo o compás |
| `energy_tempo` | Interacción entre energía y tempo |
| `is_long_track` | Identifica canciones de 10 minutos o más |

### Variables excluidas del modelo

| Variable | Motivo |
|---|---|
| `track_id` | Identificador técnico |
| `artists` | Referencia, no predictor |
| `album_name` | Referencia, no predictor |
| `track_name` | Referencia, no predictor |
| `popularity` | Se usa para construir la variable objetivo |

---

## 9. Preparación de datos

El proceso de preparación incluyó:

1. Eliminación de columnas de índice no informativas.
2. Eliminación de un registro inválido.
3. Consolidación de canciones repetidas mediante `track_id`.
4. Agregación de géneros múltiples en una sola variable.
5. Exclusión de canciones ambiguas cercanas al umbral de popularidad.
6. Conversión de valores de tempo y compás iguales a cero en valores faltantes técnicos.
7. Imputación de valores faltantes por mediana.
8. Escalado de variables numéricas.
9. Codificación multi-etiqueta de géneros.
10. División estratificada de train, validation y test.

---

## 10. Modelo seleccionado

| Elemento | Valor |
|---|---|
| Modelo | HistGradientBoostingClassifier |
| Método de tuning | RandomizedSearchCV |
| Número de configuraciones evaluadas | 12 |
| Validación cruzada | 3 folds |
| Métrica de optimización | PR-AUC |
| Número total de features | 131 |
| Random state | 42 |

---

## 11. Métricas de evaluación

Se utilizaron las siguientes métricas:

| Métrica | Propósito |
|---|---|
| Accuracy | Proporción total de predicciones correctas |
| Precision | Qué porcentaje de canciones predichas como populares realmente lo son |
| Recall | Qué porcentaje de canciones populares reales logra detectar el modelo |
| F1-score | Balance entre precision y recall |
| ROC-AUC | Capacidad de ordenar canciones populares por encima de no populares |
| PR-AUC | Desempeño del modelo ante clases desbalanceadas |

---

## 12. Resultados finales en test

### Estrategia equilibrada

| Métrica | Resultado |
|---|---:|
| Umbral | 0.55 |
| Accuracy | 80.13% |
| Precision | 55.95% |
| Recall | 74.84% |
| F1-score | 64.03% |
| ROC-AUC | 0.870 |
| PR-AUC | 0.686 |

### Estrategia de descubrimiento

| Métrica | Resultado |
|---|---:|
| Umbral | 0.35 |
| Accuracy | 70.29% |
| Precision | 43.76% |
| Recall | 90.35% |
| F1-score | 58.96% |
| ROC-AUC | 0.870 |
| PR-AUC | 0.686 |

---

## 13. Impacto de negocio

Se utilizaron valores ilustrativos para simular el impacto de las decisiones:

| Resultado | Valor ilustrativo |
|---|---:|
| Verdadero positivo | +$10,000 |
| Falso positivo | -$2,000 |
| Falso negativo | -$8,000 |
| Verdadero negativo | $0 |

| Estrategia | Impacto estimado | Canciones para revisión |
|---|---:|---:|
| Equilibrada — 0.55 | $13,652,000 | 4,254 |
| Descubrimiento — 0.35 | $18,890,000 | 6,565 |

Los valores económicos son académicos e ilustrativos; no representan ingresos garantizados.

---

## 14. Integración propuesta


Nueva canción
      ↓
Extracción de audio features y género
      ↓
Preprocesamiento
      ↓
Modelo HistGradientBoosting
      ↓
Probabilidad de popularidad
      ↓
Selección de umbral
      ↓
Lista priorizada para revisión A&R


---


## 15. Riesgos, fairness y evaluación ética

La evaluación de fairness se realizó sobre el conjunto de test mediante dos enfoques:

1. Evaluación individual por género musical.
2. Comparación agrupada entre segmentos Mainstream y Latino / emergente.

Los resultados se interpretan como un **riesgo de cobertura del modelo**. No representan evidencia automática de discriminación, ya que los grupos corresponden a segmentos musicales y presentan tasas base de popularidad distintas.

### Fairness por género individual

Se evaluaron 76 géneros con soporte suficiente.

Para evitar métricas inestables, se consideraron únicamente géneros con:

- Al menos 100 canciones.
- Al menos 20 canciones populares reales.

La estrategia de descubrimiento puede reducir el F1-score en algunos géneros, porque prioriza recall y acepta más falsos positivos.

| Género | Cambio de F1 con estrategia de descubrimiento |
|---|---:|
| hardcore | -11.66 puntos porcentuales |
| show-tunes | -11.43 puntos porcentuales |
| folk | -9.84 puntos porcentuales |
| swedish | -9.25 puntos porcentuales |
| j-rock | -8.82 puntos porcentuales |

### Fairness agrupado: Mainstream vs. Latino / emergente

Los grupos se construyeron únicamente con géneros disponibles en el dataset.

| Grupo | Géneros incluidos |
|---|---|
| Mainstream | `pop`, `edm`, `hip-hop`, `rock` |
| Latino / emergente | `afrobeat`, `indie`, `latino`, `reggaeton`, `salsa` |

Se excluyeron 56 canciones presentes en ambos grupos para evitar superposición.

| Grupo | Canciones | Populares reales |
|---|---:|---:|
| Mainstream | 521 | 246 |
| Latino / emergente | 563 | 91 |

### Resultados con estrategia equilibrada

| Grupo | Tasa de predicción positiva | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|
| Mainstream | 66.41% | 66.18% | 93.09% | 77.36% |
| Latino / emergente | 23.09% | 47.69% | 68.13% | 56.11% |

### Resultados con estrategia de descubrimiento

| Grupo | Tasa de predicción positiva | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|
| Mainstream | 82.34% | 56.64% | 98.78% | 72.00% |
| Latino / emergente | 34.81% | 37.24% | 80.22% | 50.87% |

### Brechas de fairness

| Estrategia | Brecha precision | Brecha recall | Brecha F1-score | Brecha tasa de predicción positiva |
|---|---:|---:|---:|---:|
| Equilibrada — 0.55 | +18.49 pp | +24.96 pp | +21.26 pp | +43.32 pp |
| Descubrimiento — 0.35 | +19.40 pp | +18.56 pp | +21.13 pp | +47.53 pp |

Ambas estrategias presentan una brecha de recall superior a 10 puntos porcentuales.

La estrategia de descubrimiento reduce la brecha de recall de 24.96 pp a 18.56 pp, pero todavía mantiene una diferencia relevante entre grupos.

### Medidas de mitigación

- Mantener revisión humana obligatoria antes de invertir, descartar o promover una canción.
- Monitorear precision, recall y F1-score por género y grupo musical en cada nueva versión.
- Revisar manualmente canciones Latino / emergente con probabilidades cercanas al umbral.
- Incorporar mayor representación histórica de géneros con menor desempeño.
- No utilizar el modelo como criterio único de inversión, contratación o exclusión.
- Usar la estrategia de descubrimiento únicamente cuando exista capacidad adicional de revisión humana.
