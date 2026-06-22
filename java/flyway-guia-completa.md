# 🐦 Flyway: Guía Completa de Migraciones de Base de Datos

## 📋 Tabla de Contenidos

1. [¿Qué es Flyway?](#1-qué-es-flyway)
2. [Conceptos fundamentales](#2-conceptos-fundamentales)
3. [Instalación y configuración](#3-instalación-y-configuración)
4. [Nomenclatura y tipos de migraciones](#4-nomenclatura-y-tipos-de-migraciones)
5. [Comandos principales](#5-comandos-principales)
6. [Flyway con Spring Boot](#6-flyway-con-spring-boot)
7. [Flyway con Maven Plugin](#7-flyway-con-maven-plugin)
8. [Flyway con Gradle Plugin](#8-flyway-con-gradle-plugin)
9. [Flyway con Docker](#9-flyway-con-docker)
10. [Configuración avanzada](#10-configuración-avanzada)
11. [Callbacks](#11-callbacks)
12. [Migraciones Java (programáticas)](#12-migraciones-java-programáticas)
13. [Reparar el historial de migraciones](#13-reparar-el-historial-de-migraciones)
14. [Entornos múltiples y estrategias de equipo](#14-entornos-múltiples-y-estrategias-de-equipo)
15. [Buenas prácticas](#15-buenas-prácticas)
16. [Solución de problemas comunes](#16-solución-de-problemas-comunes)
17. [Proyecto práctico completo](#17-proyecto-práctico-completo)

---

## 1. ¿Qué es Flyway?

**Flyway** es una herramienta de **migración de bases de datos** de código abierto que permite versionar y gestionar los cambios en el esquema de tu base de datos de forma ordenada, reproducible y automatizable, igual que Git gestiona el código fuente.

```
┌──────────────────────────────────────────────────────────────────┐
│                      EL PROBLEMA SIN FLYWAY                      │
│                                                                  │
│  Dev A: "Añadí una columna email a la tabla users"               │
│  Dev B: "¿Cuál script SQL debo ejecutar?"                        │
│  Dev A: "Mira en el chat de Slack del mes pasado..."             │
│  Producción: 💥 Error – columna 'email' no existe                │
└──────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────┐
│                      LA SOLUCIÓN CON FLYWAY                      │
│                                                                  │
│  ✅ Scripts SQL versionados junto al código fuente               │
│  ✅ Flyway aplica automáticamente los cambios pendientes         │
│  ✅ Historial completo de qué se ejecutó, cuándo y en qué BD     │
│  ✅ Integración con Spring Boot, Maven, Gradle, Docker y CLI     │
└──────────────────────────────────────────────────────────────────┘
```

### ¿Por qué usar Flyway?

| Beneficio | Descripción |
|-----------|-------------|
| 🗂️ **Control de versiones** | Cada cambio en la BD queda registrado como un script numerado |
| 🤝 **Trabajo en equipo** | Todos aplican exactamente los mismos cambios en el mismo orden |
| 🚀 **Automatización** | Se integra en el arranque de la app o en pipelines CI/CD |
| 🔄 **Reproducibilidad** | Recrea cualquier entorno (dev, staging, prod) desde cero |
| 🧪 **Testing** | Aplica migraciones en bases de datos en memoria (H2, HSQLDB) |
| 📋 **Auditoría** | Tabla `flyway_schema_history` con todo el historial |

### Bases de datos compatibles

| Base de datos | Soporte Community | Soporte Teams/Enterprise |
|---------------|:-----------------:|:------------------------:|
| PostgreSQL | ✅ | ✅ |
| MySQL / MariaDB | ✅ | ✅ |
| H2 | ✅ | ✅ |
| SQLite | ✅ | ✅ |
| Microsoft SQL Server | ✅ | ✅ |
| Oracle Database | ✅ | ✅ |
| DB2 | ✅ | ✅ |
| CockroachDB | ✅ | ✅ |
| Aurora (AWS) | ✅ | ✅ |

---

## 2. Conceptos fundamentales

### 2.1 La tabla `flyway_schema_history`

Flyway crea automáticamente una tabla especial en la base de datos donde registra cada migración ejecutada:

```sql
flyway_schema_history
┌──────────────┬──────────────────────┬──────────┬──────────────┬────────────┬──────────┐
│ installed_rank│ version              │ description│ type        │ checksum   │ success  │
├──────────────┼──────────────────────┼──────────┼──────────────┼────────────┼──────────┤
│      1       │ 1                    │ init     │ SQL          │ 1234567890 │ true     │
│      2       │ 2                    │ add users│ SQL          │ 9876543210 │ true     │
│      3       │ 3                    │ add email│ SQL          │ 1122334455 │ true     │
└──────────────┴──────────────────────┴──────────┴──────────────┴────────────┴──────────┘
```

### 2.2 Estados de una migración

```
┌─────────────────────────────────────────────────────────────┐
│                  CICLO DE VIDA DE UNA MIGRACIÓN             │
│                                                             │
│   Archivo SQL   ──►  Pending   ──►  Applied   ──►  Success  │
│   en disco             │                          ○         │
│                        └──────────────────────►  Failed     │
│                                                   ✗         │
└─────────────────────────────────────────────────────────────┘
```

| Estado | Descripción |
|--------|-------------|
| **Pending** | Migración presente en disco pero aún no aplicada |
| **Applied** | Migración ejecutada con éxito |
| **Failed** | La migración falló (solo en DBs sin soporte transaccional DDL) |
| **Missing** | Migración en el historial pero no encontrada en disco |
| **Ignored** | Migración con versión menor a la baseline |
| **Future** | Versión superior a la más alta conocida |

### 2.3 El checksum

Flyway calcula un **checksum** (hash) de cada script SQL cuando lo aplica. Si el contenido del script cambia después de haber sido aplicado, Flyway detectará la discrepancia y **rechazará** arrancar la aplicación.

> ⚠️ **Regla de oro**: una vez aplicado, **nunca modifiques** un script de migración. Crea uno nuevo en su lugar.

### 2.4 Versioned vs Repeatable vs Undo migrations

```
Versioned Migration:  V1__init.sql, V2__add_users.sql, V3__add_email.sql
                      │
                      └── Se ejecutan UNA SOLA VEZ, en orden

Repeatable Migration: R__seed_data.sql, R__create_views.sql
                      │
                      └── Se re-ejecutan cuando su checksum cambia

Undo Migration:       U2__add_users.sql  (Flyway Teams)
                      │
                      └── Revierte una migración versionada específica
```

---

## 3. Instalación y configuración

### 3.1 Flyway CLI (línea de comandos)

#### Linux / macOS

```bash
# Descargar la última versión
wget -qO- https://repo1.maven.org/maven2/org/flywaydb/flyway-commandline/10.15.0/flyway-commandline-10.15.0-linux-x64.tar.gz \
  | tar xvz

# Mover al PATH
sudo mv flyway-10.15.0 /opt/flyway
sudo ln -s /opt/flyway/flyway /usr/local/bin/flyway

# Verificar instalación
flyway -v
```

#### macOS con Homebrew

```bash
brew install flyway
flyway -v
```

#### Windows (Chocolatey)

```powershell
choco install flyway
flyway -v
```

#### Windows (manual)

```powershell
# Descargar y descomprimir desde https://flywaydb.org/download/community
# Añadir la carpeta al PATH del sistema
$env:PATH += ";C:\flyway-10.15.0"
flyway -v
```

### 3.2 Estructura de archivos del CLI

```
flyway/
├── conf/
│   └── flyway.toml          # Configuración principal
├── sql/
│   ├── V1__init_schema.sql
│   ├── V2__add_users.sql
│   └── R__seed_data.sql
├── jars/                    # Drivers JDBC adicionales
└── flyway                   # Ejecutable
```

### 3.3 Archivo de configuración `flyway.toml`

```toml
[environments.default]
url = "jdbc:postgresql://localhost:5432/mydb"
user = "postgres"
password = "secret"

[flyway]
locations = ["filesystem:sql"]
defaultSchema = "public"
```

También se puede usar formato `.conf` (clave=valor):

```properties
# flyway.conf
flyway.url=jdbc:postgresql://localhost:5432/mydb
flyway.user=postgres
flyway.password=secret
flyway.locations=filesystem:sql
```

### 3.4 Variables de entorno

Todas las opciones también se pueden configurar mediante variables de entorno:

```bash
export FLYWAY_URL=jdbc:postgresql://localhost:5432/mydb
export FLYWAY_USER=postgres
export FLYWAY_PASSWORD=secret

flyway migrate
```

---

## 4. Nomenclatura y tipos de migraciones

### 4.1 Convención de nombres

```
V 2 . 1 . 3 __ add_user_email . sql
│ │─────────│ │──────────────│ │───│
│    │       │       │          │
│  Versión   │  Descripción   Extensión
│            │  (doble guion bajo)
Prefijo      Separador
(V/R/U)
```

### 4.2 Reglas de nomenclatura

| Elemento | Regla | Ejemplos válidos |
|----------|-------|-----------------|
| **Prefijo** | `V` (versioned), `R` (repeatable), `U` (undo) | `V`, `R`, `U` |
| **Versión** | Números separados por `.` o `_` | `1`, `2.1`, `1_0_3` |
| **Separador** | Exactamente dos guiones bajos `__` | `__` |
| **Descripción** | Palabras separadas por `_` o espacios | `add_user_table`, `create index` |
| **Extensión** | `.sql` o `.java` | `.sql`, `.java` |

### 4.3 Ejemplos de nombres correctos

```
# Migraciones versionadas
V1__init_schema.sql
V2__create_users_table.sql
V2.1__add_email_to_users.sql
V3__create_products_table.sql
V3.0.1__fix_products_index.sql

# Migraciones repetibles
R__create_views.sql
R__seed_reference_data.sql
R__update_stored_procedures.sql

# Migraciones de deshacer (Flyway Teams)
U2__create_users_table.sql
U3__create_products_table.sql
```

### 4.4 Orden de ejecución

```
Orden de versiones:
  1  <  1.1  <  1.1.1  <  2  <  10  <  10.1

  ✅ Correcto: V1__a.sql → V1.1__b.sql → V2__c.sql
  ✅ Correcto: V1__a.sql → V2__b.sql   → V10__c.sql
  ❌ Incorrecto: No mezcles formatos (V1_0 y V1.0 a la vez)
```

---

## 5. Comandos principales

### 5.1 `migrate`

Aplica todas las migraciones pendientes en orden.

```bash
flyway migrate

# Salida esperada:
# Flyway Community Edition 10.15.0
# Database: jdbc:postgresql://localhost:5432/mydb (PostgreSQL 16.0)
# Schema history table "public"."flyway_schema_history" does not exist yet
# Successfully applied 3 migrations to schema "public"
# (execution time 00:00.123s)
```

### 5.2 `info`

Muestra el estado actual de todas las migraciones.

```bash
flyway info

# Salida:
# +------------+---------+---------------------+------+---------------------+---------+
# | Category   | Version | Description         | Type | Installed On        | State   |
# +------------+---------+---------------------+------+---------------------+---------+
# | Versioned  | 1       | init schema         | SQL  | 2024-01-15 10:00:00 | Success |
# | Versioned  | 2       | create users table  | SQL  | 2024-01-15 10:00:01 | Success |
# | Versioned  | 3       | add email to users  | SQL  |                     | Pending |
# +------------+---------+---------------------+------+---------------------+---------+
```

### 5.3 `validate`

Verifica que los checksums de las migraciones en disco coincidan con los del historial.

```bash
flyway validate

# Si todo está bien:
# Successfully validated 2 migrations (execution time 00:00.025s)

# Si hay discrepancia:
# ERROR: Validate failed: Migrations have failed validation
# Migration checksum mismatch for migration version 2
```

### 5.4 `baseline`

Establece una versión de referencia en una base de datos que ya existe (no fue creada con Flyway).

```bash
# Marca la versión 1 como baseline (punto de partida)
flyway baseline -baselineVersion=1 -baselineDescription="Esquema inicial existente"

# Solo se aplicarán migraciones con versión > 1
```

### 5.5 `repair`

Repara la tabla de historial de migraciones:
- Elimina entradas fallidas.
- Realinea checksums si los scripts fueron modificados (útil en desarrollo).

```bash
flyway repair
```

### 5.6 `clean`

> ⚠️ **¡PELIGROSO!** Elimina **todos** los objetos de la base de datos (tablas, vistas, procedimientos…).

```bash
# Solo usar en desarrollo/testing, NUNCA en producción
flyway clean

# Para protegerse, usa cleanDisabled=true en producción:
# flyway.cleanDisabled=true
```

### 5.7 `undo` (Flyway Teams)

Revierte la última migración aplicada usando el script `U<version>__descripcion.sql`.

```bash
flyway undo
```

### 5.8 Resumen de comandos

```
┌──────────────┬─────────────────────────────────────────────────────┐
│   Comando    │ Descripción                                         │
├──────────────┼─────────────────────────────────────────────────────┤
│ migrate      │ Aplica migraciones pendientes                       │
│ info         │ Muestra estado de todas las migraciones             │
│ validate     │ Verifica integridad de checksums                    │
│ baseline     │ Inicializa historial en BD existente                │
│ repair       │ Repara entradas fallidas / realinea checksums       │
│ clean        │ ⚠️  Borra todo el esquema (solo dev/test)          │
│ undo         │ Revierte última migración (Flyway Teams)            │
└──────────────┴─────────────────────────────────────────────────────┘
```

---

## 6. Flyway con Spring Boot

### 6.1 Dependencia Maven

```xml
<!-- pom.xml -->
<dependencies>
    <!-- Spring Boot Starter Data JPA (incluye JDBC) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Flyway Core -->
    <dependency>
        <groupId>org.flywaydb</groupId>
        <artifactId>flyway-core</artifactId>
    </dependency>

    <!-- Driver de base de datos (elige el tuyo) -->
    <!-- PostgreSQL -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>runtime</scope>
    </dependency>

    <!-- MySQL (alternativa) -->
    <!--
    <dependency>
        <groupId>com.mysql</groupId>
        <artifactId>mysql-connector-j</artifactId>
        <scope>runtime</scope>
    </dependency>
    -->

    <!-- H2 (para tests) -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

> **Nota para MySQL/MariaDB**: A partir de Flyway 9, necesitas añadir también el módulo específico:
> ```xml
> <dependency>
>     <groupId>org.flywaydb</groupId>
>     <artifactId>flyway-mysql</artifactId>
> </dependency>
> ```

### 6.2 Dependencia Gradle

```groovy
// build.gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.flywaydb:flyway-core'
    runtimeOnly 'org.postgresql:postgresql'
    testRuntimeOnly 'com.h2database:h2'
}
```

```kotlin
// build.gradle.kts
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-data-jpa")
    implementation("org.flywaydb:flyway-core")
    runtimeOnly("org.postgresql:postgresql")
    testRuntimeOnly("com.h2database:h2")
}
```

### 6.3 Configuración en `application.properties`

```properties
# application.properties

# Conexión a la base de datos
spring.datasource.url=jdbc:postgresql://localhost:5432/myapp
spring.datasource.username=postgres
spring.datasource.password=secret
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA / Hibernate
spring.jpa.hibernate.ddl-auto=validate
# ⚠️ Usa 'validate' o 'none' con Flyway, NUNCA 'create' o 'create-drop'

# Flyway
spring.flyway.enabled=true
spring.flyway.locations=classpath:db/migration
spring.flyway.baseline-on-migrate=false
spring.flyway.validate-on-migrate=true
```

### 6.4 Configuración en `application.yml`

```yaml
# application.yml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/myapp
    username: postgres
    password: secret
    driver-class-name: org.postgresql.Driver

  jpa:
    hibernate:
      ddl-auto: validate   # 'validate' o 'none' con Flyway

  flyway:
    enabled: true
    locations: classpath:db/migration
    baseline-on-migrate: false
    validate-on-migrate: true
    table: flyway_schema_history    # nombre de la tabla de historial
    default-schema: public
    schemas:
      - public
```

### 6.5 Estructura de directorios del proyecto

```
src/
├── main/
│   ├── java/
│   │   └── com/empresa/miapp/
│   │       └── MiAplicacion.java
│   └── resources/
│       ├── application.yml
│       └── db/
│           └── migration/
│               ├── V1__init_schema.sql
│               ├── V2__create_users_table.sql
│               ├── V3__create_products_table.sql
│               ├── V4__add_email_to_users.sql
│               └── R__seed_reference_data.sql
└── test/
    ├── java/
    │   └── com/empresa/miapp/
    │       └── MiAplicacionTest.java
    └── resources/
        └── application-test.yml
```

### 6.6 Scripts de migración de ejemplo

#### `V1__init_schema.sql`

```sql
-- V1__init_schema.sql
-- Creación del esquema inicial

CREATE TABLE IF NOT EXISTS roles (
    id   SERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE IF NOT EXISTS users (
    id         BIGSERIAL PRIMARY KEY,
    username   VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

#### `V2__add_email_and_role_to_users.sql`

```sql
-- V2__add_email_and_role_to_users.sql
-- Añadir email y relación con roles

ALTER TABLE users
    ADD COLUMN email   VARCHAR(255) NOT NULL DEFAULT '',
    ADD COLUMN role_id INT REFERENCES roles(id);

-- Índice para búsqueda por email
CREATE INDEX idx_users_email ON users(email);

-- Eliminar valor por defecto temporal
ALTER TABLE users ALTER COLUMN email DROP DEFAULT;
```

#### `V3__create_products_table.sql`

```sql
-- V3__create_products_table.sql
CREATE TABLE products (
    id          BIGSERIAL PRIMARY KEY,
    name        VARCHAR(200) NOT NULL,
    description TEXT,
    price       NUMERIC(10, 2) NOT NULL CHECK (price >= 0),
    stock       INT NOT NULL DEFAULT 0,
    created_at  TIMESTAMP NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMP NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_products_name ON products(name);
```

#### `R__seed_reference_data.sql`

```sql
-- R__seed_reference_data.sql
-- Se re-ejecuta automáticamente si cambia el contenido

INSERT INTO roles (name) VALUES ('ADMIN')
    ON CONFLICT (name) DO NOTHING;

INSERT INTO roles (name) VALUES ('USER')
    ON CONFLICT (name) DO NOTHING;

INSERT INTO roles (name) VALUES ('MODERATOR')
    ON CONFLICT (name) DO NOTHING;
```

### 6.7 Configuración para pruebas con H2

```yaml
# src/test/resources/application-test.yml
spring:
  datasource:
    url: jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;MODE=PostgreSQL
    driver-class-name: org.h2.Driver
    username: sa
    password:

  jpa:
    database-platform: org.hibernate.dialect.H2Dialect
    hibernate:
      ddl-auto: none

  flyway:
    enabled: true
    locations: classpath:db/migration
    clean-on-validation-error: true  # Útil en tests para limpiar y re-migrar
```

```java
// MiAplicacionTest.java
@SpringBootTest
@ActiveProfiles("test")
class MiAplicacionTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    void contextLoads() {
        // Si llega aquí, las migraciones se aplicaron correctamente
    }

    @Test
    void deberiaCrearTablaUsers() {
        // La tabla 'users' fue creada por Flyway en el arranque
        assertThat(userRepository.count()).isGreaterThanOrEqualTo(0);
    }
}
```

### 6.8 Propiedades completas de Spring Flyway

```yaml
spring:
  flyway:
    # Generales
    enabled: true                        # Activar/desactivar Flyway
    locations: classpath:db/migration    # Ruta(s) a los scripts
    encoding: UTF-8                      # Codificación de los scripts
    sql-migration-prefix: V              # Prefijo para versionadas
    sql-migration-separator: __          # Separador versión/descripción
    sql-migration-suffixes: .sql         # Extensiones válidas
    repeatable-sql-migration-prefix: R  # Prefijo para repetibles

    # Esquema y tabla
    table: flyway_schema_history         # Nombre de la tabla de historial
    schemas:                             # Esquemas a gestionar
      - public
    default-schema: public

    # Comportamiento
    baseline-on-migrate: false           # Crear baseline si no existe historial
    baseline-version: 1                  # Versión del baseline
    baseline-description: Baseline       # Descripción del baseline
    validate-on-migrate: true            # Validar checksums al migrar
    clean-disabled: true                 # Desactivar el comando 'clean' (recomendado en prod)
    out-of-order: false                  # Permitir migraciones fuera de orden
    ignore-missing-migrations: false     # Ignorar migraciones ausentes del disco
    ignore-future-migrations: true       # Ignorar versiones desconocidas del futuro
    mixed: false                         # Permitir transacciones mixtas (DDL+DML)

    # Placeholders
    placeholder-replacement: true
    placeholders:
      schema: public
      environment: production

    # Conexión (si es diferente al datasource principal)
    url: jdbc:postgresql://localhost:5432/mydb
    user: flyway_user
    password: flyway_pass
```

---

## 7. Flyway con Maven Plugin

### 7.1 Configuración del plugin

```xml
<!-- pom.xml -->
<build>
    <plugins>
        <plugin>
            <groupId>org.flywaydb</groupId>
            <artifactId>flyway-maven-plugin</artifactId>
            <version>10.15.0</version>
            <configuration>
                <url>jdbc:postgresql://localhost:5432/mydb</url>
                <user>postgres</user>
                <password>secret</password>
                <locations>
                    <location>filesystem:src/main/resources/db/migration</location>
                </locations>
                <cleanDisabled>true</cleanDisabled>
            </configuration>
            <dependencies>
                <!-- Driver JDBC -->
                <dependency>
                    <groupId>org.postgresql</groupId>
                    <artifactId>postgresql</artifactId>
                    <version>42.7.3</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

### 7.2 Comandos Maven

```bash
# Aplicar migraciones pendientes
mvn flyway:migrate

# Ver estado de las migraciones
mvn flyway:info

# Validar checksums
mvn flyway:validate

# Crear baseline
mvn flyway:baseline

# Reparar historial
mvn flyway:repair

# Limpiar BD (¡solo dev/test!)
mvn flyway:clean
```

### 7.3 Pasar credenciales por línea de comandos

```bash
mvn flyway:migrate \
  -Dflyway.url=jdbc:postgresql://localhost:5432/mydb \
  -Dflyway.user=postgres \
  -Dflyway.password=secret
```

### 7.4 Perfiles Maven para distintos entornos

```xml
<!-- pom.xml -->
<profiles>
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <flyway.url>jdbc:postgresql://localhost:5432/myapp_dev</flyway.url>
            <flyway.user>postgres</flyway.user>
            <flyway.password>dev_password</flyway.password>
        </properties>
    </profile>

    <profile>
        <id>prod</id>
        <properties>
            <flyway.url>${env.DB_URL}</flyway.url>
            <flyway.user>${env.DB_USER}</flyway.user>
            <flyway.password>${env.DB_PASSWORD}</flyway.password>
        </properties>
    </profile>
</profiles>
```

```bash
# Migrar en desarrollo (por defecto)
mvn flyway:migrate

# Migrar en producción
mvn flyway:migrate -Pprod
```

---

## 8. Flyway con Gradle Plugin

### 8.1 Configuración del plugin

```groovy
// build.gradle
plugins {
    id 'org.flywaydb.flyway' version '10.15.0'
}

flyway {
    url      = 'jdbc:postgresql://localhost:5432/mydb'
    user     = 'postgres'
    password = 'secret'
    locations = ['filesystem:src/main/resources/db/migration']
    cleanDisabled = true
}

dependencies {
    // Driver JDBC para el plugin
    implementation 'org.postgresql:postgresql:42.7.3'
}
```

```kotlin
// build.gradle.kts
plugins {
    id("org.flywaydb.flyway") version "10.15.0"
}

flyway {
    url      = "jdbc:postgresql://localhost:5432/mydb"
    user     = "postgres"
    password = "secret"
    locations = arrayOf("filesystem:src/main/resources/db/migration")
    isCleanDisabled = true
}
```

### 8.2 Comandos Gradle

```bash
# Aplicar migraciones
./gradlew flywayMigrate

# Ver estado
./gradlew flywayInfo

# Validar
./gradlew flywayValidate

# Crear baseline
./gradlew flywayBaseline

# Reparar
./gradlew flywayRepair

# Limpiar (¡solo dev/test!)
./gradlew flywayClean
```

---

## 9. Flyway con Docker

### 9.1 Imagen oficial de Flyway

```bash
# Aplicar migraciones con Docker
docker run --rm \
  -v $(pwd)/sql:/flyway/sql \
  -v $(pwd)/conf:/flyway/conf \
  flyway/flyway:10 migrate

# Con variables de entorno
docker run --rm \
  -v $(pwd)/sql:/flyway/sql \
  -e FLYWAY_URL=jdbc:postgresql://host.docker.internal:5432/mydb \
  -e FLYWAY_USER=postgres \
  -e FLYWAY_PASSWORD=secret \
  flyway/flyway:10 migrate
```

### 9.2 Docker Compose con Flyway

```yaml
# docker-compose.yml
version: '3.8'

services:
  postgres:
    image: postgres:16
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: secret
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

  flyway:
    image: flyway/flyway:10
    command: migrate
    volumes:
      - ./src/main/resources/db/migration:/flyway/sql
    environment:
      FLYWAY_URL: jdbc:postgresql://postgres:5432/myapp
      FLYWAY_USER: postgres
      FLYWAY_PASSWORD: secret
      FLYWAY_CONNECT_RETRIES: 10
    depends_on:
      postgres:
        condition: service_healthy

  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/myapp
      SPRING_DATASOURCE_USERNAME: postgres
      SPRING_DATASOURCE_PASSWORD: secret
      SPRING_FLYWAY_ENABLED: "false"   # Flyway ya se ejecutó en el servicio 'flyway'
    depends_on:
      flyway:
        condition: service_completed_successfully
```

```bash
# Levantar todo el stack
docker compose up

# Solo correr la BD y las migraciones
docker compose up postgres flyway

# Ver los logs de flyway
docker compose logs flyway
```

### 9.3 Dockerfile multi-stage con Flyway

```dockerfile
# Dockerfile
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package -DskipTests

FROM eclipse-temurin:21-jre
WORKDIR /app

# Copiar el JAR compilado
COPY --from=build /app/target/*.jar app.jar

# Flyway se ejecuta automáticamente al arrancar Spring Boot
ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## 10. Configuración avanzada

### 10.1 Múltiples ubicaciones de scripts

```yaml
# application.yml
spring:
  flyway:
    locations:
      - classpath:db/migration          # Scripts principales
      - classpath:db/migration/seed     # Datos de prueba
      - filesystem:/opt/migrations      # Scripts externos (en producción)
```

### 10.2 Placeholders (variables en scripts SQL)

Los placeholders permiten reutilizar scripts en distintos entornos:

```yaml
# application.yml
spring:
  flyway:
    placeholder-replacement: true
    placeholders:
      schema: public
      app_user: myapp_user
      admin_role: ADMIN
```

```sql
-- V5__setup_permissions.sql
-- Se reemplaza ${schema}, ${app_user} y ${admin_role} al ejecutar

GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA ${schema}
  TO ${app_user};

INSERT INTO roles (name) VALUES ('${admin_role}')
  ON CONFLICT (name) DO NOTHING;
```

### 10.3 Migraciones fuera de orden (`out-of-order`)

Útil cuando varios desarrolladores trabajan en paralelo y hacen merge:

```yaml
spring:
  flyway:
    out-of-order: true
```

```
Sin out-of-order:   V1 → V2 → V3 → ERROR (V2.1 aparece tarde)
Con out-of-order:   V1 → V2 → V2.1 → V3  ✅ (aplicada en orden correcto)
```

> ⚠️ Solo activa esto durante el período de merge. Desactívalo en producción.

### 10.4 Esquemas múltiples

```yaml
spring:
  flyway:
    schemas:
      - public
      - reporting
      - audit
    default-schema: public
```

### 10.5 Tabla de historial personalizada

```yaml
spring:
  flyway:
    table: schema_version        # Por defecto: flyway_schema_history
    tablespace: pg_default       # Para Oracle/PostgreSQL
```

### 10.6 Ignorar migraciones faltantes

Útil cuando se eliminan scripts antiguos deliberadamente:

```yaml
spring:
  flyway:
    ignore-missing-migrations: true
```

### 10.7 Estrategia de bloqueo

Flyway usa bloqueo a nivel de BD para evitar que varias instancias migren al mismo tiempo (crítico en entornos con múltiples réplicas):

```yaml
spring:
  flyway:
    lock-retry-count: 50        # Intentos de obtener el bloqueo
    connect-retries: 10         # Reintentos de conexión
    connect-retries-interval: 1 # Segundos entre reintentos
```

---

## 11. Callbacks

Los callbacks permiten ejecutar lógica personalizada en momentos específicos del ciclo de vida de Flyway.

### 11.1 Callbacks con SQL

Crea archivos SQL con nombres especiales en la carpeta de migraciones:

```
db/migration/
├── beforeMigrate.sql           # Antes de cualquier migración
├── afterMigrate.sql            # Después de aplicar todas
├── beforeEachMigrate.sql       # Antes de cada migración individual
├── afterEachMigrate.sql        # Después de cada migración individual
├── afterMigrateError.sql       # Si una migración falla
├── beforeClean.sql             # Antes de 'clean'
└── afterClean.sql              # Después de 'clean'
```

#### Ejemplo: `afterMigrate.sql`

```sql
-- afterMigrate.sql
-- Actualizar estadísticas de la BD tras migrar

ANALYZE;

INSERT INTO migration_log (migrated_at, comment)
VALUES (NOW(), 'Flyway migration completed');
```

### 11.2 Callbacks con Java

```java
// src/main/java/com/empresa/miapp/config/FlywayMigrationCallback.java
import org.flywaydb.core.api.callback.Callback;
import org.flywaydb.core.api.callback.Context;
import org.flywaydb.core.api.callback.Event;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

@Component
public class FlywayMigrationCallback implements Callback {

    private static final Logger log = LoggerFactory.getLogger(FlywayMigrationCallback.class);

    @Override
    public boolean supports(Event event, Context context) {
        return event == Event.AFTER_MIGRATE || event == Event.AFTER_MIGRATE_ERROR;
    }

    @Override
    public boolean canHandleInTransaction(Event event, Context context) {
        return true;
    }

    @Override
    public void handle(Event event, Context context) {
        if (event == Event.AFTER_MIGRATE) {
            log.info("✅ Flyway: todas las migraciones aplicadas correctamente");
        } else if (event == Event.AFTER_MIGRATE_ERROR) {
            log.error("❌ Flyway: error durante la migración");
        }
    }

    @Override
    public String getCallbackName() {
        return "MigracionCallback";
    }
}
```

```java
// Registrar el callback en la configuración de Flyway
import org.flywaydb.core.Flyway;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FlywayConfig {

    @Bean
    public Flyway flyway(DataSource dataSource, FlywayMigrationCallback callback) {
        return Flyway.configure()
                .dataSource(dataSource)
                .locations("classpath:db/migration")
                .callbacks(callback)
                .load();
    }
}
```

---

## 12. Migraciones Java (programáticas)

Para cambios que no pueden expresarse en SQL puro (transformaciones de datos complejas, llamadas a APIs, lógica de negocio):

```java
// src/main/java/db/migration/V5__migrate_user_data.java
// IMPORTANTE: El paquete debe ser exactamente 'db.migration'

import org.flywaydb.core.api.migration.BaseJavaMigration;
import org.flywaydb.core.api.migration.Context;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.datasource.SingleConnectionDataSource;

public class V5__migrate_user_data extends BaseJavaMigration {

    @Override
    public void migrate(Context context) throws Exception {
        JdbcTemplate jdbc = new JdbcTemplate(
            new SingleConnectionDataSource(context.getConnection(), true)
        );

        // Leer datos en el formato antiguo
        List<Map<String, Object>> rows = jdbc.queryForList(
            "SELECT id, full_name FROM users WHERE first_name IS NULL"
        );

        // Transformar y actualizar
        for (Map<String, Object> row : rows) {
            Long id = (Long) row.get("id");
            String fullName = (String) row.get("full_name");

            String[] parts = fullName.split(" ", 2);
            String firstName = parts[0];
            String lastName = parts.length > 1 ? parts[1] : "";

            jdbc.update(
                "UPDATE users SET first_name = ?, last_name = ? WHERE id = ?",
                firstName, lastName, id
            );
        }
    }
}
```

> ⚠️ Las migraciones Java también tienen checksum (basado en el bytecode). No modifiques la clase una vez aplicada.

---

## 13. Reparar el historial de migraciones

### 13.1 Escenario: migración fallida en una BD sin DDL transaccional

MySQL no soporta transacciones DDL, así que si una migración falla a mitad, deja la BD en estado inconsistente y la tabla de historial con una entrada `failed`.

```bash
# 1. Corregir manualmente el estado de la BD
mysql -u root -p mydb -e "DROP TABLE IF EXISTS tabla_a_medias;"

# 2. Corregir el script SQL (si tenía errores)
# editar V4__crear_tabla.sql

# 3. Limpiar la entrada fallida del historial
flyway repair

# 4. Volver a migrar
flyway migrate
```

### 13.2 Escenario: script modificado accidentalmente (desarrollo)

```bash
# Error: "Migration checksum mismatch for version 3"
# Causa: alguien editó V3__create_products_table.sql ya aplicada

# Opción 1: Realinear el checksum (solo en desarrollo)
flyway repair
# Flyway actualiza el checksum en el historial para que coincida con el archivo actual

# Opción 2 (recomendada): revertir el archivo al original
git checkout src/main/resources/db/migration/V3__create_products_table.sql
```

### 13.3 Escenario: inicializar Flyway en BD existente

```bash
# La BD ya tiene tablas creadas manualmente. Quiero empezar a usar Flyway.

# 1. Crear scripts SQL que describan el estado actual de la BD
# (No importa si duplican tablas existentes, no se ejecutarán)

# 2. Crear el baseline en la versión que corresponde al estado actual
flyway baseline -baselineVersion=1 -baselineDescription="Estado inicial"

# 3. A partir de ahora, Flyway solo ejecutará V2 en adelante
flyway info
```

---

## 14. Entornos múltiples y estrategias de equipo

### 14.1 Variables de entorno por perfil (Spring Boot)

```
src/main/resources/
├── application.yml              # Configuración base
├── application-dev.yml          # Desarrollo local
├── application-staging.yml      # Pre-producción
└── application-prod.yml         # Producción
```

```yaml
# application.yml (base)
spring:
  flyway:
    locations: classpath:db/migration
    validate-on-migrate: true
    clean-disabled: true
```

```yaml
# application-dev.yml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/myapp_dev
    username: postgres
    password: dev_pass
  flyway:
    clean-disabled: false      # Permitir 'clean' en dev
    out-of-order: true         # Aceptar migraciones fuera de orden en dev
```

```yaml
# application-prod.yml
spring:
  datasource:
    url: ${DB_URL}
    username: ${DB_USER}
    password: ${DB_PASSWORD}
  flyway:
    clean-disabled: true
    out-of-order: false
    validate-on-migrate: true
```

### 14.2 Flujo de trabajo en equipo con Git

```
main/develop
     │
     ├── feature/user-module  ──►  V3__create_users_table.sql
     │
     └── feature/product-module  ──►  V4__create_products_table.sql
```

**Problema**: dos ramas crean migraciones con la misma versión (`V3`).

**Soluciones**:

```
Solución 1: Prefijo con timestamp
  V20240115_001__create_users.sql
  V20240115_002__create_products.sql

Solución 2: Rangos por equipo
  Equipo Backend:   V100__, V101__, V102__...
  Equipo Frontend:  V200__, V201__, V202__...

Solución 3: Out-of-order + convención de nombres
  V3.1__create_users.sql    (rama A)
  V3.2__create_products.sql (rama B)
  Ambas se aplican con out-of-order=true
```

### 14.3 CI/CD con GitHub Actions

```yaml
# .github/workflows/migrate.yml
name: Database Migration

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  migrate:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_DB: myapp
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: secret
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - name: Checkout código
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 21

      - name: Ejecutar migraciones
        run: mvn flyway:migrate
        env:
          FLYWAY_URL: jdbc:postgresql://localhost:5432/myapp
          FLYWAY_USER: postgres
          FLYWAY_PASSWORD: secret

      - name: Validar migraciones
        run: mvn flyway:validate
        env:
          FLYWAY_URL: jdbc:postgresql://localhost:5432/myapp
          FLYWAY_USER: postgres
          FLYWAY_PASSWORD: secret
```

### 14.4 Migración en Kubernetes (Job)

```yaml
# k8s/flyway-job.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: flyway-migration
spec:
  template:
    spec:
      initContainers: []
      containers:
        - name: flyway
          image: flyway/flyway:10
          args: ["migrate"]
          env:
            - name: FLYWAY_URL
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: url
            - name: FLYWAY_USER
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: username
            - name: FLYWAY_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-secret
                  key: password
          volumeMounts:
            - name: migrations
              mountPath: /flyway/sql
      volumes:
        - name: migrations
          configMap:
            name: flyway-migrations
      restartPolicy: OnFailure
```

---

## 15. Buenas prácticas

### 15.1 Reglas fundamentales

```
✅ DO (SÍ hacer)                        ❌ DON'T (NO hacer)
───────────────────────────────────     ──────────────────────────────────
Nombrar scripts descriptivamente        Usar nombres genéricos (V1__fix.sql)
Hacer scripts idempotentes              Asumir estado inicial desconocido
Testear migraciones en entorno local    Probar por primera vez en producción
Usar transacciones cuando sea posible   Mezclar DDL y DML sin cuidado
Guardar los scripts en Git              Editar scripts ya aplicados
Activar cleanDisabled=true en prod      Ejecutar 'clean' en producción
Documentar cada migración con comentarios  Escribir SQL sin contexto
Un cambio lógico por script             Agrupar cambios no relacionados
```

### 15.2 Scripts idempotentes

```sql
-- ❌ Fallará si ya existe
CREATE TABLE users (id BIGSERIAL PRIMARY KEY);

-- ✅ Idempotente
CREATE TABLE IF NOT EXISTS users (id BIGSERIAL PRIMARY KEY);

-- ❌ Fallará si la columna ya existe
ALTER TABLE users ADD COLUMN email VARCHAR(255);

-- ✅ Idempotente (PostgreSQL)
DO $$
BEGIN
    IF NOT EXISTS (
        SELECT 1 FROM information_schema.columns
        WHERE table_name='users' AND column_name='email'
    ) THEN
        ALTER TABLE users ADD COLUMN email VARCHAR(255);
    END IF;
END $$;

-- ✅ Idempotente para índices
CREATE INDEX IF NOT EXISTS idx_users_email ON users(email);

-- ✅ Idempotente para datos
INSERT INTO roles (name) VALUES ('ADMIN')
    ON CONFLICT (name) DO NOTHING;
```

### 15.3 Estructura de scripts recomendada

```sql
-- V7__add_audit_columns.sql
-- ============================================================
-- Propósito: Añadir columnas de auditoría a la tabla 'orders'
-- Autor:     equipo-backend
-- Fecha:     2024-03-15
-- Jira:      PROJ-1234
-- ============================================================

-- Añadir columnas de auditoría
ALTER TABLE orders
    ADD COLUMN IF NOT EXISTS created_by VARCHAR(100),
    ADD COLUMN IF NOT EXISTS updated_by VARCHAR(100),
    ADD COLUMN IF NOT EXISTS updated_at TIMESTAMP;

-- Rellenar datos históricos
UPDATE orders
SET created_by = 'system',
    updated_by = 'system',
    updated_at = created_at
WHERE created_by IS NULL;

-- Aplicar constraint NOT NULL ahora que hay datos
ALTER TABLE orders
    ALTER COLUMN created_by SET NOT NULL,
    ALTER COLUMN updated_by SET NOT NULL;
```

### 15.4 No usar `spring.jpa.hibernate.ddl-auto=create`

```yaml
# ❌ PELIGROSO con Flyway: Hibernate recreará las tablas en cada inicio
spring:
  jpa:
    hibernate:
      ddl-auto: create

# ✅ CORRECTO: Hibernate solo valida que el esquema coincide con las entidades
spring:
  jpa:
    hibernate:
      ddl-auto: validate

# ✅ TAMBIÉN VÁLIDO: Hibernate no toca el esquema
spring:
  jpa:
    hibernate:
      ddl-auto: none
```

### 15.5 Nomenclatura con timestamps (para equipos grandes)

```
# Formato: V<YYYYMMDDHHMMSS>__descripcion.sql
V20240315143022__add_email_to_users.sql
V20240315150011__create_products_table.sql
V20240316090000__add_index_products_name.sql
```

Ventaja: imposible que dos desarrolladores generen la misma versión simultáneamente.

---

## 16. Solución de problemas comunes

### ❌ Error: `Migration checksum mismatch`

```
ERROR: Validate failed: Migrations have failed validation
Migration checksum mismatch for migration version 3
-> Applied to database : 1234567890
-> Resolved locally    : 9876543210
```

**Causa**: El contenido del script fue modificado después de aplicarse.

**Solución**:
```bash
# Opción 1: Revertir el script al original
git checkout -- src/main/resources/db/migration/V3__*.sql

# Opción 2: Reparar (solo en desarrollo)
flyway repair
# o
mvn flyway:repair
```

---

### ❌ Error: `Found more than one migration with version X`

```
ERROR: Found more than one migration with version 5
Offenders:
-> V5__add_users.sql (SQL)
-> V5__create_orders.sql (SQL)
```

**Causa**: Dos scripts tienen el mismo número de versión.

**Solución**: Renombrar uno de los scripts con una versión única.

---

### ❌ Error: `Unable to obtain Jdbc Connection`

```
ERROR: Unable to obtain Jdbc Connection
Caused by: Connection refused. Check that the hostname and port are correct
```

**Causa**: La base de datos no está disponible.

**Solución**:
```yaml
spring:
  flyway:
    connect-retries: 10          # Reintentar 10 veces
    connect-retries-interval: 2  # Con 2 segundos entre intentos
```

---

### ❌ Error: `FlywayException: Found non-empty schema(s) ... without schema history table!`

```
ERROR: Found non-empty schema(s) "public" without schema history table!
Use baseline() or set baselineOnMigrate to true to initialize the schema history table.
```

**Causa**: La base de datos ya tiene tablas pero no tiene la tabla de historial de Flyway.

**Solución**:
```yaml
# Opción 1: baseline automático al arrancar
spring:
  flyway:
    baseline-on-migrate: true
    baseline-version: 0
    baseline-description: "Esquema existente antes de Flyway"
```

```bash
# Opción 2: crear el baseline manualmente
flyway baseline -baselineVersion=1 -baselineDescription="Estado inicial"
```

---

### ❌ Error: `Validate failed: Detected resolved migration not applied to database`

```
ERROR: Validate failed: Detected resolved migration not applied to database: 3
```

**Causa**: Hay un script en disco que aún no está en el historial y `out-of-order=false`.

**Solución**:
```bash
# Aplicar la migración pendiente
flyway migrate

# O si es una migración antigua que no debe aplicarse:
spring.flyway.out-of-order=true    # Permite aplicarla
# o
spring.flyway.ignore-missing-migrations=true  # La ignora del todo
```

---

### ❌ Flyway se ejecuta pero las tablas no se crean (Spring Boot)

**Causa probable**: Los scripts están en la ruta incorrecta.

**Verificar**:
```
# Ruta correcta por defecto
src/main/resources/db/migration/V1__init.sql

# Si usas una ruta diferente, configúrala:
spring.flyway.locations=classpath:sql/migrations
```

---

### ❌ Error en MySQL: `ERROR 1406 (22001): Data too long for column`

**Causa**: En versiones antiguas de MySQL, el charset por defecto puede causar problemas con caracteres Unicode en los scripts.

**Solución**:
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb?useUnicode=true&characterEncoding=UTF-8
spring.flyway.encoding=UTF-8
```

---

## 17. Proyecto práctico completo

Construiremos una API REST de gestión de una tienda online con Spring Boot, PostgreSQL y Flyway.

### 17.1 Estructura del proyecto

```
tienda-api/
├── pom.xml
└── src/
    ├── main/
    │   ├── java/com/tienda/api/
    │   │   ├── TiendaApiApplication.java
    │   │   ├── config/
    │   │   │   └── FlywayConfig.java
    │   │   ├── controller/
    │   │   │   ├── ProductoController.java
    │   │   │   └── PedidoController.java
    │   │   ├── model/
    │   │   │   ├── Categoria.java
    │   │   │   ├── Producto.java
    │   │   │   └── Pedido.java
    │   │   └── repository/
    │   │       ├── ProductoRepository.java
    │   │       └── PedidoRepository.java
    │   └── resources/
    │       ├── application.yml
    │       └── db/migration/
    │           ├── V1__crear_tablas_base.sql
    │           ├── V2__crear_tabla_productos.sql
    │           ├── V3__crear_tabla_pedidos.sql
    │           ├── V4__añadir_indices.sql
    │           ├── V5__añadir_columnas_auditoria.sql
    │           └── R__seed_categorias.sql
    └── test/
        ├── java/com/tienda/api/
        │   └── TiendaApiApplicationTest.java
        └── resources/
            └── application-test.yml
```

### 17.2 `pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.3.0</version>
    </parent>

    <groupId>com.tienda</groupId>
    <artifactId>tienda-api</artifactId>
    <version>1.0.0</version>

    <properties>
        <java.version>21</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.flywaydb</groupId>
            <artifactId>flyway-core</artifactId>
        </dependency>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.flywaydb</groupId>
                <artifactId>flyway-maven-plugin</artifactId>
                <version>10.15.0</version>
                <configuration>
                    <url>${env.DB_URL}</url>
                    <user>${env.DB_USER}</user>
                    <password>${env.DB_PASSWORD}</password>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>org.postgresql</groupId>
                        <artifactId>postgresql</artifactId>
                        <version>42.7.3</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
</project>
```

### 17.3 `application.yml`

```yaml
# src/main/resources/application.yml
spring:
  datasource:
    url: ${DB_URL:jdbc:postgresql://localhost:5432/tienda}
    username: ${DB_USER:postgres}
    password: ${DB_PASSWORD:secret}

  jpa:
    hibernate:
      ddl-auto: validate
    show-sql: false
    properties:
      hibernate:
        format_sql: true
        dialect: org.hibernate.dialect.PostgreSQLDialect

  flyway:
    enabled: true
    locations: classpath:db/migration
    validate-on-migrate: true
    clean-disabled: true
    connect-retries: 5

server:
  port: 8080
```

### 17.4 Scripts de migración

#### `V1__crear_tablas_base.sql`

```sql
-- V1__crear_tablas_base.sql
-- Tablas fundamentales: categorías y usuarios

CREATE TABLE categorias (
    id          SERIAL PRIMARY KEY,
    nombre      VARCHAR(100) NOT NULL UNIQUE,
    descripcion TEXT,
    activa      BOOLEAN NOT NULL DEFAULT TRUE,
    created_at  TIMESTAMP NOT NULL DEFAULT NOW()
);

CREATE TABLE usuarios (
    id         BIGSERIAL PRIMARY KEY,
    email      VARCHAR(255) NOT NULL UNIQUE,
    nombre     VARCHAR(200) NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

#### `V2__crear_tabla_productos.sql`

```sql
-- V2__crear_tabla_productos.sql
-- Tabla de productos con referencia a categorías

CREATE TABLE productos (
    id           BIGSERIAL PRIMARY KEY,
    nombre       VARCHAR(200) NOT NULL,
    descripcion  TEXT,
    precio       NUMERIC(10, 2) NOT NULL CHECK (precio >= 0),
    stock        INT NOT NULL DEFAULT 0 CHECK (stock >= 0),
    categoria_id INT NOT NULL REFERENCES categorias(id) ON DELETE RESTRICT,
    activo       BOOLEAN NOT NULL DEFAULT TRUE,
    created_at   TIMESTAMP NOT NULL DEFAULT NOW(),
    updated_at   TIMESTAMP NOT NULL DEFAULT NOW()
);
```

#### `V3__crear_tabla_pedidos.sql`

```sql
-- V3__crear_tabla_pedidos.sql
-- Tablas de pedidos y líneas de pedido

CREATE TYPE estado_pedido AS ENUM ('PENDIENTE', 'CONFIRMADO', 'ENVIADO', 'ENTREGADO', 'CANCELADO');

CREATE TABLE pedidos (
    id           BIGSERIAL PRIMARY KEY,
    usuario_id   BIGINT NOT NULL REFERENCES usuarios(id) ON DELETE RESTRICT,
    estado       estado_pedido NOT NULL DEFAULT 'PENDIENTE',
    total        NUMERIC(12, 2) NOT NULL DEFAULT 0,
    created_at   TIMESTAMP NOT NULL DEFAULT NOW(),
    updated_at   TIMESTAMP NOT NULL DEFAULT NOW()
);

CREATE TABLE lineas_pedido (
    id          BIGSERIAL PRIMARY KEY,
    pedido_id   BIGINT NOT NULL REFERENCES pedidos(id) ON DELETE CASCADE,
    producto_id BIGINT NOT NULL REFERENCES productos(id) ON DELETE RESTRICT,
    cantidad    INT NOT NULL CHECK (cantidad > 0),
    precio_unit NUMERIC(10, 2) NOT NULL CHECK (precio_unit >= 0)
);
```

#### `V4__añadir_indices.sql`

```sql
-- V4__añadir_indices.sql
-- Índices para mejorar el rendimiento de consultas frecuentes

CREATE INDEX idx_productos_categoria ON productos(categoria_id);
CREATE INDEX idx_productos_activo ON productos(activo) WHERE activo = TRUE;
CREATE INDEX idx_pedidos_usuario ON pedidos(usuario_id);
CREATE INDEX idx_pedidos_estado ON pedidos(estado);
CREATE INDEX idx_lineas_pedido_pedido ON lineas_pedido(pedido_id);
CREATE INDEX idx_lineas_pedido_producto ON lineas_pedido(producto_id);
```

#### `V5__añadir_columnas_auditoria.sql`

```sql
-- V5__añadir_columnas_auditoria.sql
-- Columnas de auditoría para rastrear cambios

ALTER TABLE productos
    ADD COLUMN IF NOT EXISTS updated_by VARCHAR(100);

ALTER TABLE pedidos
    ADD COLUMN IF NOT EXISTS updated_by VARCHAR(100);

-- Función para actualizar 'updated_at' automáticamente
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Triggers en las tablas que tienen 'updated_at'
CREATE TRIGGER trg_productos_updated_at
    BEFORE UPDATE ON productos
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();

CREATE TRIGGER trg_pedidos_updated_at
    BEFORE UPDATE ON pedidos
    FOR EACH ROW EXECUTE FUNCTION update_updated_at_column();
```

#### `R__seed_categorias.sql`

```sql
-- R__seed_categorias.sql
-- Datos de referencia para categorías (se re-ejecuta si cambia)

INSERT INTO categorias (nombre, descripcion) VALUES
    ('Electrónica',   'Dispositivos y gadgets electrónicos'),
    ('Ropa',          'Moda y complementos'),
    ('Hogar',         'Artículos para el hogar'),
    ('Deportes',      'Artículos deportivos y fitness'),
    ('Libros',        'Libros, e-books y material educativo')
ON CONFLICT (nombre) DO UPDATE
    SET descripcion = EXCLUDED.descripcion;
```

### 17.5 Entidades JPA

```java
// Categoria.java
@Entity
@Table(name = "categorias")
public class Categoria {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    @Column(nullable = false, unique = true, length = 100)
    private String nombre;

    private String descripcion;

    @Column(nullable = false)
    private Boolean activa = true;

    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt = LocalDateTime.now();

    // getters y setters...
}
```

```java
// Producto.java
@Entity
@Table(name = "productos")
public class Producto {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 200)
    private String nombre;

    private String descripcion;

    @Column(nullable = false, precision = 10, scale = 2)
    private BigDecimal precio;

    @Column(nullable = false)
    private Integer stock = 0;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "categoria_id", nullable = false)
    private Categoria categoria;

    @Column(nullable = false)
    private Boolean activo = true;

    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt = LocalDateTime.now();

    @Column(name = "updated_at", nullable = false)
    private LocalDateTime updatedAt = LocalDateTime.now();

    // getters y setters...
}
```

### 17.6 Tests de integración con Flyway

```java
// TiendaApiApplicationTest.java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@ActiveProfiles("test")
class TiendaApiApplicationTest {

    @Autowired
    private ProductoRepository productoRepository;

    @Autowired
    private CategoriaRepository categoriaRepository;

    @Test
    void contextLoads() {
        // Flyway aplicó las migraciones correctamente si llega aquí
    }

    @Test
    void deberiaCargarCategoriasDeReferencia() {
        // La migración R__seed_categorias.sql debe haber insertado datos
        List<Categoria> categorias = categoriaRepository.findAll();
        assertThat(categorias).hasSizeGreaterThanOrEqualTo(5);
        assertThat(categorias)
            .extracting(Categoria::getNombre)
            .contains("Electrónica", "Ropa", "Hogar");
    }

    @Test
    void deberiaCrearYRecuperarProducto() {
        Categoria electronica = categoriaRepository.findByNombre("Electrónica")
            .orElseThrow();

        Producto producto = new Producto();
        producto.setNombre("Laptop Gaming");
        producto.setPrecio(new BigDecimal("999.99"));
        producto.setStock(10);
        producto.setCategoria(electronica);

        Producto guardado = productoRepository.save(producto);

        assertThat(guardado.getId()).isNotNull();
        assertThat(guardado.getNombre()).isEqualTo("Laptop Gaming");
    }
}
```

```yaml
# src/test/resources/application-test.yml
spring:
  datasource:
    url: jdbc:h2:mem:tienda_test;DB_CLOSE_DELAY=-1;MODE=PostgreSQL;DEFAULT_NULL_ORDERING=HIGH
    driver-class-name: org.h2.Driver
    username: sa
    password:

  jpa:
    hibernate:
      ddl-auto: none
    database-platform: org.hibernate.dialect.H2Dialect

  flyway:
    enabled: true
    locations: classpath:db/migration
    clean-on-validation-error: true
```

### 17.7 Arrancar el proyecto

```bash
# 1. Levantar PostgreSQL con Docker
docker run -d \
  --name tienda-postgres \
  -e POSTGRES_DB=tienda \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=secret \
  -p 5432:5432 \
  postgres:16

# 2. Compilar y ejecutar (Flyway se ejecuta automáticamente al arrancar)
mvn spring-boot:run

# Salida esperada:
# Flyway Community Edition 10.x.x
# Database: jdbc:postgresql://localhost:5432/tienda (PostgreSQL 16.0)
# Successfully applied 5 migrations to schema "public", now at version v5
# Started TiendaApiApplication in 3.456 seconds

# 3. Verificar el estado de las migraciones
mvn flyway:info

# 4. Ejecutar tests
mvn test
```

---

## 📊 Comparativa: Flyway vs Liquibase

| Característica | Flyway | Liquibase |
|----------------|--------|-----------|
| **Formato de migraciones** | SQL, Java | SQL, XML, YAML, JSON |
| **Curva de aprendizaje** | ⭐ Muy baja | ⭐⭐ Media |
| **Rollback** | Manual (scripts U) / Teams | Automático (changeSet con rollback) |
| **Integración Spring Boot** | ✅ Nativa y simple | ✅ Nativa |
| **Rendimiento** | Muy rápido | Ligeramente más lento |
| **Comunidad** | Grande | Grande |
| **Licencia open source** | Apache 2.0 | Apache 2.0 |
| **Funciones avanzadas** | Flyway Teams (pago) | Liquibase Pro (pago) |
| **Ideal para** | Equipos que prefieren SQL puro | Equipos que quieren más abstracción |

---

## 🔗 Recursos adicionales

| Recurso | Enlace |
|---------|--------|
| 📖 Documentación oficial | https://documentation.red-gate.com/flyway |
| 🐙 GitHub de Flyway | https://github.com/flyway/flyway |
| 🏠 Sitio oficial | https://flywaydb.org |
| 💬 Slack de la comunidad | https://flywaydb.org/community |
| 🐋 Imagen Docker oficial | https://hub.docker.com/r/flyway/flyway |
| 📦 Maven Central | https://mvnrepository.com/artifact/org.flywaydb/flyway-core |

---

> 💡 **Consejo final**: Empieza usando Flyway desde el primer día de tu proyecto, no cuando ya tengas 50 tablas creadas a mano. Incorporarlo desde el inicio es trivial y te ahorrará muchos dolores de cabeza cuando trabajes en equipo o necesites reproducir entornos de forma consistente.
