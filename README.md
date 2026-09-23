# Spotify Tracks ML

**Spotify Tracks ML** es un proyecto de Machine Learning desarrollado en Python que busca predecir si una canción puede alcanzar un nivel alto de popularidad en Spotify a partir de sus características musicales. El proyecto está orientado a apoyar la toma de decisiones de equipos **A&R (Artists and Repertoire)**, permitiendo priorizar canciones con mayor potencial mediante un enfoque basado en datos.

El problema se aborda como una **clasificación binaria**, considerando como popular una canción con un valor de popularidad **mayor o igual a 60**. El modelo funciona como una herramienta de apoyo y no como un reemplazo del criterio humano.

## Funcionalidades principales

* Análisis exploratorio de más de **114 000 canciones**.
* Limpieza y preparación de los datos para Machine Learning.
* Creación de la variable objetivo de popularidad (`popularity >= 60`).
* Análisis de características musicales como `danceability`, `energy`, `loudness`, `acousticness`, `valence` y `tempo`.
* Procesamiento de variables numéricas y categóricas.
* Entrenamiento y comparación de diferentes modelos de clasificación.
* Evaluación mediante métricas como Precision, Recall, F1-Score y ROC-AUC.
* Análisis de matrices de confusión.
* Ajuste del umbral de clasificación según diferentes escenarios de negocio.
* Generación y almacenamiento de artefactos del modelo.
* Documentación del modelo mediante Model Card.
* Evaluación de impacto, riesgos y posibles sesgos.
* Documentación de gobernanza y cumplimiento de la LOPDP.
* Definición de responsabilidades mediante matrices RACI.

## Tecnologías utilizadas

* Python
* Pandas — manipulación y análisis de datos
* NumPy — operaciones numéricas
* Scikit-learn — preprocesamiento, entrenamiento y evaluación de modelos
* Matplotlib — generación de visualizaciones
* Jupyter Notebook — análisis y experimentación
* Joblib — almacenamiento de modelos y artefactos
* Git — control de versiones
* GitHub — gestión y documentación del proyecto

## Estructura del proyecto

```text
spotify-tracks-ml/
├── data/
│   ├── raw/                    # Datos originales
│   └── processed/              # Datos procesados
│
├── notebooks/
│   ├── eda/                    # Análisis exploratorio
│   └── modeling/               # Entrenamiento y evaluación
│
├── src/
│   ├── preprocessing/          # Limpieza y transformación
│   ├── features/               # Ingeniería de características
│   ├── models/                 # Entrenamiento de modelos
│   └── evaluation/             # Métricas y evaluación
│
├── models/
│   └── artifacts/              # Modelos y artefactos generados
│
├── docs/
│   ├── ml_canvas.md            # ML Canvas del proyecto
│   ├── raci_initial.md         # Matriz RACI inicial
│   ├── raci_final.md           # Matriz RACI final
│   ├── checklist_lopdp.md      # Checklist LOPDP
│   ├── model_card.md           # Model Card
│   ├── impact_assessment.md    # Evaluación de impacto
│   └── governance.md           # Gobierno del modelo
│
├── requirements.txt            # Dependencias
├── .gitignore                  # Archivos excluidos de Git
└── README.md                   # Documentación general
```

## Instalación

1. Clona el repositorio:

```bash
git clone https://github.com/gabycardenas1/spotify-tracks-ml.git
```

2. Ingresa al proyecto:

```bash
cd spotify-tracks-ml
```

3. Instala las dependencias:

```bash
pip install -r requirements.txt
```

4. Abre Jupyter Notebook:

```bash
jupyter notebook
```

## Modelo de Machine Learning

El proyecto utiliza un enfoque de **clasificación binaria**:

* `0` → canción con popularidad menor a 60.
* `1` → canción con popularidad mayor o igual a 60.

El dataset presenta aproximadamente la siguiente distribución:

| Clase      | Canciones | Porcentaje |
| ---------- | --------: | ---------: |
| No popular |    84 633 |    74.24 % |
| Popular    |    29 367 |    25.76 % |

Se comparan diferentes algoritmos de Machine Learning y se evalúan mediante métricas que permiten analizar tanto el rendimiento general como la capacidad del modelo para identificar canciones potencialmente populares.

## Ética y gobernanza

El proyecto incorpora prácticas de Machine Learning responsable mediante:

* Model Card.
* Evaluación de impacto.
* Checklist LOPDP.
* Análisis de posibles sesgos.
* Matrices RACI inicial y final.
* ML Canvas.
* Documentación de riesgos y limitaciones.
* Trazabilidad y versionado del desarrollo.

El modelo está diseñado como una **herramienta de apoyo para A&R**. Sus predicciones no representan una garantía de éxito comercial ni deben utilizarse como único criterio para seleccionar o descartar canciones o artistas.

## Autora
Gabriela Cárdenas

Estudiante de Ingeniería en Ciencias de Datos & Desarrollo de Software

Pontificia Universidad Católica del Ecuador (PUCE)

Universidad UTE (UTE)


## Dependencias

```text
pandas
numpy
scikit-learn
matplotlib
jupyter
joblib
```
