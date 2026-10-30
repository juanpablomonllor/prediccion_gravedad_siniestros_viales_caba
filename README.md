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

Proyecto de aprendizaje supervisado que busca anticipar si un siniestro vial ocurrido en la Ciudad Autónoma de Buenos Aires tendrá víctimas graves o fatales, a partir de la información disponible al inicio: tipo de vehículo, contraparte, tipo de vía, zona, horario y condiciones como la lluvia o el tipo de día.

La estimación podría servir como apoyo para priorizar recursos de emergencia y para identificar las combinaciones de mayor riesgo en políticas de seguridad vial.

## Pregunta y objetivo

**Pregunta:** ¿es posible anticipar si un siniestro vial va a tener víctimas graves o fatales a partir de las circunstancias en que ocurre?

**Objetivo general:** desarrollar y evaluar un modelo de clasificación supervisada que permita estimar si un siniestro vial resultará grave o mortal, a partir de sus características y de las condiciones en que ocurre.

**Tipo de problema:** clasificación binaria (0 = leve, 1 = grave o mortal).

## Estado del proyecto

| Entrega | Contenido | Estado |
|---|---|---|
| 1. Descripción y formulación del objetivo | Problema, objetivos, contexto, tipo de problema y modelos posibles | Entregada |
| 2. Descripción del dataset y origen | Repositorio, informe del dataset, diccionario de datos y notebook de exploración y preprocesamiento | En curso |
| 3. Presentación del modelo y análisis de resultados | Modelo, métricas, conclusiones y video | Pendiente |

## Datos

| Fuente | Uso en el proyecto |
|---|---|
| [Buenos Aires Data - Siniestros viales](https://data.buenosaires.gob.ar/dataset/victimas-siniestros-viales) (Observatorio de Movilidad y Seguridad Vial, GCBA, 2019-2025) | Dataset principal |
| [Open-Meteo](https://open-meteo.com) (datos meteorológicos históricos por hora) | Lluvia a la hora del siniestro |
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

## Tecnologías utilizadas

`Python` · `Pandas` · `NumPy` · `Matplotlib` · `Scikit-learn` · `Jupyter Notebook`

---

<p align="center">
  <strong>Juan Pablo Monllor</strong><br>
  Tecnicatura Superior en Ciencia de Datos e Inteligencia Artificial<br>
  2026
</p>
