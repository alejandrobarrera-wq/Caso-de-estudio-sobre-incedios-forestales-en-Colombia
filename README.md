# Caso de estudio sobre incendios forestales en Colombia

Procesamiento y clasificación geoespacial de incendios forestales en Colombia mediante datos de **NASA FIRMS** y la cartografía oficial de ecosistemas del **IDEAM**. El proyecto incluye limpieza y validación de datos, integración de información satelital, implementación de un agente de inteligencia artificial para la obtención y asociación de coordenadas, transformación de sistemas de referencia espacial, creación de geometrías de puntos y clasificación ecosistémica mediante análisis espacial.

# Requerimientos
pandas: limpieza y manejo de datos.
geopandas: procesamiento y análisis geoespacial.
shapely: creación y manejo de geometrías.
fiona: lectura de la geodatabase del IDEAM.
matplotlib: generación de gráficos y visualizaciones.
Python 3: entorno principal de ejecución.

# Objetivos del proyecto
Desarrollar una base de datos geoespacial de incendios forestales en Colombia que integre registros históricos, datos satelitales y características ambientales de los ecosistemas, para entender mejor dónde y bajo qué condiciones ocurren los incendios.

# Organización de los datos

Todo el material necesario para ejecutar el proyecto se encuentra organizado en la siguiente carpeta de Google Drive:

https://drive.google.com/drive/folders/1IpXy65wyVVcIRdlz9IuSRqXiLV4kGPwJ?usp=sharing

En esta carpeta se encuentran las principales fuentes y rutas de información utilizadas durante el proyecto.

## 1. Inventario de casos (UNGRD, Unidad Nacional para la Gestión del Riesgo de Desastres)

Contiene nuestro inventario de incendios forestales registrados en Colombia por la UNGRD. Aproximadamente quince mil incendios, que constituye la base principal sobre la cual se desarrolla el procesamiento.

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
( PRE PROCESAMIENTO al descargar los datos y explicar los duplicados una vez jecho el dataset)
## 4. Archivos generados durante el procesamiento

A medida que se ejecuta el proyecto se generan diferentes archivos derivados, entre ellos resultados de procesos de integración, consolidación y análisis espacial.

Algunos ejemplos son:

- `NASA FIRMS 2010-2024`
- Resultados de clasificación mediante IDEAM (colocamso arriba que logramos con este codigo`

Estos archivos son productos intermedios o finales generados durante el procesamiento.

# Consolidación de NASA FIRMS (lo pongo en pre procesamiento)

Los registros provenientes de MODIS, NOAA-20 y Suomi NPP fueron procesados y consolidados para disponer de una base conjunta de detecciones térmicas.

El objetivo de esta consolidación fue facilitar el cruce entre las detecciones satelitales y el inventario de casos, manteniendo las variables necesarias para el análisis.

Entre las variables principales utilizadas se encuentran `latitude`, `longitude`, `acq_date`, `confidence` y `frp`.

# Flujo de trabajo: procesamiento y enriquecimiento geoespacial ( hacerlo en el diagrama de flujo)

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

## 15. Generación de la base final

Una vez completados los procesos de georreferenciación, validación y clasificación espacial, se generan las bases procesadas que contienen el inventario enriquecido con información geográfica y ecosistémica.

