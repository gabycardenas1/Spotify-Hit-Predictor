# Diccionario de Datos — Spotify Popularity Prediction

## Información general

- **Fuente principal:** Spotify Tracks Dataset — Kaggle
- **Uso del dataset:** académico
- **Unidad de análisis inicial:** registro canción–género
- **Unidad de análisis final:** canción única identificada por `track_id`
- **Número inicial de registros:** 114.000
- **Número inicial de variables:** 22
- **Número final de registros para modelado:** 89.730 canciones únicas
- **Número final de variables:** 24
- **Archivo procesado:** `data/processed/spotify_model_dataset.csv`

> El dataset inicial contenía canciones repetidas en distintos géneros. Para evitar que la misma canción aparezca en train, validation y test, se consolidó una sola fila por `track_id`.

## Variables del dataset original

| Variable | Tipo | Rango / valores esperados | Descripción | Uso en el proyecto | Nivel LOPDP |
|---|---|---|---|---|---|
| `Unnamed: 0.1` | Entero | 0 a 113999 | Índice generado en una exportación previa del dataset. | Eliminada durante la limpieza; no es predictor. | No aplica |
| `Unnamed: 0` | Entero | 0 a 113999 | Índice generado en una exportación previa del dataset. | Eliminada durante la limpieza; no es predictor. | No aplica |
| `track_id` | Texto | Identificador alfanumérico de Spotify | Código técnico que identifica una canción dentro de Spotify. | Identificación, trazabilidad y consolidación de registros repetidos. No se utilizará como predictor. | No aplica |
| `artists` | Texto | Nombre de uno o más artistas | Artista o artistas asociados a la canción. | Trazabilidad; no se utilizará directamente por su alta cardinalidad. | No aplica |
| `album_name` | Texto | Nombre de álbum | Álbum al que pertenece la canción. | Trazabilidad; no predictor por alta cardinalidad. | No aplica |
| `track_name` | Texto | Nombre de canción | Título comercial de la pista. | Trazabilidad y revisión manual de calidad; no predictor directo. | No aplica |
| `popularity` | Decimal | 0 a 100 | Indicador de popularidad de Spotify. Después de consolidar duplicados, se obtiene mediante la mediana de la popularidad de cada `track_id`. | Se usa únicamente para construir la variable objetivo; no entra como predictor para evitar fuga de información. | No aplica |
| `duration_ms` | Entero | Milisegundos; valores positivos | Duración de la pista musical. | Predictor numérico y base para crear `is_long_track`. | No aplica |
| `explicit` | Booleano | `True` / `False` | Indica si la canción contiene contenido explícito. | Predictor binario potencial. | No aplica |
| `danceability` | Decimal | 0 a 1 | Medida de qué tan adecuada es una canción para bailar según ritmo, estabilidad y beat. | Predictor numérico. | No aplica |
| `energy` | Decimal | 0 a 1 | Intensidad y actividad percibida de la canción. | Predictor numérico y base para crear `energy_tempo`. | No aplica |
| `key` | Entero | 0 a 11 | Tonalidad musical estimada de la canción. | Predictor musical categórico. | No aplica |
| `loudness` | Decimal | Decibeles, aproximadamente de -60 a 5 | Intensidad sonora promedio de una canción. | Predictor numérico. | No aplica |
| `mode` | Entero | 0 o 1 | Modalidad musical: menor o mayor. | Predictor musical categórico. | No aplica |
| `speechiness` | Decimal | 0 a 1 | Presencia estimada de palabras habladas en una canción. | Predictor numérico. | No aplica |
| `acousticness` | Decimal | 0 a 1 | Nivel de confianza de que una canción sea acústica. | Predictor numérico. | No aplica |
| `instrumentalness` | Decimal | 0 a 1 | Probabilidad de que una canción no contenga voz. | Predictor numérico. | No aplica |
| `liveness` | Decimal | 0 a 1 | Probabilidad de que una canción haya sido grabada en vivo. | Predictor numérico. | No aplica |
| `valence` | Decimal | 0 a 1 | Positividad o tono emocional percibido de la canción. | Predictor numérico. | No aplica |
| `tempo` | Decimal | BPM; valor faltante cuando no existe medición válida | Velocidad estimada de la canción en beats por minuto. Los valores `0` del dataset original se reemplazaron por valores faltantes técnicos. | Predictor numérico; será imputado dentro del pipeline usando únicamente datos de entrenamiento. | No aplica |
| `time_signature` | Entero | Usualmente 3, 4 o 5; valor faltante cuando no existe medición válida | Compás estimado de la canción. Los valores `0` del dataset original se reemplazaron por valores faltantes técnicos. | Predictor musical; será imputado dentro del pipeline usando únicamente datos de entrenamiento. | No aplica |
| `track_genre` | Texto | 114 categorías musicales | Género musical original asociado a la pista. Una canción podía aparecer en varios géneros. | Se transformó en la variable consolidada `genres`; no se utiliza directamente en el dataset final. | No aplica |

## Variables creadas o transformadas para el modelado

| Variable | Tipo | Rango / valores esperados | Descripción | Uso en el proyecto | Nivel LOPDP |
|---|---|---|---|---|---|
| `genres` | Texto multi-etiqueta | Uno o varios géneros separados por `\|` | Géneros asociados a una canción después de consolidar las filas repetidas por `track_id`. Ejemplo: `alt-rock\|alternative\|rock`. | Predictor categórico multi-etiqueta. Se transformará dentro del pipeline de entrenamiento y será usado en análisis de fairness. | No aplica |
| `popular` | Entero | 0 o 1 | Variable objetivo creada desde `popularity`. Toma valor `1` cuando la popularidad consolidada es mayor o igual a 50; en caso contrario toma valor `0`. | Target de clasificación binaria. | No aplica |
| `rhythm_missing` | Entero | 0 o 1 | Indica ausencia de medición válida en `tempo` o `time_signature`. | Feature derivada para diferenciar una ausencia técnica de una medición rítmica real. | No aplica |
| `energy_tempo` | Decimal | Producto entre `energy` y `tempo`; puede ser faltante si no existe tempo válido | Aproximación al dinamismo musical de una canción. | Feature derivada; será imputada dentro del pipeline cuando corresponda. | No aplica |
| `is_long_track` | Entero | 0 o 1 | Indica si una pista dura 10 minutos o más. | Feature derivada para distinguir canciones convencionales de mixes, sets o contenido de larga duración. | No aplica |

## Transformaciones ejecutadas

Durante la preparación del dataset se realizaron las siguientes transformaciones:

1. Se eliminaron las columnas de índice `Unnamed: 0` y `Unnamed: 0.1`.
2. Se eliminó un registro incompleto con duración igual a cero y campos descriptivos vacíos.
3. Se consolidó el dataset a una fila por `track_id`.
4. Se preservaron todos los géneros asociados a cada canción mediante la variable multi-etiqueta `genres`.
5. Se calculó la mediana de `popularity` cuando una canción tenía varios registros.
6. Se excluyeron 10 canciones cuya popularidad podía cruzar el umbral de 50 entre registros duplicados.
7. Se creó la variable objetivo `popular`.
8. Se transformaron los valores `0` de `tempo` y `time_signature` en valores faltantes técnicos.
9. Se crearon las variables derivadas `rhythm_missing`, `energy_tempo` e `is_long_track`.
10. El dataset final fue dividido de manera estratificada en train, validation y test.

## Consideraciones de privacidad y LOPDP

No se identificaron datos personales de oyentes ni información individual sensible. El dataset contiene atributos de canciones, artistas, álbumes y géneros musicales.

Por tanto, no se procesa PII ni datos sensibles dentro del proyecto. Las variables `artists`, `album_name`, `track_name` y `track_id` se utilizan exclusivamente para trazabilidad, control de calidad y consolidación técnica de registros.