# Caso-de-estudio-sobre-incedios-forestales-en-Colombia
Procesamiento y clasificación geoespacial de incendios forestales en Colombia mediante datos de NASA FIRMS y la cartografía oficial de ecosistemas del IDEAM. Incluye limpieza y validación de coordenadas, transformación de sistemas de referencia espacial, creación de geometrías de puntos.

Ahora, necesitamos organizar y tener en cuenta todos los documentos y bases de datos que vamos a necesitar organizar para ejecutar todo el documento. Todo lo que necesitamos esta en esta carpeta y ya todas las rutas estan para que funcione solo en ese drive, ahi estan los siguientes archivos:

Nuestro inventario de casos sobre incendios forestales registrados, aquello con el que vamos a trabajar y llenar las columnas de COORDENADAS Y ECOSISTEMA (14.895 de datos)

Tres carpetas con la informacion de tres sistemas satelitales llamados Modis, NOA20 y Suomi que son los archivos principales descargados desde NASA FIRMS para corroborar que los incendios de nuestra base de datos (inventario de casos) si esten registrados en la base datos la NASA. (2.2 millones de datos)

Una base de datos del IDEAM que contiene un mapeo de todo Colombia y se va a tratar con temas como puntos (sacados de las coordenadas proveidas por el dataset de NASA FIRMS), Capas, entre otros atributos sobre las mismas.

Por supuesto a medida que corre el codigo se descargan algunos archivos como "resultados incendio IDEAM" o "NASA FIRMS 2010-2024" extraidos gracias a Sjoin y concat (que se uso para juntar toda la info de los tres sistemas satelitales en un solo dataset sin mezclar los satelites entre si, el cual hablablaremos mas adelante


##Flujo de trabajo procesamiento y enriquecimiento geoespacial
Obtención y consolidación de datos: recopilación y organización de registros históricos de incendios forestales en Colombia.

Exploración inicial de los datos: revisión de las variables disponibles, estructura de la base, cantidad de registros y análisis de los campos relacionados con la ubicación y el tipo de ecosistema.

Análisis exploratorio y visualización: generación de gráficos y estadísticas para identificar patrones temporales, geográficos y ambientales de los incendios.

Identificación de datos faltantes o poco confiables: detección de registros clasificados como "Otros / No determinado", que representaban una proporción importante de la base.

Validación de coordenadas: extracción de latitud y longitud desde el campo de coordenadas y comprobación de sus rangos geográficos para verificar la consistencia espacial de los registros.

Georreferenciación de los incendios: transformación de cada registro de incendio en una geometría espacial de tipo Point, utilizando sus coordenadas geográficas.

Integración de información geográfica oficial: descarga e incorporación de la cartografía de ecosistemas continentales, costeros y marinos del IDEAM.

Procesamiento de la cartografía IDEAM: identificación y carga de la geodatabase (.gdb) y de la capa de ecosistemas correspondiente, compuesta por aproximadamente 460.350 unidades espaciales.

Validación de sistemas de referencia espacial (CRS): revisión de los sistemas de coordenadas utilizados por ambas fuentes y transformación de los puntos de los incendios al CRS de la cartografía IDEAM (EPSG:4686 / MAGNA-SIRGAS).

Validación espacial: comprobación de que los puntos de incendio estuvieran dentro de la cobertura de la cartografía IDEAM y que cada punto tuviera una única correspondencia espacial.

Spatial Join: integración espacial entre los puntos de incendios y los polígonos de ecosistemas del IDEAM mediante la relación espacial within, asignando a cada incendio la información ambiental correspondiente al polígono donde se encuentra.

Clasificación ecosistémica: obtención de variables ambientales y ecológicas para cada incendio, incluyendo gran_bioma y ecos_general.

Validación de resultados: comprobación de que los 14.985 registros fueran correctamente asociados con información ecosistémica, sin casos sin clasificación y sin coincidencias espaciales múltiples.

Reclasificación de registros: mejora de la clasificación inicial de los incendios que aparecían como "Otros / No determinado", utilizando la ubicación espacial y la cartografía oficial del IDEAM.
Generación de la base final: exportación de los datos procesados y enriquecidos con información geográfica y ecosistémica para su posterior análisis.
Preparación para modelado: construcción de una base de datos más robusta para continuar con el análisis de factores asociados a la ocurrencia de incendios y el desarrollo del modelo de predicción.
