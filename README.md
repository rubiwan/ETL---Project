# Fuel Station ETL Pipeline

A Java project that transforms fuel-station CSV data into relational records, MongoDB documents and an Elasticsearch bulk-import file.

Developed as a group assignment for **Advanced Databases**, the project explores how the same dataset can be represented and queried across relational, document and search systems.

## Pipeline

1. Load the included CSV files into MySQL using JDBC.
2. Query relational data and export JSON documents.
3. Insert the documents into MongoDB collections.
4. Transform station JSON files into Elasticsearch bulk format.
5. Import the generated file into Elasticsearch separately.

The Java entry point runs steps 1–4. Elasticsearch indexing is a separate operation; the program does not upload data to a cluster.

## Technologies

Java 21 · Maven · JDBC · MySQL · MongoDB · Elasticsearch · OpenCSV · Gson · Lombok · SLF4J/Logback

MySQL and MongoDB can be run locally or in Docker containers.

## Data and search resources

The relational schema includes stations, companies, fuels, fuel prices and location information.

MongoDB documents are organised into five collections: `estaciones`, `empresas`, `carburantes`, `precios` and `ubicaciones`.

The repository also includes an Elasticsearch index definition and example queries for company, geographic and terrestrial-station searches:

- [SQL schema](bbdda-actividad3-grupal/src/main/resources/sql/estaciones_de_servicio_mysql.sql)
- [Elasticsearch index definition](bbdda-actividad3-grupal/src/main/resources/elasticsearch/bonsai_io/elasticsearch_index.json)
- [Example Elasticsearch queries](bbdda-actividad3-grupal/src/main/resources/elasticsearch/queries)

The original project documentation identifies Spain's Ministry for the Ecological Transition as the fuel-data source. The included CSV files are local inputs; this program does not fetch live prices.

## Build

Use **JDK 21**. Compilation was verified locally with Eclipse Temurin 21 through IntelliJ's Maven integration.

```bash
git clone https://github.com/rubiwan/ETL---Project.git
cd ETL---Project
mvn -f bbdda-actividad3-grupal/pom.xml clean compile
```

The terminal command requires Maven to be installed and available on your PATH, with Java 21 configured for Maven.

### IntelliJ IDEA

1. Open the repository and add `bbdda-actividad3-grupal/pom.xml` as a Maven project.
2. Set the Project SDK and module SDK to an installed JDK 21.
3. Set **Settings → Build Tools → Maven → Runner → JRE** to that JDK.
4. Run **Maven → Lifecycle → clean**, then **compile**.

The current project uses Lombok 1.18.30. Use JDK 21 for this setup; compilation with JDK 26 failed to generate Lombok members during local verification.

Compilation does not require running databases. Executing the pipeline does.

## Run the pipeline

### 1. Prepare the databases

Start MySQL and MongoDB instances with credentials appropriate to your local environment.

Create the MySQL database `estaciones_de_servicio_mysql`, select it in your SQL client, and execute the linked SQL schema. The script creates tables and indexes but does not create or select the database. Run it against a fresh schema: repeated index creation can fail.

MongoDB uses the database `estaciones_de_servicio_mongodb`, which is created when documents are first written. Ensure the configured MongoDB user can authenticate through the connection URI and write to that database.

### 2. Configure environment variables

These names match the variables read by the Java connectors:

| Variable | Example local value |
| --- | --- |
| `MYSQL_DB_NAME` | `estaciones_de_servicio_mysql` |
| `MYSQL_DB_HOST` | `localhost` |
| `MYSQL_DB_PORT` | `3306` |
| `MYSQL_DB_USER` | Your MySQL username |
| `MYSQL_DB_PASSWORD` | Your MySQL password |
| `MONGO_DB_NAME` | `estaciones_de_servicio_mongodb` |
| `MONGO_DB_HOST` | `localhost` |
| `MONGO_DB_PORT` | `27017` |
| `MONGO_DB_USER` | Your MongoDB username |
| `MONGO_DB_PASSWORD` | Your MongoDB password |

In IntelliJ, add these values to the **Environment variables** field of the Application run configuration. Credentials belong in your local configuration, not in committed files.

### 3. Run the Java entry point

Create an Application run configuration with:

- **Main class:** `app.Main`
- **Module classpath:** the imported Maven module
- **JRE:** JDK 21
- **Working directory:** the repository's `bbdda-actividad3-grupal` folder
- **Environment variables:** the database settings above

The working directory matters because the application uses relative `src/main/resources` paths.

The program writes JSON documents under `src/main/resources/json/` and generates:

```text
src/main/resources/elasticsearch/estaciones_bulk_raw.jsonl
```

Use the index definition and the generated JSONL file with your own Elasticsearch instance. Bulk actions target the index named `estaciones`. Cluster credentials and an active hosted instance are not supplied by this repository.

## Repository layout

| Path | Purpose |
| --- | --- |
| `bbdda-actividad3-grupal/pom.xml` | Maven build and dependencies |
| `src/main/java/app/` | Pipeline entry point |
| `src/main/java/config/` | Database connections and bulk-file transformation |
| `src/main/java/persistence/` | CSV, JDBC and MongoDB operations |
| `src/main/java/constants/` | Query and collection definitions |
| `src/main/java/dao/` | Data-access interfaces |
| `src/main/resources/csv/` | Included input datasets |
| `src/main/resources/sql/` | Relational schema |
| `src/main/resources/json/` | JSON output directories |
| `src/main/resources/elasticsearch/` | Index definition, queries and bulk output |

The `src/` paths above are relative to `bbdda-actividad3-grupal/`. IDE configuration and Maven build output are excluded from version control.

## Validation status

- **Verified:** clean compilation with JDK 21 through IntelliJ Maven.
- **Pending in this cleanup:** a complete run with MySQL and MongoDB, and Elasticsearch indexing/query verification.

This is an educational batch pipeline. A successful compilation alone does not validate the data-loading steps or establish that repeated execution is safe.

## Authors

The existing README credits [Emilio Quechen](https://github.com/eQuechen) and [Anabel Díaz](https://github.com/rubiwan). Source-file author comments also name Minerva.
