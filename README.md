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

## Abstract

This project applies supervised machine learning to estimate whether a road traffic crash in the City of Buenos Aires (CABA), Argentina, will be severe or fatal, using characteristics that are known when the crash is first reported. The goal is to support the prioritization of emergency resources. The main dataset contains the open crash records published by the city government for 2019–2025 (65,818 crashes with at least one victim and 21 columns), complemented with hourly precipitation from Open-Meteo (used as a retrospective approximation) and Argentine public holidays. After removing records with missing vehicle or counterpart data and excluding variables that would reveal the outcome, 51,144 crashes remained, 6.8% of them severe or fatal. The data were split by time: 2019–2024 for training and 2025 as a held-out test set. Logistic regression, a decision tree and k-nearest neighbors were tuned with time-based cross-validation on the training data only, and the decision threshold was also selected on validation data. Logistic regression was chosen because it detected more severe crashes and is easier to interpret. On the 2025 test set, it identified 63.4% of severe or fatal crashes (recall) with a precision of 12.3%, about 1.7 times better than random selection, but with many false alarms. The strongest associations were pedestrians, motorcycles, fixed objects, trains, highways and early-morning hours. The model can support prioritization, but it should not be used as a standalone classifier, and its results apply only to crashes in CABA with complete vehicle information.

## Pregunta y objetivo

**Pregunta:** ¿es posible anticipar si un siniestro vial va a tener víctimas graves o fatales a partir de las circunstancias en que ocurre?

**Objetivo general:** desarrollar y evaluar un modelo de clasificación supervisada que permita estimar si un siniestro vial resultará grave o mortal, a partir de sus características y de las condiciones en que ocurre.

**Tipo de problema:** clasificación binaria (0 = leve, 1 = grave o mortal).

## Definición del problema

| Pregunta | Respuesta |
|---|---|
| ¿Qué representa cada fila? | Un siniestro vial con al menos una víctima, ocurrido en CABA entre 2019 y 2025 |
| ¿Qué se predice? | Si el siniestro será leve (0) o grave o mortal (1) |
| ¿Cuándo se hace la predicción? | Al recibir el aviso del siniestro, antes de conocer su resultado |
| ¿Con qué información? | Vehículo de la víctima, contraparte, tipo de vía, hora, tipo de día, estación y lluvia en la hora previa (esta última, como aproximación retrospectiva) |
| ¿Para qué sirve? | Como apoyo para priorizar recursos de emergencia ante varios avisos simultáneos |
| ¿Cómo produce el resultado? | Una regresión logística calcula un puntaje de riesgo y, si supera el umbral de 0,50, clasifica el siniestro como grave o mortal |
| ¿Cómo se usa con un caso nuevo? | Se cargan las siete predictoras del aviso en el modelo guardado (ver la sección 12 de la notebook de modelado) |

## Datos

| Fuente | Uso en el proyecto |
|---|---|
| [Buenos Aires Data - Siniestros viales](https://data.buenosaires.gob.ar/dataset/victimas-siniestros-viales) (Observatorio de Movilidad y Seguridad Vial, GCBA, 2019-2025) | Dataset principal |
| [Open-Meteo](https://open-meteo.com) (datos meteorológicos históricos por hora) | Lluvia en la hora previa al siniestro |
| [holidays](https://pypi.org/project/holidays/) (librería de Python) | Feriados nacionales de Argentina |

## Documentación y orden de lectura

| Orden | Documento | Contenido |
|---|---|---|
| 1 | [Descripción del dataset](docs/descripcion_dataset.md) | Origen de los datos, diccionario de datos, glosario y resumen del preprocesamiento |
| 2 | [Exploración y preprocesamiento](notebooks/1.0-jpm-eda-preprocesamiento.ipynb) | Carga, análisis exploratorio, tratamiento de los registros sin dato, variables nuevas y limpieza |
| 3 | [Modelado y evaluación](notebooks/2.0-jpm-modelado.ipynb) | Partición temporal, comparación de modelos, elección de hiperparámetros y umbral, evaluación final, interpretación y ejemplo de uso |

## Resultados principales

Se compararon tres modelos con validación cruzada temporal sobre el período 2019-2024 (métricas de la clase grave o mortal, umbral 0,5):

| Modelo | Precisión | Recall | F1 |
|---|---|---|---|
| Regresión logística (C = 1) | 0,096 | 0,727 | 0,168 |
| Árbol de decisión (profundidad 8) | 0,097 | 0,663 | 0,167 |
| KNN (K = 5) | 0,168 | 0,066 | 0,069 |

Se eligió la **regresión logística**, con un umbral de 0,50 elegido en la validación. Evaluada una sola vez con los siniestros de **2025** (conjunto de prueba):

| Métrica (clase grave o mortal) | Valor |
|---|---|
| Recall (graves detectados) | 0,634 (476 de 751) |
| Precisión (alarmas correctas) | 0,123 (1 de cada 8 alarmas) |
| F1 | 0,206 |
| Alarmas totales / falsas alarmas | 3.860 / 3.384 |

Las características más asociadas a siniestros graves fueron: víctima peatón o en moto, contraparte objeto fijo, peatón o tren, autopista y horas de madrugada.

## Conclusiones y limitaciones

Con la información disponible al momento del aviso, el modelo anticipa en parte la gravedad de un siniestro: en 2025 detectó cerca de 6 de cada 10 siniestros graves y sus alarmas acertaron alrededor de 1,7 veces más que una selección al azar. Su capacidad es limitada y genera muchas falsas alarmas, por lo que puede servir como apoyo para priorizar, no como una clasificación confiable por sí sola.

**Limitaciones:**

- Solo aplica a siniestros con el vehículo de la víctima y la contraparte informados: se excluyó el 22,3% de los registros, lo que cambia la población estudiada.
- La lluvia es un dato retrospectivo de un único punto de la Ciudad, no disponible en tiempo real al recibir el aviso.
- No es posible verificar si cada dato del archivo se registró al momento del aviso o se completó después.
- El análisis exploratorio usó todo el período, incluido 2025, que luego se usó como prueba.
- El dataset no registra factores relevantes, como la velocidad, el uso de casco o cinturón o el alcohol.
- Los resultados corresponden a CABA. Aplicarlos en otra jurisdicción requeriría datos locales y reentrenar el modelo.

**Posibles mejoras:** incorporar variables disponibles en tiempo real (por ejemplo, una estación meteorológica), sumar información de la ubicación, probar modelos que capten combinaciones de factores y reentrenar con años posteriores.

## Estructura del repositorio

```text
prediccion_gravedad_siniestros_viales_caba/
│
├── data/
│   ├── raw/          <- Datos originales, sin modificar (siniestros)
│   ├── external/     <- Datos de terceros (lluvia de Open-Meteo)
│   ├── interim/      <- Datos intermedios (no se usa en este proyecto)
│   └── processed/    <- Dataset limpio, entrada del modelado
│
├── docs/             <- Descripción del dataset e imágenes del README
├── models/           <- Modelo final entrenado (.joblib)
├── notebooks/        <- Notebooks de exploración y de modelado
├── references/       <- Material de consulta
├── reports/
│   └── figures/      <- Gráficos generados
│
├── requirements.txt  <- Librerías necesarias y sus versiones
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
4. Abrir y ejecutar completa la notebook `notebooks/2.0-jpm-modelado.ipynb`. Lee `data/processed/siniestros_limpio.csv`, entrena y evalúa los modelos, y guarda el modelo final en `models/modelo_gravedad_siniestros.joblib`. La búsqueda de hiperparámetros de KNN puede tardar alrededor de un minuto.

Las rutas a los datos son relativas a la carpeta `notebooks/`, por lo que no hace falta modificarlas si se mantiene la estructura del repositorio.

## Tecnologías utilizadas

Python | Pandas | NumPy | Matplotlib | Scikit-learn | Jupyter Notebook

---

<p align="center">
  <strong>Juan Pablo Monllor</strong><br>
  Tecnicatura Superior en Ciencia de Datos e Inteligencia Artificial<br>
  2026
</p>