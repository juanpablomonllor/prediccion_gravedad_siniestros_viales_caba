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
| Fuente | [Open-Meteo](https://open-meteo.com), API de datos meteorológicos históricos ([documentación](https://open-meteo.com/en/docs/historical-weather-api)) |
| Variable | Precipitación horaria en milímetros: suma de la lluvia de la hora anterior (el valor de las 18:00 corresponde a lo caído entre las 17:00 y las 18:00) |
| Período | 01/01/2019 a 31/12/2025, en horario de Buenos Aires |
| Ubicación | Un único punto para toda la Ciudad (latitud -34,61, longitud -58,44) |
| Fecha de adquisición | 30/10/2026 |
| Ubicación en el repositorio | `data/external/lluvia_caba_open_meteo_2019_2025.csv` (sin modificar) |

Los datos se descargaron con la siguiente consulta a la API:

```text
https://archive-api.open-meteo.com/v1/archive?latitude=-34.61&longitude=-58.44&start_date=2019-01-01&end_date=2025-12-31&hourly=precipitation&timezone=America/Argentina/Buenos_Aires&format=csv
```

**Disponibilidad:** estos datos provienen de modelos meteorológicos y reanálisis históricos, que se publican con días de demora. No estarían disponibles en tiempo real al recibir un aviso, por lo que la lluvia se usa como una aproximación retrospectiva de las condiciones previas al siniestro.

### 2.3 Feriados

Los feriados nacionales de Argentina se obtienen con la librería de Python [holidays](https://pypi.org/project/holidays/) (versión 0.107), a partir de la fecha de cada siniestro. No se descarga ningún archivo.

## 3. Diccionario de datos

### 3.1 Columnas originales

La columna "¿Se conoce al aviso?" indica si el dato puede conocerse en el momento en que se informa el siniestro. Que pueda conocerse no garantiza que el valor del archivo se haya registrado en ese momento: el dataset es un registro histórico consolidado y algunos datos podrían haberse completado o corregido después, algo que no es posible verificar con la información publicada.

| Columna | Tipo y unidad | Descripción | ¿Se conoce al aviso? | Uso en el proyecto |
|---|---|---|---|---|
| `id_siniestro` | Texto | Identificador único del siniestro (por ejemplo, `LC-2019-0000053`) | Sí | Excluida: identificador |
| `numero_total_de_victimas` | Entero (personas) | Cantidad total de víctimas | No | Excluida: fuga de información |
| `numero_victimas_leve_siniestro` | Entero (personas) | Cantidad de víctimas leves | No | Excluida: fuga de información |
| `numero_victimas_grave_siniestro` | Entero (personas) | Cantidad de víctimas graves | No | Excluida: fuga de información |
| `numero_victimas_mortal_siniestro` | Entero (personas) | Cantidad de víctimas fallecidas | No | Excluida: fuga de información |
| `fecha_siniestro` | Fecha (año-mes-día) | Fecha del siniestro | Sí | Se conserva para la partición temporal y para calcular tipo de día y estación |
| `anio_siniestro` | Entero (2019 a 2025) | Año del siniestro | Sí | Excluida: repite la fecha |
| `mes_siniestro` | Entero (1 a 12) | Mes del siniestro | Sí | Excluida: repite la fecha |
| `dia_siniestro` | Entero (1 a 31) | Día del mes | Sí | Excluida: repite la fecha |
| `hora_siniestro` | Texto (hh:mm:ss) | Hora exacta | Sí | Excluida: se usa la hora entera de `rango_horario` |
| `rango_horario` | Texto (0 a 23, o "SD") | Hora entera del siniestro: 18 significa entre las 18:00 y las 18:59 | Sí | Predictora (como `hora`, entero de 0 a 23) |
| `direccion_normalizada_siniestro` | Texto | Dirección o intersección del siniestro | Sí | Excluida: simplificación del alcance |
| `comuna_siniestro` | Texto (1 a 15) | Comuna donde ocurrió (ver sección 3.4) | Sí | Excluida: aporta poca información y sus faltantes podrían depender del registro |
| `tipo_de_via_siniestro` | Texto (categorías) | Tipo de vía: AVENIDA, CALLE, AV. GRAL. PAZ, AUTOPISTA o sin dato | Sí | Predictora |
| `geocodificacion_plana` | Texto | Coordenadas planas del lugar | Sí | Excluida: simplificación del alcance |
| `longitud_siniestro` | Decimal (grados) | Longitud geográfica | Sí | Excluida: simplificación del alcance |
| `latitud_siniestro` | Decimal (grados) | Latitud geográfica | Sí | Excluida: simplificación del alcance |
| `participantes_siniestro` | Texto | Combinación de vehículo de la víctima y contraparte (por ejemplo, `MOTO-AUTO`) | Sí | Excluida: repite dos predictoras |
| `modo_desplazamiento_victima` | Texto (categorías) | Cómo se desplazaba la víctima: MOTO, PEATON, BICICLETA, AUTO, etc. | Sí | Predictora |
| `contraparte_siniestro` | Texto (categorías) | Con qué o con quién se produjo el siniestro: AUTO, OBJETO FIJO, CAMION, TREN, etc. | Sí | Predictora |
| `gravedad_siniestro` | Texto (LEVE, GRAVE, MORTAL) | Gravedad del siniestro (ver sección 3.3) | No | Origen de la variable objetivo |

### 3.2 Variables nuevas

| Columna | Tipo y unidad | Descripción | Origen | ¿Se conoce al aviso? |
|---|---|---|---|---|
| `hora` | Entero (0 a 23) | Hora entera del siniestro, tomada de `rango_horario` | Dataset principal | Sí |
| `lluvia_mm` | Decimal (milímetros) | Lluvia caída en la hora previa al siniestro | Open-Meteo, unida por fecha y hora | No en tiempo real: aproximación retrospectiva |
| `tipo_dia` | Texto (HABIL, FIN DE SEMANA, FERIADO) | Tipo de día del siniestro | Fecha del siniestro y librería `holidays` | Sí |
| `estacion` | Texto (VERANO, OTOÑO, INVIERNO, PRIMAVERA) | Estación del año según el mes (hemisferio sur) | Fecha del siniestro | Sí |
| `grave_o_mortal` | Entero (0 o 1) | Variable objetivo: 0 = leve, 1 = grave o mortal | `gravedad_siniestro` | No (es lo que se predice) |

### 3.3 Glosario

| Término | Significado |
|---|---|
| Siniestro vial | Hecho de tránsito ocurrido en la vía pública. Este dataset incluye solo los que dejaron al menos una víctima |
| Víctima | Persona herida o fallecida en el siniestro |
| Leve / Grave / Mortal | Gravedad del siniestro según su víctima más afectada. Es grave cuando alguna víctima estuvo internada al menos 24 horas, y mortal cuando alguna víctima falleció. El detalle está en las notas metodológicas del dataset |
| Modo de desplazamiento de la víctima | Cómo se trasladaba la persona herida: a pie (peatón), en moto, en bicicleta, en auto, etc. |
| Contraparte | El otro participante del siniestro: otro vehículo, un peatón o un objeto fijo |
| Objeto fijo | Elemento inmóvil contra el que se produjo el impacto, como un poste, un árbol o una columna |
| "SD" | "Sin dato": el dataset usa este texto cuando no se registró la información |
| Comuna | Cada una de las 15 unidades en que se divide la Ciudad, que agrupan a sus 48 barrios |
| Av. Gral. Paz | Avenida General Paz, la vía rápida que marca el límite entre la Ciudad y la Provincia de Buenos Aires |

### 3.4 Referencia: comunas y barrios

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

El análisis mostró que la falta de dato no se distribuye igual entre siniestros leves y graves. En el vehículo de la víctima y la contraparte, los registros sin dato son casi todos leves (0,2% y 0,9% de graves, frente a 6,6% y 6,8% cuando el dato está). Esta asociación no demuestra por sí sola que haya fuga de información: para saberlo habría que conocer cuándo y cómo se completa cada registro, y esa información no está disponible. Se tomaron las siguientes decisiones:

| Decisión | Registros afectados |
|---|---|
| Eliminar registros con error de planilla (`#¡REF!`) | 1 |
| Eliminar registros sin dato en el vehículo de la víctima o en la contraparte | 14.646 |
| Eliminar registros sin hora | 27 |
| Agrupar los faltantes del tipo de vía en la categoría "SIN DATO" | 10.812 (se conservan) |
| No usar la comuna como predictora | Toda la columna |

En total se eliminaron 14.674 registros (22,3%), entre ellos 157 siniestros graves o mortales. El porcentaje de graves pasa de 5,5% a 6,8%, es decir que el dataset limpio representa una población distinta de la original.

**Alcance del modelo:** el modelo se entrena y se evalúa solo con siniestros que tienen informados el vehículo de la víctima y la contraparte. Ante un aviso sin esos datos no puede aplicarse: habría que completar la información o tratar el caso por fuera del modelo.

### 4.3 Variables nuevas

Se agregaron la lluvia en la hora previa al siniestro, el tipo de día y la estación del año (ver sección 3.2).

### 4.4 Codificación y escalado

En esta etapa no se codifican ni se escalan las variables. El dataset limpio conserva las categorías como texto, y la codificación y el escalado se hacen en la notebook de modelado, después de separar los datos por fecha y ajustándolos solo con los datos de entrenamiento.

### 4.5 Período usado en el análisis exploratorio

El análisis exploratorio y las decisiones de preprocesamiento se tomaron con el período completo (2019 a 2025), que incluye los años que después se usan como conjunto de prueba. Por eso el conjunto de prueba no es totalmente independiente de estas decisiones, y esto se reconoce como una limitación. En el modelado, el período de prueba se reserva antes de cualquier otra decisión.

## 5. Dataset resultante

| Archivo | Registros | Columnas | Descripción |
|---|---|---|---|
| `data/processed/siniestros_limpio.csv` | 51.144 | 9 | Dataset limpio, con las variables categóricas como texto. Es la entrada de la notebook de modelado |

Columnas: `fecha_siniestro`, `modo_desplazamiento_victima`, `contraparte_siniestro`, `tipo_de_via_siniestro`, `hora`, `lluvia_mm`, `tipo_dia`, `estacion` y `grave_o_mortal`. La fecha no se usa como predictora: se conserva para separar los datos por período.

El 6,8% de los registros corresponde a siniestros graves o mortales (3.487 de 51.144), y el 93,2% a siniestros leves.