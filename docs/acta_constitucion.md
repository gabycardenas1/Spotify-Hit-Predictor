# Acta de Constitución del Proyecto

## Spotify Hit Predictor  
**Predicción de popularidad de canciones mediante Machine Learning**

---

## 1. Información general

| Elemento | Descripción |
|---|---|
| Asignatura | Ciencia de Datos para el Estudio de Casos |
| Nombre del proyecto | Spotify Hit Predictor |
| Organización simulada | Sello discográfico independiente |
| Fecha de inicio | 30 de junio de 2026 |
| Repositorio | `spotify-tracks-ml` |
| Fuente principal de datos | Spotify Tracks Dataset — Kaggle |
| Unidad de análisis | Canción única identificada mediante `track_id` |

---

## 2. Problema de negocio

Un sello discográfico independiente dispone de recursos limitados para invertir en producción, promoción y distribución de nuevas canciones.

Actualmente, muchas decisiones de inversión se realizan principalmente con criterio experto, sin una herramienta analítica que permita priorizar canciones con mayor potencial de popularidad.

El proyecto busca apoyar estas decisiones mediante un modelo de clasificación que estime si una canción tiene probabilidad de alcanzar un nivel relevante de popularidad en Spotify.

---

## 3. Objetivo general

Desarrollar un modelo de Machine Learning capaz de predecir si una canción será popular en Spotify a partir de sus características de audio, duración, contenido explícito y géneros musicales asociados.

---

## 4. Objetivos específicos

1. Analizar la calidad, estructura y distribución del dataset de Spotify.
2. Limpiar y consolidar registros repetidos para evitar fuga de información.
3. Construir una variable objetivo binaria llamada `popular`.
4. Crear variables derivadas que representen características musicales relevantes.
5. Entrenar y comparar modelos de clasificación.
6. Evaluar el rendimiento mediante métricas técnicas y de negocio.
7. Analizar posibles diferencias de desempeño entre grupos de géneros musicales.
8. Documentar decisiones, riesgos, limitaciones y consideraciones éticas.

---

## 5. Pregunta analítica

> Dadas las características musicales y técnicas de una canción, ¿es posible predecir si alcanzará una popularidad mayor o igual a 50 en Spotify?

---

## 6. Variable objetivo

La variable objetivo será:

```text
popular = 1, si popularity >= 50
popular = 0, si popularity < 50
