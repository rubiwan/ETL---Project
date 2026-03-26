# Plataforma ETL de Datos de Estaciones de Servicio
Este sistema implementa un pipeline de ingeniería de datos para la ingestión, transformación y explotación de grandes volúmenes de datos públicos sobre precios de combustibles en España.

### Autores y créditos

###  🐢️ [Emilio Quechen](https://github.com/eQuechen) y [Anabel Díaz](https://github.com/rubiwan) 👨‍💻

Desarrollado para la asignatura **Bases de Datos Avanzadas**.  
Incluye prácticas reales de diseño de software, modularización y pruebas automatizadas.


## Proyecto orientado a demostrar competencias reales en:

- Ingeniería de datos
- Backend
- Arquitectura de software
- Integración de bases de datos
- Motores de búsqueda


##  Problema que Resuelve

Los datos públicos de estaciones de servicio publicados por organismos oficiales:

- Se encuentran en **formatos masivos poco explotables (CSV)**
- No están optimizados para **búsquedas rápidas ni analítica**
- No permiten **consultas eficientes geográficas o por precios**
- No están preparados para **plataformas modernas de datos**
  
## Flujo de datos

- Extracción de datos desde fuentes públicas en formato CSV
- Normalización e inserción en base de datos relacional MySQL
- Transformación a formato documental JSON
- Migración a base de datos NoSQL MongoDB
- Generación de datasets optimizados para indexación
- Indexación en clúster ElasticSearch para búsquedas eficientes

### Arquitectura

```
Fuente pública: CSV Ministerio Transición Ecológica
├── Motor ETL en Java (JDBC)
├── Base de datos relacional (MySQL)
├── Transformación a JSON
├── Base de datos documental (MongoDB)
├── Dataset Bulk
└── ElasticSearch Cluster
```
### Tecnologías utilizadas

- **Java (procesamiento ETL)**
- **JDBC**
- **MySQL (modelo relacional)**
- **MongoDB (modelo documental)**
- **ElasticSearch (motor de búsqueda)**
- **Docker (entornos reproducibles)**
- **Procesamiento masivo CSV / JSON**

## Configuración del entorno


### Variables de entorno
	1A. Crear las variables de entorno:
		MYSQL_DB_NAME=estaciones_de_servicio_mysql;
		MYSQL_DB_HOST=localhost;
		MYSQL_USER=root;
		MYSQL_PASSWORD=mysql;
		MYSQL_DB_PORT=3306;
		
		MONGO_DB_NAME=estaciones_de_servicio_mongodb;
		MONGO_DB_HOST=localhost;
		MONGO_USER=root;
		MONGO_PASSWORD=mongo;
		MONGO_DB_PORT=27017;

	1B. Copiar y pegar en run/debug configurations si se usa Inteliij:
		MONGO_DB_HOST=localhost;MONGO_DB_NAME=estaciones_de_servicio_mongodb;MONGO_DB_PASSWORD=mongo;MONGO_DB_PORT=27017;MONGO_DB_USER=root;MYSQL_DB_HOST=localhost;MYSQL_DB_NAME=estaciones_de_servicio_mysql;MYSQL_DB_PASSWORD=mysql;MYSQL_DB_PORT=3306;MYSQL_DB_USER=root



### Configuración MySQL
	2. Instalar y ejecutar un contenedor de Docker con una imagen de MySQL:
		docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=mysql -d -p 3306:3306 mysql:latest

	3. Crear la conexión con MySQL en DataGrip y crear la BBDD con el nombre:
		"estaciones_de_servicio_mysql"
	
	4. Ejecutar en DataGrip el script SQL (DDL) que se encuentra en la ruta del proyecto "src/main/resources/sql":
		"estaciones_de_servicio_mysql.sql"



### Configuración MongoDB
	5. Instalar y ejecutar un contenedor de Docker con una imagen de MongoDB:
		docker run --name mongo-container -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=mongo -d -p 27017:27017 mongo:latest

	6. Crear la conexión con MongoDB en DataGrip

	7. La BBDD MongoDB se creará automáticamente con la primera inyección de datos en el
	siguiente paso, si decides crearla antes debes hacerlo con el nombre:
		"estaciones_de_servicio_mongodb"


### Finalmente
	8. Ejecutar el programa Java.

## Flujo de ejecución:
	8.1. Inyectar datos en la BBDD MySQL desde ficheros CSV.
	8.2. Descargar los datos de la BBDD Mysql y almacenarlos en archivos JSON.
	(Directorio dividido por colecciones)
	8.3. Inyectar datos en la BBDD MongoDB desde los archivos JSON.
	8.4. Generar el archivo "estaciones_bulk_raw.jsonl" con todas las estaciones
	para su implementación en EslasticSearch

### Uso de ElasticSearch:
	* Se ha creado un clúster en bonsai.io a través de una cuenta compartida por el grupo
	de trabajo
	* En dicho clúster se ha cargado el índice propuesto por este grupo, se encuentra
	almacenado en un archivo JSON en la ruta del proyecto 
	"src/main/resources/elasticsearch/bonsai_io":
		"elasticsearch_index.json"
	* Se han cargado los registros en el índice usando el archivo
	"estaciones_bulk_raw.jsonl" (generado a traves del programa en el punto 8.4).
	

##	Debido a la necesidad de credenciales, se enviarán para su prueba los comandos:
#### 	curl -X POST
	(Carga todos los registros en el índice propuesto para ElasticSearch usando
	el archivo generado en el programa).
#### 	curl -X GET
	(Devuleve todos los registros del índice para su comprobación).

