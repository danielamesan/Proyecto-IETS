# Proyecto-IETS

Proyecto: Caracterización de los Prestadores de Servicios de Salud en Colombia

Objetivo: El objetivo de este proyecto es integrar diferentes fuentes de datos para caracterizar el entorno poblacional y étnico de los prestadores de servicios de salud en Colombia. Esto permite realizar análisis más completos sobre cobertura, pertinencia cultural y necesidades demográficas por municipio y departamento para el año 2025.

🗂️ Fuentes de Datos
IETS: 
Municipios.xlsx: Contiene la información de departamento, municipio, superficie, población, población rural y región. 

Prestadores.xlsx: Contiene la información de los prestadores de salud a 13/05/2025 9:25 am


DANE: 
Proy_Pob_2020-2035.xlsx: Contiene la distribución de la población por sexo y edad, desagregada por departamento, municipio y año. 

Proy_Pob_EtnicoRacial.xlsx: Registra la población que se autoidentifica con algún grupo étnico, diferenciando entre cabecera y centro poblado/rural disperso.

🔄 Procesamiento

ARCHIVO Limpieza de datos y vistas en SQL.ipynb

1. Se realizó la limpieza de la base de datos de Municipios, asegurando coherencia y formato estandarizado, en las columnas Departamento y Municipio. Asimismo, debido a que la fila de municipio 94663 perteneciente a Guainía - Mapiripana esta vacia, se realiza una investigación y se encuentra que el municipio desde diciembre del 2019 paso a ser corregimiento del municipio Barrancominas. Por lo tanto se elimina esa linea. 

2. En las bases del DANE y Municipios, se renombraron las columnas correspondientes a Departamento y Municipio como depa_nombre y muni_nombre, respectivamente, para mantener consistencia con la base principal (Prestadores), y se deja todo en mayúsculas. Este formato permite que, si es necesario actualizar la base, simplemente se puede reemplazar con la versión nueva sin afectar la estructura.

3. Se agruparon y transformaron los datos mediante consultas SQL, con el fin de facilitar el análisis. Como resultado, se generaron dos vistas intermedias: 

- pobhm: Se basa en los datos del archivo Proy_Pob_2020-2035.xlsx y en agrega datos de población por departamento, municipio y año, incluyendo distribución por sexo y grupo etario. En esta vista, se clasifican los grupos etarios de la siguiente manera: primera infancia (0-5 años), infancia (6 - 11 años), adolescencia (12-17 años), juventud (18 - 26 años), adultez (27 - 59 años) y vejez (60 años y más).

- pobetn: Se basa en los datos del archivo Proy_Pob_EtnicoRacial.xlsx y agrega datos de etnicidad con las variables:
etnia_cabecera: población étnica en cabecera municipal
etnia_cprd: población étnica en cabecera municipal
etnia_total: población étnica total

Se unieron mediante un JOIN 1 a 1 utilizando como claves: MPIO y AÑO

Se crea la vista "data_pob", la cual garantiza la integridad entre las bases de datos al unificar la información poblacional y étnica. 

4. Luego se une esta vista con la información brindad en la BD de Municipios. Se crea una vista con toda la información poblacional, étnica y geografica llamada data_pob_g. En el cruce de estas bases de datos, se observa que en la BD Municipios no aparecen todos los municipios, hacen falta 4, a saber: 
BOLÍVAR-NOROSÍ, CAUCA-GUACHENÉ, CÓRDOBA-SAN JOSÉ DE URÉ, CÓRDOBA-TUCHÍN. Se hace una busqueda y se agrega la información faltante de superficie y región. 
Asimismo, en este punto se cambia Bogotá, D.C por BOGOTÁ para que concuerde con la BD Prestadores. 

NOTA: Para facilitar la gestión y el análisis de los datos, se han creado varias vistas intermedias separadas, cada una con un enfoque específico y bien definido. Esto permite mantener la modularidad y claridad en el manejo de la información, facilitando actualizaciones y posibles revisiones parciales sin afectar el conjunto completo de datos.Esta estructura modular garantiza la integridad de los datos y optimiza el rendimiento, al evitar procesos innecesarios de unión y transformación durante las consultas, además de facilitar la reutilización de las vistas para futuros análisis específicos.

5. Se agrupan datos en la BD de prestadores, se crean columnas dependiendo del tipo de ips, de la persona juridica, de la naturaleza de la entidad (público, privado o mixta), sobre vencimiento de permisos por año, y municipios priorizados. Al unirlo con data_pob_g, se encuentra que de 988 datos solo estan uniendose 921. Se revisa que sucede con esos 65 datos faltantes y se encuentra que por errores de tipeo estos datos no estan siendo reconocidos. Se corrigen y queda una base de datos total de 948 municipios donde hay prestadores de salud. El total de municipios para Colombia es de 1121.

ARCHIVO Análisis prestadores.ipynb
En este archivo se encuentra las tablas y gráficos que aparecen en el informe con el codigo debidamente comentado. 

