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
Desarrollar una base de datos geoespacial de incendios forestales en Colombia que integre registros históricos, datos satelitales y características ambientales de los ecosistemas, para entender mejor dónde y bajo qué condiciones ocurren los incendios. buscamos lograr relacionar cada incendio con su ecosistema, utilizando la cartografía oficial del IDEAM e integrar información satelital para enriquecer nuestro inventario de casos.

# Organización de los datos

Todo el material necesario para ejecutar el proyecto se encuentra organizado en la siguiente carpeta de Google Drive:

https://drive.google.com/drive/folders/1IpXy65wyVVcIRdlz9IuSRqXiLV4kGPwJ?usp=sharing

En esta carpeta se encuentran las principales fuentes y rutas de información utilizadas durante el proyecto.

## 1. Inventario de casos (UNGRD, Unidad Nacional para la Gestión del Riesgo de Desastres)

Contiene nuestro inventario de incendios forestales registrados en Colombia por la UNGRD. Aproximadamente quince mil incendios, que constituye la base principal sobre la cual vamos a desarrollar el procesamiento.

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

## 2. Pre procesamiento de todo lo de NASA FIRMS

Los registros provenientes NASA FIRMS que contienen informacion de MODIS, NOAA-20 y Suomi NPP fueron procesados y cargados para  mas adelante hacer un dataset con todos los registros obtenidos de los tres sistemas satelitales.

El objetivo de esta conglomeracion fue facilitar el cruce entre las detecciones satelitales y el inventario de casos, manteniendo las variables necesarias para el análisis.

Estos archivos contienen millones de datos sobre las detecciones térmicas y variables relacionadas con cada detección, como:

- `latitude`
- `longitude`
- `acq_date`
- `confidence`
- `frp` (Potencia Radiativa del Fuego). Mide la tasa de energía radiada por un incendio en un momento dado

Los datos de los diferentes sistemas satelitales son consolidados para facilitar el procesamiento conjunto y la búsqueda de coincidencias con nuestro inventario de incendios.

## Pre procesamiento de cartografía oficial del IDEAM

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

# Flujo de trabajo: procesamiento y enriquecimiento geoespacial ( hacerlo en el diagrama de flujo)

DATOS DE ENTRADA
│
├── Inventario de incendios
├── NASA FIRMS
└── Cartografía IDEAM
        │
        ▼
┌─────────────────────────────┐
│ 1. Limpieza y exploración   │
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│ 2. Validación de datos      │
│    Fechas y coordenadas     │
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│ 3. Georreferenciación       │
│    Coordenadas → Point      │
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│ 4. Homologación CRS         │
│    EPSG:4326 → EPSG:4686   │
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│ 5. Validación espacial      │
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│ 6. Spatial Join              │
│    Point + Polígono IDEAM   │
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│ 7. Clasificación ecológica  │
│    Gran Bioma + Ecosistema  │
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│ 8. Validación y             │
│    reclasificación          │
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│ DATASET ENRIQUECIDO         │
│ Incendio + ubicación +      │
│ ecosistema + variables     │
└─────────────────────────────┘
        │
        ▼
┌─────────────────────────────┐
│ ANÁLISIS Y MACHINE LEARNING │
└─────────────────────────────┘
