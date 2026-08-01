# Caso de estudio sobre incendios forestales en Colombia

Procesamiento y clasificación geoespacial de incendios forestales en Colombia mediante datos de **NASA FIRMS** y la cartografía oficial de ecosistemas del **IDEAM**. El proyecto incluye limpieza y validación de datos, integración de información satelital, implementación de un agente de inteligencia artificial para la obtención y asociación de coordenadas, transformación de sistemas de referencia espacial, creación de geometrías de puntos y clasificación ecosistémica mediante análisis espacial.

# Organización de los datos

Todo el material necesario para ejecutar el proyecto se encuentra organizado en la siguiente carpeta de Google Drive:

https://drive.google.com/drive/folders/1IpXy65wyVVcIRdlz9IuSRqXiLV4kGPwJ?usp=sharing

En esta carpeta se encuentran las principales fuentes y rutas de información utilizadas durante el proyecto.

## 1. Inventario de casos

Contiene nuestro inventario de incendios forestales registrados en Colombia, que constituye la base principal sobre la cual se desarrolla el procesamiento.

El inventario contiene variables relacionadas con:

- Año
- Fecha
- Departamento
- Municipio
- Evento
- Comentarios
- Coordenadas
- Tipo de ecosistema

Esta base es enriquecida progresivamente mediante información proveniente de NASA FIRMS y del IDEAM.

## 2. Datos satelitales NASA FIRMS

Se cuenta con tres conjuntos principales de información provenientes de diferentes sistemas satelitales disponibles mediante NASA FIRMS:

- MODIS
- NOAA-20
- Suomi NPP

Estos archivos contienen millones de detecciones térmicas y variables relacionadas con cada detección, como:

- `latitude`
- `longitude`
- `acq_date`
- `confidence`
- `frp`

Los datos de los diferentes sistemas satelitales son consolidados para facilitar el procesamiento conjunto y la búsqueda de coincidencias con nuestro inventario de incendios.

## 3. Cartografía oficial del IDEAM

Se incorpora la cartografía oficial de ecosistemas continentales, costeros y marinos de Colombia.

La información se encuentra almacenada en una geodatabase (`.gdb`) y contiene aproximadamente **460.350 unidades espaciales**, representadas principalmente mediante polígonos.

Cada polígono contiene atributos ambientales y ecosistémicos que posteriormente son asociados a los incendios mediante análisis espacial.

## 4. Archivos generados durante el procesamiento

A medida que se ejecuta el proyecto se generan diferentes archivos derivados, entre ellos resultados de procesos de integración, consolidación y análisis espacial.

Algunos ejemplos son:

- `NASA FIRMS 2010-2024`
- Resultados de clasificación mediante IDEAM
- Bases procesadas del inventario
- Archivos derivados de los procesos de `concat` y `Spatial Join`

Estos archivos son productos intermedios o finales generados durante el procesamiento.

# Georreferenciación mediante un agente de IA

Una de las primeras etapas consistió en obtener coordenadas geográficas para los registros del inventario.

Para este proceso se implementó un **agente de inteligencia artificial especializado en el cruce y asociación de información**, utilizando como fuente las detecciones térmicas de NASA FIRMS.

El agente permitió relacionar los registros del inventario con las detecciones satelitales teniendo en cuenta diferentes criterios:

- Fecha del incendio.
- Departamento.
- Municipio.
- Ubicación espacial.
- Intensidad térmica mediante `FRP (Fire Radiative Power)`.

El objetivo fue identificar la detección satelital más compatible con cada caso y utilizar su latitud y longitud para generar la columna `COORDENADAS`.

Las coordenadas fueron manejadas inicialmente en grados decimales bajo el sistema de referencia **WGS84 (EPSG:4326)**.

Por ejemplo, una coordenada puede representarse como `6.5800, -70.9100`.

Estas coordenadas posteriormente permitieron convertir cada registro de incendio en una geometría espacial tipo `Point`.

# Consolidación de NASA FIRMS

Los registros provenientes de MODIS, NOAA-20 y Suomi NPP fueron procesados y consolidados para disponer de una base conjunta de detecciones térmicas.

El objetivo de esta consolidación fue facilitar el cruce entre las detecciones satelitales y el inventario de casos, manteniendo las variables necesarias para el análisis.

Entre las variables principales utilizadas se encuentran `latitude`, `longitude`, `acq_date`, `confidence` y `frp`.

# Flujo de trabajo: procesamiento y enriquecimiento geoespacial

## 1. Obtención y consolidación de datos

Recopilación y organización de registros históricos de incendios forestales en Colombia, junto con las detecciones térmicas provenientes de NASA FIRMS y la cartografía oficial del IDEAM.

## 2. Exploración inicial de los datos

Revisión de las variables disponibles, estructura de las bases de datos, cantidad de registros y análisis de los campos relacionados con la ubicación y clasificación inicial de los incendios.

## 3. Análisis exploratorio y visualización

Generación de gráficos, estadísticas y análisis descriptivos para identificar patrones temporales, geográficos y ambientales relacionados con los incendios.

## 4. Identificación de datos faltantes o poco confiables

Detección de registros que presentaban información incompleta o clasificaciones como `"Otros / No determinado"`, las cuales posteriormente podían ser mejoradas mediante información geoespacial.

## 5. Georreferenciación de los incendios

Utilización de las coordenadas obtenidas mediante NASA FIRMS para transformar los registros de incendios en geometrías espaciales de tipo `Point`.

## 6. Validación de coordenadas

Extracción de latitud y longitud desde el campo `COORDENADAS` y comprobación de sus rangos geográficos para verificar la consistencia espacial de los registros.

## 7. Integración de información geográfica oficial

Descarga e incorporación de la cartografía oficial de ecosistemas continentales, costeros y marinos del IDEAM.

## 8. Procesamiento de la cartografía IDEAM

Identificación y carga de la geodatabase (`.gdb`) y de la capa de ecosistemas correspondiente.

La capa utilizada contiene aproximadamente **460.350 unidades espaciales**.

## 9. Validación de sistemas de referencia espacial

Se revisaron los sistemas de coordenadas utilizados por ambas fuentes.

Los puntos de los incendios fueron transformados al CRS utilizado por la cartografía IDEAM:

**EPSG:4686 — MAGNA-SIRGAS**

Este paso garantiza que los puntos y los polígonos puedan compararse correctamente en el espacio.

## 10. Validación espacial

Se verificó que los puntos de los incendios estuvieran dentro de la extensión geográfica de la cartografía IDEAM.

También se comprobó que cada incendio tuviera una única correspondencia espacial con la capa de ecosistemas.

## 11. Spatial Join

El Spatial Join permite relacionar información utilizando la **posición geográfica** de los elementos.

### Explicación sencilla

En nuestro caso tenemos los incendios representados como **puntos** y los ecosistemas del IDEAM representados como **polígonos**.

El Spatial Join responde a una pregunta sencilla:

**¿Dentro de qué polígono del IDEAM se encuentra cada incendio?**

El proceso consiste en tomar las coordenadas del incendio, convertirlas en un punto, ubicar ese punto sobre la cartografía del IDEAM y determinar dentro de qué polígono se encuentra.

Una vez identificado el polígono correspondiente, el incendio recibe la información ambiental y ecosistémica asociada a dicho polígono.

Por ejemplo, si un incendio se encuentra dentro de un polígono clasificado por el IDEAM como `Bosque Basal Humedo`, el registro del incendio puede recibir esa clasificación.

### Explicación técnica

Los registros del inventario fueron transformados en un `GeoDataFrame`, utilizando sus coordenadas para generar geometrías de tipo `Point`.

Posteriormente, se homologó el sistema de referencia espacial de los puntos con el CRS de la cartografía IDEAM.

La integración espacial se realizó mediante GeoPandas utilizando la función `sjoin` y la relación espacial `within`.

En términos técnicos, `predicate="within"` establece que la geometría del punto de incendio debe encontrarse dentro de la geometría del polígono correspondiente de la capa IDEAM.

Por lo tanto, el proceso no realiza una coincidencia textual entre las bases de datos. La asociación se realiza mediante una **relación espacial entre geometrías**.

El resultado consiste en conservar la información original de cada incendio y agregar los atributos ambientales y ecosistémicos correspondientes al polígono IDEAM donde se encuentra ubicado.

## 12. Clasificación ecosistémica

A partir del Spatial Join se obtuvieron variables ambientales y ecológicas para cada incendio.

Entre las variables utilizadas se encuentran:

- `gran_bioma`
- `bioma_preliminar`
- `bioma_IAvH`
- `ecos_sintesis`
- `ecos_general`
- `clima`
- `paisaje`
- `relieve`
- `suelos`
- `provincia`
- `eco_region`
- `eco_zona`

Entre las clasificaciones principales utilizadas para el análisis se encuentran `gran_bioma` y `ecos_general`.

El resultado obtenido presenta:

- **5 categorías de `gran_bioma`**
- **57 categorías de `ecos_general`**

## 13. Validación de resultados

Se realizaron comprobaciones para verificar la correcta integración espacial.

Resultados principales:

- **14.985 casos originales**
- **14.985 casos después del Spatial Join**
- **0 casos sin ecosistema asignado**
- **14.985 casos con ecosistema asignado**
- **14.985 incendios con una sola coincidencia espacial**
- **0 incendios con múltiples coincidencias**

Esto permitió comprobar que todos los registros del inventario fueron asociados espacialmente con la cartografía IDEAM.

## 14. Reclasificación de registros

La información obtenida mediante el IDEAM permitió mejorar la clasificación inicial de los registros que aparecían como `"Otros / No determinado"`.

En lugar de depender exclusivamente de la clasificación inicial del inventario, se utilizó la ubicación geográfica de cada incendio y la cartografía oficial para obtener una clasificación ecosistémica basada en información espacial.

## 15. Generación de la base final

Una vez completados los procesos de georreferenciación, validación y clasificación espacial, se generan las bases procesadas que contienen el inventario enriquecido con información geográfica y ecosistémica.

## 16. Preparación para modelado

La base resultante proporciona una estructura más robusta para continuar con el análisis de los factores asociados a la ocurrencia de incendios forestales.

La información geográfica y ecosistémica podrá utilizarse posteriormente junto con variables temporales, ambientales y espaciales para desarrollar modelos de análisis y predicción.

# Flujo general del proyecto

El proyecto comienza con el **inventario de casos de incendios forestales**. Posteriormente, un **agente de IA** participa en la asociación de los registros con información de **NASA FIRMS**, utilizando los datos provenientes de **MODIS, NOAA-20 y Suomi NPP**.

A partir de este proceso se obtienen las **coordenadas de los incendios**, que permiten realizar la **georreferenciación** de cada registro.

Una vez georreferenciados, los incendios son relacionados con la **cartografía oficial del IDEAM**, compuesta por aproximadamente **460.350 unidades espaciales**.

Mediante un **Spatial Join** se establece la relación entre los puntos de los incendios y los polígonos de ecosistemas.

Finalmente, se obtiene la **clasificación ecosistémica**, con **5 categorías de `gran_bioma` y 57 categorías de `ecos_general`**, generando una base de datos enriquecida que podrá utilizarse para el **análisis y modelado predictivo de incendios forestales**.

# Objetivo final

El objetivo del proyecto es construir una base de datos geoespacial de incendios forestales en Colombia que integre **registros históricos, detecciones satelitales y clasificación ecosistémica oficial**, permitiendo pasar de un inventario básico de eventos a una base estructurada y espacialmente enriquecida.

Esta información servirá como fundamento para las siguientes etapas del proyecto, orientadas al **análisis de patrones de ocurrencia y desarrollo de modelos de predicción de incendios forestales**.
