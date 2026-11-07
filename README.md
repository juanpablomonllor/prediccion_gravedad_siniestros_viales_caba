<p align="center">
  <img src="docs/img/logo-pma.png" width="180" alt="Centro Politécnico Superior Malvinas Argentinas">
</p>

<h1 align="center">Predicción de la gravedad de siniestros viales en CABA</h1>

<p align="center">
  <strong>Trabajo parcial - Aprendizaje Automático 2026</strong><br>
  Tecnicatura Superior en Ciencia de Datos e Inteligencia Artificial<br>
  Centro Politécnico Superior Malvinas Argentinas
</p>

---

## Descripción

Proyecto de aprendizaje supervisado que busca anticipar si un siniestro vial ocurrido en la Ciudad Autónoma de Buenos Aires tendrá víctimas graves o fatales, a partir de la información disponible al inicio: tipo de vehículo, contraparte, tipo de vía, horario y condiciones como la lluvia, el tipo de día o la estación del año.

## Pregunta y objetivo

**Pregunta:** ¿es posible anticipar si un siniestro vial va a tener víctimas graves o fatales a partir de las circunstancias en que ocurre?

**Objetivo general:** desarrollar y evaluar un modelo de clasificación supervisada que permita estimar si un siniestro vial resultará grave o mortal, a partir de sus características y de las condiciones en que ocurre.

**Tipo de problema:** clasificación binaria (0 = leve, 1 = grave o mortal).

## Estado del proyecto

| Entrega | Contenido | Estado |
|---|---|---|
| 1. Descripción y formulación del objetivo | Problema, objetivos, contexto, tipo de problema y modelos posibles | Entregada |
| 2. Descripción del dataset y origen | Repositorio, [informe del dataset y diccionario de datos](docs/descripcion_dataset.md) y [notebook de exploración y preprocesamiento](notebooks/1.0-jpm-eda-preprocesamiento.ipynb) | Entregada |
| 3. Presentación del modelo y análisis de resultados | Modelo, métricas, conclusiones y video | Pendiente |

## Datos

| Fuente | Uso en el proyecto |
|---|---|
| [Buenos Aires Data - Siniestros viales](https://data.buenosaires.gob.ar/dataset/victimas-siniestros-viales) (Observatorio de Movilidad y Seguridad Vial, GCBA, 2019-2025) | Dataset principal |
| [Open-Meteo](https://open-meteo.com) (datos meteorológicos históricos por hora) | Lluvia en la hora previa al siniestro |
| [holidays](https://pypi.org/project/holidays/) (librería de Python) | Feriados nacionales de Argentina |

## Estructura del repositorio

```text
prediccion_gravedad_siniestros_viales_caba/
│
├── data/
│   ├── raw/          <- Datos originales, sin modificar
│   ├── external/     <- Datos de terceros (lluvia)
│   ├── interim/      <- Datos intermedios
│   └── processed/    <- Dataset final para el modelado
│
├── docs/             <- Documentación del dataset y del proyecto
├── models/           <- Modelos entrenados (Entrega 3)
├── notebooks/        <- Notebooks de Jupyter
├── references/       <- Material de consulta
├── reports/
│   └── figures/      <- Gráficos generados
│
├── requirements.txt  <- Librerías necesarias
└── README.md
```

## Cómo ejecutar el proyecto

Probado con Python 3.14 (Anaconda) y Jupyter en VS Code.

1. Clonar el repositorio:
   ```bash
   git clone https://github.com/juanpablomonllor/prediccion_gravedad_siniestros_viales_caba.git
   cd prediccion_gravedad_siniestros_viales_caba
   ```
2. Instalar las dependencias (las versiones necesarias están en `requirements.txt`):
   ```bash
   pip install -r requirements.txt
   ```
3. Abrir y ejecutar completa la notebook `notebooks/1.0-jpm-eda-preprocesamiento.ipynb`. Lee los datos de `data/raw` y `data/external`, y genera `data/processed/siniestros_limpio.csv`.

Las rutas a los datos son relativas a la carpeta `notebooks/`, por lo que no hace falta modificarlas si se mantiene la estructura del repositorio.

## Tecnologías utilizadas

`Python` · `Pandas` · `NumPy` · `Matplotlib` · `Scikit-learn` · `Jupyter Notebook`

---

<p align="center">
  <strong>Juan Pablo Monllor</strong><br>
  Tecnicatura Superior en Ciencia de Datos e Inteligencia Artificial<br>
  2026
</p>
