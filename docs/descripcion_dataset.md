# Descripción del dataset

**Proyecto:** Predicción de la gravedad de siniestros viales en CABA

**Autor:** Juan Pablo Monllor - Aprendizaje Automático 2026

## 1. Descripción general

El dataset principal registra los siniestros viales con víctimas ocurridos en la Ciudad Autónoma de Buenos Aires entre el 1 de enero de 2019 y el 31 de diciembre de 2025. Cada fila corresponde a un siniestro.

| Característica | Valor |
|---|---|
| Registros (instancias) | 65.818 |
| Columnas (características) | 21 |
| Período | 2019 a 2025 |
| Formato | CSV separado por punto y coma (`;`) |
| Unidad de análisis | Un siniestro vial con al menos una víctima |

**Alcance:** el dataset incluye solo siniestros con víctimas (heridos leves, graves o fallecidos). Los siniestros sin víctimas, que solo producen daños materiales, no forman parte del registro, por lo que el modelo solo es aplicable a siniestros donde hay al menos una persona herida.

A partir de la columna `gravedad_siniestro` se construye la variable objetivo del proyecto, `grave_o_mortal`: vale 0 si el siniestro fue leve y 1 si fue grave o mortal.

## 2. Origen de los datos

El proyecto combina tres fuentes de dominio público.

### 2.1 Siniestros viales (dataset principal)

| Dato | Detalle |
|---|---|
| Fuente | [Buenos Aires Data - Víctimas de siniestros viales](https://data.buenosaires.gob.ar/dataset/victimas-siniestros-viales) |
| Responsable | Observatorio de Movilidad y Seguridad Vial, Gobierno de la Ciudad de Buenos Aires |
| Archivo utilizado | `siniestros_viales_hechos.csv` (un registro por siniestro) |
| Fecha de adquisición | 11/10/2026 |
| Ubicación en el repositorio | `data/raw/siniestros_viales_hechos.csv` (sin modificar) |

### 2.2 Lluvia

| Dato | Detalle |
|---|---|
| Fuente | [Open-Meteo](https://open-meteo.com), API de datos meteorológicos históricos |
| Variable | Precipitación por hora, en milímetros |
| Período | 01/01/2019 a 31/12/2025, en horario de Buenos Aires |
| Ubicación | Un único punto para toda la Ciudad (latitud -34,61, longitud -58,44) |
| Fecha de adquisición | 30/10/2026 |
| Ubicación en el repositorio | `data/external/lluvia_caba_open_meteo_2019_2025.csv` (sin modificar) |

Los datos se descargaron con la siguiente consulta a la API:

```text
https://archive-api.open-meteo.com/v1/archive?latitude=-34.61&longitude=-58.44&start_date=2019-01-01&end_date=2025-12-31&hourly=precipitation&timezone=America/Argentina/Buenos_Aires&format=csv
```

### 2.3 Feriados

Los feriados nacionales de Argentina se obtienen con la librería de Python [holidays](https://pypi.org/project/holidays/) (versión 0.107), a partir de la fecha de cada siniestro. No se descarga ningún archivo.

## 3. Diccionario de datos

### 3.1 Columnas originales

La columna "¿Se conoce al aviso?" indica si el dato está disponible en el momento en que se informa el siniestro. Solo esas variables pueden usarse para predecir sin incurrir en fuga de información.

| Columna | Tipo | Descripción | ¿Se conoce al aviso? | Uso en el proyecto |
|---|---|---|---|---|
| `id_siniestro` | Texto | Identificador único del siniestro (por ejemplo, `LC-2019-0000053`) | Sí | Excluida: identificador |
| `numero_total_de_victimas` | Entero | Cantidad total de víctimas | No | Excluida: fuga de información |
| `numero_victimas_leve_siniestro` | Entero | Cantidad de víctimas leves | No | Excluida: fuga de información |
| `numero_victimas_grave_siniestro` | Entero | Cantidad de víctimas graves | No | Excluida: fuga de información |
| `numero_victimas_mortal_siniestro` | Entero | Cantidad de víctimas fallecidas | No | Excluida: fuga de información |
| `fecha_siniestro` | Fecha | Fecha del siniestro (año-mes-día) | Sí | Se conserva para la partición temporal y para calcular tipo de día y estación |
| `anio_siniestro` | Entero | Año del siniestro | Sí | Excluida: repite la fecha |
| `mes_siniestro` | Entero | Mes del siniestro | Sí | Excluida: repite la fecha |
| `dia_siniestro` | Entero | Día del mes | Sí | Excluida: repite la fecha |
| `hora_siniestro` | Texto | Hora exacta (hh:mm:ss) | Sí | Excluida: se usa la hora entera de `rango_horario` |
| `rango_horario` | Texto | Hora del siniestro, de 0 a 23, o "SD" | Sí | Predictora (como `hora`) |
| `direccion_normalizada_siniestro` | Texto | Dirección o intersección del siniestro | Sí | Excluida: simplificación del alcance |
| `comuna_siniestro` | Texto | Comuna donde ocurrió (1 a 15) | Sí | Excluida: aporta poca información y sus faltantes dependen del registro |
| `tipo_de_via_siniestro` | Texto | Tipo de vía: AVENIDA, CALLE, AV. GRAL. PAZ, AUTOPISTA o sin dato | Sí | Predictora |
| `geocodificacion_plana` | Texto | Coordenadas planas del lugar | Sí | Excluida: simplificación del alcance |
| `longitud_siniestro` | Decimal | Longitud geográfica | Sí | Excluida: simplificación del alcance |
| `latitud_siniestro` | Decimal | Latitud geográfica | Sí | Excluida: simplificación del alcance |
| `participantes_siniestro` | Texto | Combinación de vehículo de la víctima y contraparte (por ejemplo, `MOTO-AUTO`) | Sí | Excluida: repite dos predictoras |
| `modo_desplazamiento_victima` | Texto | Cómo se desplazaba la víctima: MOTO, PEATON, BICICLETA, AUTO, etc. | Sí | Predictora |
| `contraparte_siniestro` | Texto | Con qué o con quién se produjo el siniestro: AUTO, OBJETO FIJO, CAMION, TREN, etc. | Sí | Predictora |
| `gravedad_siniestro` | Texto | Gravedad: LEVE, GRAVE o MORTAL | No | Origen de la variable objetivo |

### 3.2 Variables nuevas

| Columna | Tipo | Descripción | Origen |
|---|---|---|---|
| `lluvia_mm` | Decimal | Precipitación en la hora del siniestro, en milímetros | Open-Meteo, unida por fecha y hora |
| `tipo_dia` | Texto | HABIL, FIN DE SEMANA o FERIADO | Fecha del siniestro y librería `holidays` |
| `estacion` | Texto | VERANO, OTOÑO, INVIERNO o PRIMAVERA (hemisferio sur, por mes) | Fecha del siniestro |
| `grave_o_mortal` | Entero | Variable objetivo: 0 = leve, 1 = grave o mortal | `gravedad_siniestro` |

### 3.3 Referencia: comunas y barrios

| Comuna | Barrios |
|---|---|
| 1 | Retiro, San Nicolás, Puerto Madero, San Telmo, Monserrat, Constitución |
| 2 | Recoleta |
| 3 | Balvanera, San Cristóbal |
| 4 | La Boca, Barracas, Parque Patricios, Nueva Pompeya |
| 5 | Almagro, Boedo |
| 6 | Caballito |
| 7 | Flores, Parque Chacabuco |
| 8 | Villa Soldati, Villa Riachuelo, Villa Lugano |
| 9 | Liniers, Mataderos, Parque Avellaneda |
| 10 | Villa Real, Monte Castro, Versalles, Floresta, Vélez Sarsfield, Villa Luro |
| 11 | Villa General Mitre, Villa Devoto, Villa del Parque, Villa Santa Rita |
| 12 | Coghlan, Saavedra, Villa Urquiza, Villa Pueyrredón |
| 13 | Núñez, Belgrano, Colegiales |
| 14 | Palermo |
| 15 | Chacarita, Villa Crespo, La Paternal, Villa Ortúzar, Agronomía, Parque Chas |

## 4. Preprocesamiento realizado

El detalle completo, con el código y los resultados de cada paso, está en la notebook [1.0-jpm-eda-preprocesamiento.ipynb](../notebooks/1.0-jpm-eda-preprocesamiento.ipynb). A continuación se resumen las decisiones tomadas.

### 4.1 Unificación de formatos

- `contraparte_siniestro`: se pasaron todos los valores a mayúsculas, ya que una misma categoría aparecía escrita de distintas formas.
- `comuna_siniestro`: se unificaron los tres formatos en que aparecía la comuna (por ejemplo, "Comuna 8" y "8").
- `contraparte_siniestro`: `CAMIONETA` se unificó con `UTILITARIO` (74 registros).

### 4.2 Registros sin dato

El análisis mostró que la falta de dato no se distribuye igual entre siniestros leves y graves. En el vehículo de la víctima y la contraparte, los registros sin dato son casi todos leves (0,2% y 0,9% de graves, frente a 6,6% y 6,8% cuando el dato está). Una hipótesis es que los siniestros leves se registran con menos detalle. Para que el modelo no aprenda a usar la ausencia del dato como pista, se tomaron las siguientes decisiones:

| Decisión | Registros afectados |
|---|---|
| Eliminar registros con error de planilla (`#¡REF!`) | 1 |
| Eliminar registros sin dato en el vehículo de la víctima o en la contraparte | 14.646 |
| Eliminar registros sin hora | 27 |
| Agrupar los faltantes del tipo de vía en la categoría "SIN DATO" | 10.812 (se conservan) |
| No usar la comuna como predictora | Toda la columna |

En total se eliminaron 14.674 registros (22,3%). Entre ellos había solo 157 siniestros graves o mortales.

### 4.3 Variables nuevas

Se agregaron la lluvia en la hora del siniestro, el tipo de día y la estación del año (ver sección 3.2).

### 4.4 Codificación

Las variables categóricas se transformaron con one-hot encoding: cada categoría pasa a ser una columna con valores 0 y 1. El escalado de las variables numéricas se hará en la Entrega 3, ajustándolo solo con los datos de entrenamiento.

## 5. Datasets resultantes

| Archivo | Registros | Columnas | Descripción |
|---|---|---|---|
| `data/interim/siniestros_limpio.csv` | 51.144 | 9 | Dataset limpio, con las variables categóricas como texto. Se usa para la exploración y la presentación de resultados |
| `data/processed/siniestros_modelo.csv` | 51.144 | 42 | Dataset codificado con one-hot encoding, listo para el entrenamiento de los modelos |

En ambos archivos, el 6,8% de los registros corresponde a siniestros graves o mortales (3.487 de 51.144), y el 93,2% a siniestros leves.