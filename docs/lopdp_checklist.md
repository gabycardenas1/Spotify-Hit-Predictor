# Checklist LOPDP — Spotify Hit Predictor

## Información general

| Elemento | Detalle |
|---|---|
| Proyecto | Spotify Hit Predictor |
| Finalidad | Predecir si una canción tiene potencial de alcanzar popularidad igual o superior a 50 en Spotify |
| Responsable del análisis | Equipo Spotify Tracks ML |
| Tipo de datos | Metadatos musicales y características de audio |
| Datos personales directos | No tratados |
| Fecha de revisión | 2026-06-30 |

---

## 1. Identificación de datos tratados

| Verificación | Estado | Evidencia / observación |
|---|---|---|
| Se identificaron las variables utilizadas | Cumple | El diccionario de datos documenta variables, tipos y transformaciones. |
| Se identificaron datos personales directos | No aplica | El dataset no contiene nombres, correos, teléfonos, documentos ni direcciones de personas. |
| Se identificaron datos indirectamente sensibles | Riesgo bajo | `artists` y títulos de canciones son datos públicos asociados a contenido musical. |
| Se excluyeron identificadores no necesarios para el modelo | Cumple | `track_id`, artista, álbum y nombre de canción se conservaron como referencia, pero no se usaron como variables predictoras. |
| Se revisó la presencia de datos sensibles | Cumple | No se identificaron atributos de salud, etnia, religión, orientación política, ubicación precisa u otras categorías sensibles. |

---

## 2. Finalidad y minimización de datos

| Verificación | Estado | Evidencia / observación |
|---|---|---|
| La finalidad del tratamiento está definida | Cumple | Apoyar la priorización de canciones para revisión humana por parte de un equipo A&R. |
| Se usan únicamente variables necesarias | Cumple | El modelo utiliza atributos de audio, explicitud, duración y géneros musicales. |
| Se evita utilizar información personal para predecir popularidad | Cumple | No se utilizan datos personales de artistas, oyentes o usuarios. |
| Las variables de referencia están separadas de las variables predictoras | Cumple | Las columnas de referencia se mantienen fuera de `feature_columns`. |
| Se limita el uso del modelo a apoyo de decisión | Cumple | El modelo no toma decisiones automáticas de inversión ni reemplaza el criterio humano. |

---

## 3. Seguridad y acceso

| Verificación | Estado | Evidencia / observación |
|---|---|---|
| El repositorio mantiene estructura organizada | Cumple | Datos, notebooks, modelos, reportes y documentación están separados por carpetas. |
| Los archivos temporales o internos se excluyen del repositorio | Cumple | `.gitignore` excluye `mlflow.db`, `mlartifacts/`, `mlruns/` e `.ipynb_checkpoints/`. |
| Los artefactos del modelo están versionados | Cumple | Se guardan el modelo final, preprocesador, codificador de géneros y metadata. |
| Se evita publicar credenciales o secretos | Cumple | El proyecto no requiere tokens, llaves API ni credenciales de usuarios. |
| Se puede reproducir el entrenamiento | Cumple | Se usa `random_state=42`, notebooks versionados y metadata del modelo. |

---

## 4. Transparencia y trazabilidad

| Verificación | Estado | Evidencia / observación |
|---|---|---|
| Existe diccionario de datos | Cumple | `docs/data_dictionary.md`. |
| Existe registro de decisiones | Cumple | `docs/decision_log.md`. |
| Existe documentación del modelo | Cumple | `docs/model_card.md`. |
| Existe registro de experimentación | Cumple | MLflow registra parámetros, métricas, artefactos y modelo final. |
| Se documentan supuestos de negocio | Cumple | Los valores de TP, FP y FN son ilustrativos y están declarados en los notebooks. |
| Se comunican limitaciones | Cumple | Se reportan diferencias por género, riesgo de cobertura y necesidad de revisión humana. |

---

## 5. Evaluación de impacto y fairness

| Verificación | Estado | Evidencia / observación |
|---|---|---|
| Se evaluó desempeño global | Cumple | Se reportan Accuracy, Precision, Recall, F1-score, ROC-AUC y PR-AUC sobre test. |
| Se evaluó desempeño por grupos | Cumple | Se evaluó fairness por género musical con grupos de soporte suficiente. |
| Se definieron criterios de inclusión para grupos | Cumple | Se consideran géneros con al menos 100 canciones y 20 canciones populares reales. |
| Se identificaron grupos con menor desempeño | Cumple | Se documentaron brechas de F1-score y recomendaciones de monitoreo. |
| Se evita interpretar resultados como discriminación automática | Cumple | Los géneros musicales no son atributos personales protegidos; se reporta como riesgo de cobertura. |
| Existe mitigación propuesta | Cumple | Monitoreo por género y revisión humana adicional para segmentos con desempeño bajo. |

---

## 6. Retención y uso responsable

| Verificación | Estado | Evidencia / observación |
|---|---|---|
| Se limita el uso del modelo al propósito declarado | Cumple | Priorización y apoyo a evaluación de canciones. |
| El modelo no debe usarse como criterio único de inversión | Cumple | La decisión final corresponde a revisión humana del equipo A&R. |
| Se recomienda reentrenamiento y monitoreo periódico | Cumple | Las métricas, impacto y fairness deben revisarse en cada nueva versión. |
| Se recomienda validar la fuente de datos antes de producción | Pendiente | En un despliegue real se debe verificar licencia, procedencia y actualización del dataset. |
| Se recomienda evaluación legal antes de usar datos de usuarios reales | Pendiente | Si el sistema incorpora datos de oyentes o artistas no públicos, debe realizarse una evaluación específica de privacidad. |

---

## Conclusión

El proyecto utiliza datos musicales y metadatos públicos, sin tratamiento de datos personales directos ni categorías sensibles. Se aplican principios de minimización, trazabilidad, control de acceso y uso responsable.

El modelo debe mantenerse como una herramienta de apoyo para el equipo A&R. No debe utilizarse para decisiones totalmente automatizadas de inversión, contratación, exclusión de artistas o asignación de recursos sin revisión humana.