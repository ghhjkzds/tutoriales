# 🕸️ Web Crawling y Crawlers en Java - Tutorial Completo

## Introducción

Un **Web Crawler** (también llamado _spider_ o _robot_) es un programa que navega automáticamente por la web siguiendo enlaces de manera sistemática. A diferencia del scraping (que extrae datos de páginas específicas), el crawling **descubre** páginas siguiendo hipervínculos, construyendo un mapa o índice del contenido web.

Los motores de búsqueda como Google, Bing y DuckDuckGo utilizan crawlers masivos para indexar la web. En este tutorial aprenderás a construir crawlers desde cero, con Java y Spring Boot, desde los casos más simples hasta arquitecturas distribuidas.

> ⚠️ **Nota Legal y Ética**: Respeta siempre el archivo `robots.txt` de cada sitio y sus Términos de Servicio. Los crawlers mal configurados pueden sobrecargar servidores y causar daños. Usa delays apropiados y limita la profundidad/amplitud de tu crawl.

---

## 📋 Tabla de Contenidos

1. [Conceptos Fundamentales del Crawling](#1-conceptos-fundamentales-del-crawling)
2. [Configuración del Entorno](#2-configuración-del-entorno)
3. [Nivel Básico: Tu Primer Crawler](#3-nivel-básico-tu-primer-crawler)
4. [Nivel Intermedio: Crawler con Spring Boot](#4-nivel-intermedio-crawler-con-spring-boot)
5. [Respetar robots.txt](#5-respetar-robotstxt)
6. [Gestión de la Frontera (Frontier)](#6-gestión-de-la-frontera-frontier)
7. [Almacenamiento y Persistencia](#7-almacenamiento-y-persistencia)
8. [Crawler Multihilo y Concurrente](#8-crawler-multihilo-y-concurrente)
9. [Crawler para Páginas Dinámicas (JavaScript)](#9-crawler-para-páginas-dinámicas-javascript)
10. [Indexación y Búsqueda con Apache Lucene](#10-indexación-y-búsqueda-con-apache-lucene)
11. [Arquitectura de Crawler Distribuido](#11-arquitectura-de-crawler-distribuido)
12. [Proyecto Completo: Motor de Búsqueda Básico](#12-proyecto-completo-motor-de-búsqueda-básico)
13. [Ejercicios Resueltos](#13-ejercicios-resueltos)
14. [Diferencias entre Crawlers y Scrapers: Cuándo usar cada uno](#14-diferencias-entre-crawlers-y-scrapers-cuándo-usar-cada-uno)
15. [Recursos Adicionales](#15-recursos-adicionales)

---

## 1. Conceptos Fundamentales del Crawling

### ¿Cómo funciona un Web Crawler?

El proceso básico de un crawler es iterativo:

```
┌─────────────────────────────────────────────────────────────────┐
│                    CICLO DE CRAWLING                            │
│                                                                 │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐  │
│  │  Frontier │───►│ Selector │───►│Descargador│───►│  Parser  │  │
│  │ (Cola de │    │ (elige   │    │(HTTP GET) │    │(extrae   │  │
│  │  URLs)   │    │siguiente │    │           │    │enlaces y │  │
│  └──────────┘    │  URL)    │    └──────────┘    │ datos)   │  │
│       ▲          └──────────┘                    └──────────┘  │
│       │                                               │         │
│       │         ┌──────────┐    ┌──────────┐         │         │
│       └─────────┤ Filtros  │◄───┤ URLs     │◄────────┘         │
│                 │(robots,  │    │nuevas    │                    │
│                 │duplicados│    │encontradas│                   │
│                 │dominio)  │    └──────────┘                   │
│                 └──────────┘                                    │
└─────────────────────────────────────────────────────────────────┘
```

### Componentes de un Crawler

| Componente | Descripción |
|---|---|
| **Frontier (Cola)** | Almacena las URLs pendientes por visitar |
| **Descargador** | Obtiene el contenido HTML de las URLs |
| **Parser** | Analiza el HTML y extrae enlaces nuevos |
| **Filtros** | Deciden qué URLs procesar (robots.txt, duplicados, dominio) |
| **Almacenamiento** | Guarda las páginas y datos extraídos |
| **Indexador** | Organiza el contenido para búsquedas posteriores |

### Estrategias de Traversal (Recorrido)

#### BFS - Breadth-First Search (Amplitud)
```
Nivel 0:        URL_inicial
                /     |     \
Nivel 1:    URL_A    URL_B   URL_C    ← Se visitan primero
            / \      / \
Nivel 2: URL_D URL_E URL_F URL_G    ← Después se visitan estas
```
- ✅ Ideal para capturar la estructura general de un sitio
- ✅ Los enlaces importantes suelen estar más cerca de la raíz
- ❌ Puede consumir mucha memoria con la cola

#### DFS - Depth-First Search (Profundidad)
```
URL_inicial
    └── URL_A
         └── URL_D
              └── URL_H  ← Se llega muy profundo antes de explorar a lo ancho
```
- ✅ Consume menos memoria (stack vs. queue)
- ❌ Puede quedarse atascado en partes profundas del sitio
- ❌ No garantiza encontrar el contenido más relevante primero

#### Best-First / Priority-Based
- Asigna prioridad a las URLs según relevancia, PageRank estimado, etc.
- Usado por motores de búsqueda modernos

### Conceptos Clave

**URL Fingerprinting**: Técnica para detectar páginas duplicadas comparando hashes del contenido (MD5, SHA-256) en lugar del texto completo.

**Politeness Policy**: Conjunto de reglas que limitan cuánto puede "molestar" el crawler a un servidor:
- Respeto al `robots.txt`
- Delay mínimo entre peticiones al mismo dominio
- Límite de peticiones simultáneas por dominio

**Canonical URLs**: Normalización de URLs para evitar visitar la misma página con diferentes URLs:
```
https://ejemplo.com/pagina
https://ejemplo.com/pagina/      ← misma página
https://ejemplo.com/pagina?ref=1 ← posiblemente la misma
HTTP://EJEMPLO.COM/PAGINA        ← misma página
```

---

## 2. Configuración del Entorno

### Dependencias Maven (`pom.xml`)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
    </parent>

    <groupId>com.tutorial</groupId>
    <artifactId>crawler-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>crawler-demo</name>

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencies>
        <!-- Spring Boot Web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Spring Data JPA -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!-- Base de datos H2 (desarrollo) -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!-- Jsoup para HTML parsing -->
        <dependency>
            <groupId>org.jsoup</groupId>
            <artifactId>jsoup</artifactId>
            <version>1.17.2</version>
        </dependency>

        <!-- Apache Lucene para indexación -->
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-core</artifactId>
            <version>9.9.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-queryparser</artifactId>
            <version>9.9.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.lucene</groupId>
            <artifactId>lucene-analysis-common</artifactId>
            <version>9.9.1</version>
        </dependency>

        <!-- Crawl4j - Framework de crawling para Java -->
        <dependency>
            <groupId>edu.uci.ics</groupId>
            <artifactId>crawler4j</artifactId>
            <version>4.4.0</version>
        </dependency>

        <!-- Selenium (para páginas dinámicas) -->
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>4.16.1</version>
        </dependency>

        <!-- WebDriverManager -->
        <dependency>
            <groupId>io.github.bonigarcia</groupId>
            <artifactId>webdrivermanager</artifactId>
            <version>5.6.3</version>
        </dependency>

        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <!-- Jackson -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>

        <!-- Tests -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

### Estructura del Proyecto

```
crawler-demo/
├── src/main/java/com/tutorial/crawler/
│   ├── CrawlerDemoApplication.java
│   ├── config/
│   │   └── CrawlerConfig.java
│   ├── model/
│   │   ├── PaginaWeb.java
│   │   └── EstadoCrawl.java
│   ├── frontier/
│   │   ├── Frontier.java
│   │   └── PriorityFrontier.java
│   ├── filter/
│   │   ├── UrlFilter.java
│   │   └── RobotsFilter.java
│   ├── downloader/
│   │   └── PageDownloader.java
│   ├── parser/
│   │   └── HtmlParser.java
│   ├── service/
│   │   ├── CrawlerService.java
│   │   └── IndexService.java
│   ├── repository/
│   │   └── PaginaWebRepository.java
│   └── controller/
│       └── CrawlerController.java
└── src/main/resources/
    └── application.properties
```

### `application.properties`

```properties
# Servidor
server.port=8080

# Base de datos
spring.datasource.url=jdbc:h2:file:./crawlerdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=update
spring.h2.console.enabled=true

# Configuración del crawler
crawler.seed-urls=https://books.toscrape.com/
crawler.max-depth=3
crawler.max-pages=100
crawler.delay-ms=500
crawler.max-concurrent-threads=4
crawler.allowed-domains=books.toscrape.com
crawler.user-agent=MiCrawler/1.0 (contacto: demo@tutorial.com)
crawler.respect-robots-txt=true

# Lucene index
lucene.index-dir=./lucene-index
```

---

## 3. Nivel Básico: Tu Primer Crawler

### 3.1 Crawler más simple posible

```java
package com.tutorial.crawler.basico;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import java.io.IOException;
import java.util.*;

/**
 * Crawler básico: BFS simple sin persistencia.
 * Solo para entender el concepto fundamental.
 */
public class CrawlerSimple {

    private static final int MAX_PAGINAS = 20;
    private static final long DELAY_MS = 500;

    public static void main(String[] args) throws InterruptedException {
        String urlInicial = "https://books.toscrape.com/";
        crawlear(urlInicial);
    }

    public static void crawlear(String urlInicial) throws InterruptedException {
        // Cola de URLs por visitar (Frontier - BFS)
        Queue<String> porVisitar = new LinkedList<>();
        // Conjunto de URLs ya visitadas
        Set<String> visitadas = new HashSet<>();

        // Seed: URL inicial
        porVisitar.add(urlInicial);

        int contador = 0;

        while (!porVisitar.isEmpty() && contador < MAX_PAGINAS) {
            String urlActual = porVisitar.poll();

            // Evitar duplicados
            if (visitadas.contains(urlActual)) {
                continue;
            }

            try {
                System.out.printf("[%d/%d] Visitando: %s%n",
                    contador + 1, MAX_PAGINAS, urlActual);

                // Descargar y parsear la página
                Document doc = Jsoup.connect(urlActual)
                        .userAgent("MiCrawler/1.0")
                        .timeout(10000)
                        .get();

                visitadas.add(urlActual);
                contador++;

                // Extraer y mostrar datos básicos
                System.out.println("  Título: " + doc.title());
                System.out.println("  Palabras: " + doc.text().split("\\s+").length);

                // Descubrir nuevos enlaces del mismo dominio
                Elements enlaces = doc.select("a[href]");
                int nuevosEnlaces = 0;
                for (Element enlace : enlaces) {
                    String href = enlace.attr("abs:href"); // URL absoluta
                    if (esMismoSitio(href, urlInicial) && !visitadas.contains(href)) {
                        porVisitar.add(href);
                        nuevosEnlaces++;
                    }
                }
                System.out.println("  Nuevos enlaces descubiertos: " + nuevosEnlaces);

                Thread.sleep(DELAY_MS); // Política de cortesía

            } catch (IOException e) {
                System.err.println("  ❌ Error: " + e.getMessage());
                visitadas.add(urlActual); // Marcar como visitada para no reintentar
            }
        }

        System.out.printf("%nCrawling completado. Páginas visitadas: %d%n", visitadas.size());
    }

    /**
     * Verifica si una URL pertenece al mismo sitio que la URL base
     */
    private static boolean esMismoSitio(String url, String urlBase) {
        if (url == null || url.isEmpty()) return false;
        try {
            String hostBase = new java.net.URI(urlBase).getHost();
            String hostUrl = new java.net.URI(url).getHost();
            return hostBase != null && hostBase.equals(hostUrl);
        } catch (Exception e) {
            return false;
        }
    }
}
```

### 3.2 Normalización de URLs

Un crawler real necesita normalizar URLs para evitar visitar la misma página dos veces con URLs ligeramente distintas:

```java
package com.tutorial.crawler.util;

import java.net.URI;
import java.net.URISyntaxException;
import java.util.Arrays;
import java.util.List;
import java.util.TreeMap;

public class UrlNormalizer {

    // Parámetros de query a ignorar (tracking, analytics, etc.)
    private static final List<String> PARAMS_IGNORAR = Arrays.asList(
        "utm_source", "utm_medium", "utm_campaign", "utm_content", "utm_term",
        "ref", "source", "fbclid", "gclid", "sessionid", "PHPSESSID"
    );

    /**
     * Normaliza una URL para evitar duplicados
     */
    public static String normalizar(String urlRaw) {
        if (urlRaw == null || urlRaw.isBlank()) return null;

        try {
            URI uri = new URI(urlRaw).normalize();

            // 1. Forzar minúsculas en esquema y host
            String esquema = uri.getScheme() != null ? uri.getScheme().toLowerCase() : "https";
            String host = uri.getHost() != null ? uri.getHost().toLowerCase() : "";

            // 2. Eliminar www. innecesario (opcional)
            // host = host.replaceFirst("^www\\.", "");

            // 3. Eliminar puerto por defecto
            int puerto = uri.getPort();
            if ((esquema.equals("http") && puerto == 80) ||
                (esquema.equals("https") && puerto == 443)) {
                puerto = -1;
            }

            // 4. Eliminar fragmento (#sección)
            String fragmento = null; // Siempre ignoramos el fragmento

            // 5. Eliminar parámetros de tracking
            String query = filtrarQueryParams(uri.getQuery());

            // 6. Eliminar trailing slash del path (excepto si es solo "/")
            String path = uri.getPath();
            if (path != null && path.length() > 1 && path.endsWith("/")) {
                path = path.substring(0, path.length() - 1);
            }

            return new URI(esquema, null, host, puerto, path, query, fragmento).toString();

        } catch (URISyntaxException e) {
            return urlRaw; // Retornar original si no se puede parsear
        }
    }

    private static String filtrarQueryParams(String query) {
        if (query == null || query.isEmpty()) return null;

        // Ordenar y filtrar parámetros
        TreeMap<String, String> params = new TreeMap<>();
        for (String par : query.split("&")) {
            String[] partes = par.split("=", 2);
            String clave = partes[0];
            if (!PARAMS_IGNORAR.contains(clave)) {
                params.put(clave, partes.length > 1 ? partes[1] : "");
            }
        }

        if (params.isEmpty()) return null;

        StringBuilder sb = new StringBuilder();
        params.forEach((k, v) -> {
            if (sb.length() > 0) sb.append("&");
            sb.append(k);
            if (!v.isEmpty()) sb.append("=").append(v);
        });
        return sb.toString();
    }

    /**
     * Extrae el dominio base de una URL
     */
    public static String extraerDominio(String url) {
        try {
            return new URI(url).getHost();
        } catch (URISyntaxException e) {
            return null;
        }
    }

    /**
     * Verifica si una URL es válida para crawlear
     */
    public static boolean esUrlValida(String url) {
        if (url == null || url.isBlank()) return false;
        if (!url.startsWith("http://") && !url.startsWith("https://")) return false;
        // Ignorar recursos no HTML
        String urlLower = url.toLowerCase();
        return !urlLower.matches(".*\\.(jpg|jpeg|png|gif|pdf|zip|mp4|mp3|css|js|ico|svg|woff2?)$");
    }
}
```

---

## 4. Nivel Intermedio: Crawler con Spring Boot

### 4.1 Modelo de Datos

```java
package com.tutorial.crawler.model;

import jakarta.persistence.*;
import java.time.LocalDateTime;
import java.util.HashSet;
import java.util.Set;

@Entity
@Table(name = "paginas_web",
       indexes = {
           @Index(name = "idx_url", columnList = "url", unique = true),
           @Index(name = "idx_dominio", columnList = "dominio"),
           @Index(name = "idx_estado", columnList = "estado")
       })
public class PaginaWeb {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true, length = 2048)
    private String url;

    private String titulo;

    @Column(length = 500)
    private String descripcion;

    private String dominio;

    private int profundidad;  // Nivel de profundidad desde el seed

    @Enumerated(EnumType.STRING)
    private EstadoCrawl estado = EstadoCrawl.PENDIENTE;

    @Column(name = "fecha_descubierta")
    private LocalDateTime fechaDescubierta = LocalDateTime.now();

    @Column(name = "fecha_visitada")
    private LocalDateTime fechaVisitada;

    @Column(name = "codigo_http")
    private int codigoHttp;

    @Column(name = "num_enlaces_salientes")
    private int numEnlacesSalientes;

    @Column(name = "hash_contenido")
    private String hashContenido;  // Para detectar duplicados por contenido

    @Column(columnDefinition = "TEXT")
    private String textoExtraido;  // Texto limpio de la página

    // Getters y Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getUrl() { return url; }
    public void setUrl(String url) { this.url = url; }

    public String getTitulo() { return titulo; }
    public void setTitulo(String titulo) { this.titulo = titulo; }

    public String getDescripcion() { return descripcion; }
    public void setDescripcion(String descripcion) { this.descripcion = descripcion; }

    public String getDominio() { return dominio; }
    public void setDominio(String dominio) { this.dominio = dominio; }

    public int getProfundidad() { return profundidad; }
    public void setProfundidad(int profundidad) { this.profundidad = profundidad; }

    public EstadoCrawl getEstado() { return estado; }
    public void setEstado(EstadoCrawl estado) { this.estado = estado; }

    public LocalDateTime getFechaDescubierta() { return fechaDescubierta; }
    public void setFechaDescubierta(LocalDateTime f) { this.fechaDescubierta = f; }

    public LocalDateTime getFechaVisitada() { return fechaVisitada; }
    public void setFechaVisitada(LocalDateTime f) { this.fechaVisitada = f; }

    public int getCodigoHttp() { return codigoHttp; }
    public void setCodigoHttp(int codigoHttp) { this.codigoHttp = codigoHttp; }

    public int getNumEnlacesSalientes() { return numEnlacesSalientes; }
    public void setNumEnlacesSalientes(int n) { this.numEnlacesSalientes = n; }

    public String getHashContenido() { return hashContenido; }
    public void setHashContenido(String hash) { this.hashContenido = hash; }

    public String getTextoExtraido() { return textoExtraido; }
    public void setTextoExtraido(String texto) { this.textoExtraido = texto; }

    @Override
    public String toString() {
        return "PaginaWeb{id=%d, url='%s', estado=%s, profundidad=%d}"
            .formatted(id, url, estado, profundidad);
    }
}
```

### 4.2 Enumeración de Estado

```java
package com.tutorial.crawler.model;

public enum EstadoCrawl {
    PENDIENTE,       // Descubierta, esperando ser visitada
    EN_PROGRESO,     // Siendo descargada/procesada
    VISITADA,        // Procesada exitosamente
    ERROR,           // Error al descargar (HTTP 4xx/5xx, timeout)
    EXCLUIDA,        // Excluida por robots.txt u otras reglas
    DUPLICADA        // Contenido duplicado de otra página
}
```

### 4.3 Repositorio JPA

```java
package com.tutorial.crawler.repository;

import com.tutorial.crawler.model.EstadoCrawl;
import com.tutorial.crawler.model.PaginaWeb;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;
import java.util.List;
import java.util.Optional;

@Repository
public interface PaginaWebRepository extends JpaRepository<PaginaWeb, Long> {

    boolean existsByUrl(String url);

    Optional<PaginaWeb> findByUrl(String url);

    List<PaginaWeb> findByEstado(EstadoCrawl estado);

    @Query("SELECT p FROM PaginaWeb p WHERE p.estado = :estado ORDER BY p.fechaDescubierta ASC")
    Page<PaginaWeb> findPendientes(@Param("estado") EstadoCrawl estado, Pageable pageable);

    long countByEstado(EstadoCrawl estado);

    long countByDominio(String dominio);

    @Modifying
    @Query("UPDATE PaginaWeb p SET p.estado = :estado WHERE p.id = :id")
    void actualizarEstado(@Param("id") Long id, @Param("estado") EstadoCrawl estado);

    @Query("SELECT p.dominio, COUNT(p) as total FROM PaginaWeb p GROUP BY p.dominio ORDER BY total DESC")
    List<Object[]> estadisticasPorDominio();

    boolean existsByHashContenido(String hash);
}
```

### 4.4 Frontier (Cola de URLs)

```java
package com.tutorial.crawler.frontier;

import com.tutorial.crawler.model.EstadoCrawl;
import com.tutorial.crawler.model.PaginaWeb;
import com.tutorial.crawler.repository.PaginaWebRepository;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.data.domain.PageRequest;
import org.springframework.stereotype.Component;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;
import java.util.Optional;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

/**
 * Frontier: Gestiona la cola de URLs a visitar.
 * Combina una cola en memoria (para rendimiento) con persistencia en BD.
 */
@Component
public class Frontier {

    private static final Logger log = LoggerFactory.getLogger(Frontier.class);
    private static final int BUFFER_SIZE = 100;

    private final PaginaWebRepository repository;
    private final BlockingQueue<PaginaWeb> colaEnMemoria = new LinkedBlockingQueue<>(BUFFER_SIZE);

    public Frontier(PaginaWebRepository repository) {
        this.repository = repository;
    }

    /**
     * Agrega una URL a la frontera (si no está ya registrada)
     */
    @Transactional
    public boolean agregar(String url, int profundidad) {
        if (repository.existsByUrl(url)) {
            return false; // Ya conocida
        }

        PaginaWeb pagina = new PaginaWeb();
        pagina.setUrl(url);
        pagina.setProfundidad(profundidad);
        pagina.setEstado(EstadoCrawl.PENDIENTE);

        // Extraer dominio
        try {
            pagina.setDominio(new java.net.URI(url).getHost());
        } catch (Exception e) {
            pagina.setDominio("desconocido");
        }

        PaginaWeb guardada = repository.save(pagina);

        // Intentar añadir al buffer en memoria
        colaEnMemoria.offer(guardada);

        return true;
    }

    /**
     * Obtiene la siguiente URL a procesar
     */
    @Transactional
    public Optional<PaginaWeb> obtenerSiguiente() {
        // Intentar del buffer en memoria primero
        PaginaWeb pagina = colaEnMemoria.poll();

        if (pagina == null) {
            // Si el buffer está vacío, cargar más desde BD
            List<PaginaWeb> pendientes = repository.findPendientes(
                EstadoCrawl.PENDIENTE,
                PageRequest.of(0, BUFFER_SIZE)
            ).getContent();

            if (pendientes.isEmpty()) {
                return Optional.empty();
            }

            pagina = pendientes.get(0);
            colaEnMemoria.addAll(pendientes.subList(1, pendientes.size()));
        }

        // Marcar como en progreso
        pagina.setEstado(EstadoCrawl.EN_PROGRESO);
        repository.save(pagina);

        return Optional.of(pagina);
    }

    public boolean estaVacia() {
        return colaEnMemoria.isEmpty() &&
               repository.countByEstado(EstadoCrawl.PENDIENTE) == 0;
    }

    public long tamano() {
        return repository.countByEstado(EstadoCrawl.PENDIENTE);
    }
}
```

---

## 5. Respetar robots.txt

### 5.1 Parser de robots.txt

```java
package com.tutorial.crawler.filter;

import org.jsoup.Jsoup;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import java.io.IOException;
import java.net.URI;
import java.util.*;
import java.util.concurrent.ConcurrentHashMap;

/**
 * Parsea y respeta el archivo robots.txt de cada dominio.
 */
@Component
public class RobotsFilter {

    private static final Logger log = LoggerFactory.getLogger(RobotsFilter.class);

    // Caché de reglas robots.txt por dominio
    private final Map<String, List<String>> reglasExclusion = new ConcurrentHashMap<>();
    private final Map<String, Long> crawlDelays = new ConcurrentHashMap<>();

    /**
     * Verifica si una URL está permitida para crawlear
     */
    public boolean estaPermitida(String url, String userAgent) {
        try {
            String dominio = obtenerDominio(url);
            if (dominio == null) return false;

            // Cargar robots.txt si no está en caché
            if (!reglasExclusion.containsKey(dominio)) {
                cargarRobotsTxt(dominio, userAgent);
            }

            List<String> reglas = reglasExclusion.getOrDefault(dominio, Collections.emptyList());
            String path = new URI(url).getPath();
            if (path == null) path = "/";

            // Verificar si el path coincide con alguna regla Disallow
            for (String regla : reglas) {
                if (path.startsWith(regla)) {
                    log.debug("URL excluida por robots.txt: {}", url);
                    return false;
                }
            }

            return true;

        } catch (Exception e) {
            log.warn("Error al verificar robots.txt para {}: {}", url, e.getMessage());
            return true; // En caso de error, permitir (conservador)
        }
    }

    /**
     * Obtiene el crawl-delay especificado para un dominio (en segundos)
     */
    public long getCrawlDelay(String dominio) {
        return crawlDelays.getOrDefault(dominio, 1L); // Por defecto 1 segundo
    }

    private void cargarRobotsTxt(String dominio, String userAgent) {
        String robotsUrl = "https://" + dominio + "/robots.txt";
        List<String> exclusiones = new ArrayList<>();

        try {
            String contenido = Jsoup.connect(robotsUrl)
                    .userAgent(userAgent)
                    .timeout(5000)
                    .ignoreContentType(true)
                    .execute()
                    .body();

            parsearRobotsTxt(contenido, userAgent, exclusiones, dominio);
            log.info("robots.txt cargado para {}: {} reglas", dominio, exclusiones.size());

        } catch (IOException e) {
            log.debug("No se encontró robots.txt para {}: {}", dominio, e.getMessage());
        }

        reglasExclusion.put(dominio, exclusiones);
    }

    private void parsearRobotsTxt(String contenido, String miUserAgent,
                                   List<String> exclusiones, String dominio) {
        boolean aplicaAMi = false;
        String[] lineas = contenido.split("\n");

        for (String linea : lineas) {
            linea = linea.trim();
            if (linea.startsWith("#") || linea.isEmpty()) continue;

            if (linea.toLowerCase().startsWith("user-agent:")) {
                String agente = linea.substring(11).trim();
                // Aplica si es para todos (*) o específicamente para nuestro bot
                aplicaAMi = agente.equals("*") ||
                            miUserAgent.toLowerCase().contains(agente.toLowerCase());

            } else if (aplicaAMi && linea.toLowerCase().startsWith("disallow:")) {
                String path = linea.substring(9).trim();
                if (!path.isEmpty()) {
                    exclusiones.add(path);
                }

            } else if (aplicaAMi && linea.toLowerCase().startsWith("crawl-delay:")) {
                try {
                    long delay = Long.parseLong(linea.substring(12).trim());
                    crawlDelays.put(dominio, delay);
                } catch (NumberFormatException e) {
                    // Ignorar valores inválidos
                }
            }
        }
    }

    private String obtenerDominio(String url) {
        try {
            return new URI(url).getHost();
        } catch (Exception e) {
            return null;
        }
    }
}
```

---

## 6. Gestión de la Frontera (Frontier)

### 6.1 Frontier con Prioridad

```java
package com.tutorial.crawler.frontier;

import java.util.Comparator;
import java.util.concurrent.PriorityBlockingQueue;

/**
 * Frontier con prioridad: páginas más relevantes se visitan primero.
 * La prioridad puede basarse en: profundidad, dominio, palabras clave en URL, etc.
 */
public class PriorityFrontier {

    record UrlConPrioridad(String url, int profundidad, int prioridad) {}

    // Cola de prioridad: menor número = mayor prioridad
    private final PriorityBlockingQueue<UrlConPrioridad> cola =
        new PriorityBlockingQueue<>(100,
            Comparator.comparingInt(UrlConPrioridad::prioridad));

    private final java.util.Set<String> visitadas = new java.util.concurrent.ConcurrentHashMap<>().newKeySet();

    public void agregar(String url, int profundidad) {
        if (!visitadas.contains(url)) {
            int prioridad = calcularPrioridad(url, profundidad);
            cola.offer(new UrlConPrioridad(url, profundidad, prioridad));
        }
    }

    public UrlConPrioridad obtenerSiguiente() throws InterruptedException {
        UrlConPrioridad siguiente;
        do {
            siguiente = cola.take();
        } while (visitadas.contains(siguiente.url()));

        visitadas.add(siguiente.url());
        return siguiente;
    }

    /**
     * Calcula la prioridad de una URL.
     * Prioridades más bajas = se visitan antes.
     */
    private int calcularPrioridad(String url, int profundidad) {
        int prioridad = profundidad * 10; // Base: la profundidad penaliza

        // Penalizar URLs con muchos parámetros (suelen ser menos importantes)
        if (url.contains("?")) prioridad += 5;

        // Premiar páginas de índice/categoría
        if (url.endsWith("/") || url.matches(".*/[^.]+$")) prioridad -= 3;

        // Penalizar recursos estáticos
        if (url.matches(".*\\.(jpg|png|pdf|zip)$")) prioridad += 100;

        return prioridad;
    }

    public boolean estaVacia() {
        return cola.isEmpty();
    }

    public int tamano() {
        return cola.size();
    }
}
```

### 6.2 Filtros de URL

```java
package com.tutorial.crawler.filter;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;
import java.net.URI;
import java.util.List;
import java.util.Set;
import java.util.regex.Pattern;

/**
 * Aplica múltiples filtros para decidir si una URL debe ser crawleada.
 */
@Component
public class UrlFilter {

    private static final Logger log = LoggerFactory.getLogger(UrlFilter.class);

    // Extensiones de archivos a ignorar
    private static final Set<String> EXTENSIONES_IGNORAR = Set.of(
        "jpg", "jpeg", "png", "gif", "bmp", "svg", "ico", "webp",
        "mp4", "mp3", "avi", "mov", "wmv", "flv",
        "pdf", "doc", "docx", "xls", "xlsx", "ppt", "pptx",
        "zip", "rar", "gz", "tar", "7z",
        "css", "js", "woff", "woff2", "ttf", "eot",
        "exe", "dmg", "apk"
    );

    // Patrones de URLs a ignorar
    private static final List<Pattern> PATRONES_IGNORAR = List.of(
        Pattern.compile(".*/(login|logout|register|signup).*"),
        Pattern.compile(".*/cart.*"),
        Pattern.compile(".*/checkout.*"),
        Pattern.compile(".*\\?.*print=.*"),
        Pattern.compile(".*#.*")  // Fragmentos (mismo contenido diferente ancla)
    );

    private final List<String> dominiosPermitidos;

    public UrlFilter(com.tutorial.crawler.config.CrawlerConfig config) {
        this.dominiosPermitidos = config.getAllowedDomains();
    }

    /**
     * Verifica si una URL debe ser crawleada
     */
    public boolean debeVisitar(String url) {
        if (url == null || url.isBlank()) return false;

        // 1. Solo HTTP/HTTPS
        if (!url.startsWith("http://") && !url.startsWith("https://")) {
            return false;
        }

        // 2. No tiene extensión de archivo estático
        if (tieneExtensionIgnorada(url)) {
            log.trace("URL ignorada (extensión estática): {}", url);
            return false;
        }

        // 3. No coincide con patrones excluidos
        for (Pattern patron : PATRONES_IGNORAR) {
            if (patron.matcher(url).matches()) {
                log.trace("URL ignorada (patrón): {}", url);
                return false;
            }
        }

        // 4. Dominio permitido (si hay restricción)
        if (!dominiosPermitidos.isEmpty() && !esDominioPermitido(url)) {
            return false;
        }

        return true;
    }

    private boolean tieneExtensionIgnorada(String url) {
        try {
            String path = new URI(url).getPath();
            if (path == null) return false;
            int ultimoPunto = path.lastIndexOf('.');
            if (ultimoPunto < 0) return false;
            String extension = path.substring(ultimoPunto + 1).toLowerCase();
            return EXTENSIONES_IGNORAR.contains(extension);
        } catch (Exception e) {
            return false;
        }
    }

    private boolean esDominioPermitido(String url) {
        try {
            String host = new URI(url).getHost();
            if (host == null) return false;
            return dominiosPermitidos.stream().anyMatch(host::endsWith);
        } catch (Exception e) {
            return false;
        }
    }
}
```

---

## 7. Almacenamiento y Persistencia

### 7.1 Servicio principal del Crawler

```java
package com.tutorial.crawler.service;

import com.tutorial.crawler.filter.RobotsFilter;
import com.tutorial.crawler.filter.UrlFilter;
import com.tutorial.crawler.frontier.Frontier;
import com.tutorial.crawler.model.EstadoCrawl;
import com.tutorial.crawler.model.PaginaWeb;
import com.tutorial.crawler.repository.PaginaWebRepository;
import com.tutorial.crawler.util.UrlNormalizer;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.io.IOException;
import java.security.MessageDigest;
import java.time.LocalDateTime;
import java.util.HexFormat;
import java.util.List;
import java.util.Optional;

@Service
public class CrawlerService {

    private static final Logger log = LoggerFactory.getLogger(CrawlerService.class);

    private final Frontier frontier;
    private final UrlFilter urlFilter;
    private final RobotsFilter robotsFilter;
    private final PaginaWebRepository repository;

    private volatile boolean corriendo = false;
    private int maxPaginas = 100;
    private int maxProfundidad = 3;
    private long delayMs = 500;
    private String userAgent = "MiCrawler/1.0";

    public CrawlerService(Frontier frontier, UrlFilter urlFilter,
                          RobotsFilter robotsFilter, PaginaWebRepository repository) {
        this.frontier = frontier;
        this.urlFilter = urlFilter;
        this.robotsFilter = robotsFilter;
        this.repository = repository;
    }

    /**
     * Inicia el proceso de crawling desde una URL seed
     */
    public void iniciarCrawl(List<String> seedUrls, int maxPaginas, int maxProfundidad) {
        this.maxPaginas = maxPaginas;
        this.maxProfundidad = maxProfundidad;
        this.corriendo = true;

        // Agregar URLs semilla a la frontera
        for (String url : seedUrls) {
            String normalizada = UrlNormalizer.normalizar(url);
            if (normalizada != null) {
                frontier.agregar(normalizada, 0);
            }
        }

        log.info("🕷️ Crawler iniciado. Seeds: {}, Max páginas: {}, Max profundidad: {}",
            seedUrls.size(), maxPaginas, maxProfundidad);

        // Ciclo principal de crawling
        int contador = 0;
        while (corriendo && contador < maxPaginas && !frontier.estaVacia()) {
            Optional<PaginaWeb> siguiente = frontier.obtenerSiguiente();
            if (siguiente.isEmpty()) break;

            PaginaWeb pagina = siguiente.get();
            procesarPagina(pagina);
            contador++;

            // Delay de cortesía
            if (delayMs > 0) {
                try {
                    Thread.sleep(delayMs);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }

        corriendo = false;
        log.info("✅ Crawling completado. Páginas procesadas: {}", contador);
        imprimirEstadisticas();
    }

    @Transactional
    protected void procesarPagina(PaginaWeb pagina) {
        log.info("🔍 Visitando [profundidad={}]: {}", pagina.getProfundidad(), pagina.getUrl());

        try {
            // Verificar robots.txt
            if (!robotsFilter.estaPermitida(pagina.getUrl(), userAgent)) {
                pagina.setEstado(EstadoCrawl.EXCLUIDA);
                repository.save(pagina);
                log.debug("  ⛔ Excluida por robots.txt");
                return;
            }

            // Descargar la página
            Document doc = descargarPagina(pagina.getUrl());

            // Calcular hash del contenido para detectar duplicados
            String hash = calcularHash(doc.text());

            if (repository.existsByHashContenido(hash)) {
                pagina.setEstado(EstadoCrawl.DUPLICADA);
                repository.save(pagina);
                log.debug("  🔄 Contenido duplicado");
                return;
            }

            // Extraer metadatos
            pagina.setTitulo(doc.title());
            pagina.setDescripcion(extraerDescripcion(doc));
            pagina.setHashContenido(hash);
            pagina.setTextoExtraido(doc.text().substring(0, Math.min(5000, doc.text().length())));
            pagina.setFechaVisitada(LocalDateTime.now());
            pagina.setEstado(EstadoCrawl.VISITADA);

            // Extraer y agregar nuevos enlaces a la frontera
            if (pagina.getProfundidad() < maxProfundidad) {
                int enlacesNuevos = extraerYAgregarEnlaces(doc, pagina.getProfundidad() + 1);
                pagina.setNumEnlacesSalientes(enlacesNuevos);
            }

            repository.save(pagina);
            log.info("  ✅ Visitada: {} | '{}'", pagina.getUrl(), pagina.getTitulo());

        } catch (IOException e) {
            pagina.setEstado(EstadoCrawl.ERROR);
            repository.save(pagina);
            log.warn("  ❌ Error: {}", e.getMessage());
        }
    }

    private Document descargarPagina(String url) throws IOException {
        return Jsoup.connect(url)
                .userAgent(userAgent)
                .timeout(10000)
                .followRedirects(true)
                .get();
    }

    private int extraerYAgregarEnlaces(Document doc, int nuevaProfundidad) {
        Elements enlaces = doc.select("a[href]");
        int nuevosEnlaces = 0;

        for (Element enlace : enlaces) {
            String href = enlace.attr("abs:href");
            String normalizada = UrlNormalizer.normalizar(href);

            if (normalizada != null && urlFilter.debeVisitar(normalizada)) {
                if (frontier.agregar(normalizada, nuevaProfundidad)) {
                    nuevosEnlaces++;
                }
            }
        }

        return nuevosEnlaces;
    }

    private String extraerDescripcion(Document doc) {
        Element meta = doc.select("meta[name=description]").first();
        if (meta != null) return meta.attr("content");
        // Fallback: primero 200 caracteres del texto
        String texto = doc.text();
        return texto.substring(0, Math.min(200, texto.length()));
    }

    private String calcularHash(String contenido) {
        try {
            MessageDigest md = MessageDigest.getInstance("SHA-256");
            byte[] hash = md.digest(contenido.getBytes());
            return HexFormat.of().formatHex(hash);
        } catch (Exception e) {
            return String.valueOf(contenido.hashCode());
        }
    }

    private void imprimirEstadisticas() {
        log.info("📊 Estadísticas del Crawl:");
        log.info("  Visitadas: {}", repository.countByEstado(EstadoCrawl.VISITADA));
        log.info("  Pendientes: {}", repository.countByEstado(EstadoCrawl.PENDIENTE));
        log.info("  Con error: {}", repository.countByEstado(EstadoCrawl.ERROR));
        log.info("  Excluidas: {}", repository.countByEstado(EstadoCrawl.EXCLUIDA));
        log.info("  Duplicadas: {}", repository.countByEstado(EstadoCrawl.DUPLICADA));
    }

    public void detener() {
        corriendo = false;
        log.info("🛑 Deteniendo crawler...");
    }

    public boolean isRunning() {
        return corriendo;
    }
}
```

---

## 8. Crawler Multihilo y Concurrente

### 8.1 Crawler con múltiples hilos

```java
package com.tutorial.crawler.service;

import com.tutorial.crawler.frontier.Frontier;
import com.tutorial.crawler.model.PaginaWeb;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;
import java.util.concurrent.*;
import java.util.concurrent.atomic.AtomicInteger;

@Service
public class CrawlerMultihilo {

    private static final Logger log = LoggerFactory.getLogger(CrawlerMultihilo.class);

    private final Frontier frontier;
    private final CrawlerService crawlerService;

    // Contador de páginas procesadas (thread-safe)
    private final AtomicInteger paginasProcesadas = new AtomicInteger(0);

    public CrawlerMultihilo(Frontier frontier, CrawlerService crawlerService) {
        this.frontier = frontier;
        this.crawlerService = crawlerService;
    }

    /**
     * Inicia el crawling con múltiples hilos concurrentes.
     */
    public void crawlearConHilos(List<String> seeds, int maxPaginas,
                                  int numHilos, long delayMs) throws InterruptedException {
        // Pool de hilos fijo
        ExecutorService executor = Executors.newFixedThreadPool(numHilos,
            r -> {
                Thread t = new Thread(r, "crawler-thread-" + paginasProcesadas.incrementAndGet());
                t.setDaemon(true);
                return t;
            }
        );

        // Agregar seeds
        seeds.forEach(url -> frontier.agregar(url, 0));

        // Barrera de finalización: cuando no haya más trabajo
        List<Future<?>> tareas = new ArrayList<>();

        log.info("🚀 Iniciando crawl con {} hilos", numHilos);

        while (paginasProcesadas.get() < maxPaginas && !frontier.estaVacia()) {
            Optional<PaginaWeb> siguiente = frontier.obtenerSiguiente();
            if (siguiente.isEmpty()) {
                Thread.sleep(100); // Esperar si la frontera está momentáneamente vacía
                continue;
            }

            PaginaWeb pagina = siguiente.get();
            Future<?> tarea = executor.submit(() -> {
                try {
                    crawlerService.procesarPagina(pagina);
                    paginasProcesadas.incrementAndGet();
                    Thread.sleep(delayMs); // Delay por hilo
                } catch (Exception e) {
                    log.error("Error procesando {}: {}", pagina.getUrl(), e.getMessage());
                }
            });
            tareas.add(tarea);
        }

        // Esperar a que terminen todas las tareas
        executor.shutdown();
        executor.awaitTermination(30, TimeUnit.MINUTES);

        log.info("✅ Crawl multihilo completado. Páginas: {}", paginasProcesadas.get());
    }
}
```

### 8.2 Control de Cortesía por Dominio

```java
package com.tutorial.crawler.service;

import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.TimeUnit;

/**
 * Controla el rate limit por dominio para ser "cortés" con los servidores.
 * Asegura que no se hagan más de N peticiones por dominio por segundo.
 */
public class PolitenessController {

    // Timestamp de la última petición por dominio
    private final Map<String, Long> ultimaPeticionPorDominio = new ConcurrentHashMap<>();
    private final long delayMinimoPorDominio; // en milisegundos

    public PolitenessController(long delayMinimoPorDominio) {
        this.delayMinimoPorDominio = delayMinimoPorDominio;
    }

    /**
     * Espera el tiempo necesario antes de hacer una petición al dominio dado.
     * Llamar antes de cada petición HTTP.
     */
    public synchronized void esperarAntesDeAcceder(String dominio)
            throws InterruptedException {
        long ahora = System.currentTimeMillis();
        Long ultimaPeticion = ultimaPeticionPorDominio.get(dominio);

        if (ultimaPeticion != null) {
            long tiempoTranscurrido = ahora - ultimaPeticion;
            if (tiempoTranscurrido < delayMinimoPorDominio) {
                long tiempoEspera = delayMinimoPorDominio - tiempoTranscurrido;
                TimeUnit.MILLISECONDS.sleep(tiempoEspera);
            }
        }

        ultimaPeticionPorDominio.put(dominio, System.currentTimeMillis());
    }
}
```

---

## 9. Crawler para Páginas Dinámicas (JavaScript)

Cuando el sitio usa JavaScript para cargar contenido (SPAs, React, Vue), necesitamos un navegador:

```java
package com.tutorial.crawler.avanzado;

import io.github.bonigarcia.wdm.WebDriverManager;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.springframework.stereotype.Service;
import java.time.Duration;
import java.util.HashSet;
import java.util.LinkedList;
import java.util.Queue;
import java.util.Set;

@Service
public class CrawlerDinamico {

    private WebDriver driver;
    private WebDriverWait wait;

    public void inicializar() {
        WebDriverManager.chromedriver().setup();

        ChromeOptions options = new ChromeOptions();
        options.addArguments(
            "--headless",
            "--no-sandbox",
            "--disable-dev-shm-usage",
            "--disable-gpu",
            "--window-size=1920,1080"
        );

        // Bloquear imágenes y CSS para mayor velocidad
        options.addArguments("--blink-settings=imagesEnabled=false");

        driver = new ChromeDriver(options);
        wait = new WebDriverWait(driver, Duration.ofSeconds(15));
    }

    /**
     * Crawlea un SPA (Single Page Application) que usa JavaScript
     */
    public void crawlearSPA(String urlInicial, int maxPaginas) throws InterruptedException {
        Set<String> visitadas = new HashSet<>();
        Queue<String> pendientes = new LinkedList<>();
        pendientes.add(urlInicial);

        int contador = 0;

        while (!pendientes.isEmpty() && contador < maxPaginas) {
            String url = pendientes.poll();
            if (visitadas.contains(url)) continue;

            try {
                driver.get(url);

                // Esperar a que el contenido dinámico cargue
                wait.until(ExpectedConditions.presenceOfElementLocated(By.tagName("body")));
                Thread.sleep(2000); // Esperar renderizado de JS

                // Obtener el HTML renderizado (incluyendo contenido generado por JS)
                String htmlRendered = driver.getPageSource();
                Document doc = Jsoup.parse(htmlRendered, url);

                // Procesar la página
                System.out.printf("[%d] %s | Título: %s%n",
                    ++contador, url, doc.title());

                visitadas.add(url);

                // Descubrir nuevos enlaces
                doc.select("a[href]").forEach(enlace -> {
                    String href = enlace.attr("abs:href");
                    if (href.startsWith("http") && !visitadas.contains(href)) {
                        pendientes.add(href);
                    }
                });

                Thread.sleep(500);

            } catch (Exception e) {
                System.err.printf("Error en %s: %s%n", url, e.getMessage());
                visitadas.add(url);
            }
        }
    }

    /**
     * Esperar a que la red esté inactiva (útil para SPAs)
     */
    public void esperarRedInactiva() throws InterruptedException {
        // Usar JavaScript para detectar cuando no hay peticiones fetch/XHR activas
        JavascriptExecutor js = (JavascriptExecutor) driver;

        int intentos = 0;
        while (intentos < 30) {
            Boolean activo = (Boolean) js.executeScript(
                "return window.fetch_active > 0 || window.xhr_active > 0");
            if (activo == null || !activo) break;
            Thread.sleep(500);
            intentos++;
        }
    }

    public void cerrar() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

---

## 10. Indexación y Búsqueda con Apache Lucene

Después de crawlear, podemos indexar el contenido para permitir búsquedas:

### 10.1 Servicio de Indexación

```java
package com.tutorial.crawler.service;

import com.tutorial.crawler.model.PaginaWeb;
import org.apache.lucene.analysis.standard.StandardAnalyzer;
import org.apache.lucene.document.*;
import org.apache.lucene.index.*;
import org.apache.lucene.queryparser.classic.ParseException;
import org.apache.lucene.queryparser.classic.QueryParser;
import org.apache.lucene.search.*;
import org.apache.lucene.store.FSDirectory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import java.io.IOException;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.List;

@Service
public class IndexService {

    private static final Logger log = LoggerFactory.getLogger(IndexService.class);

    @Value("${lucene.index-dir:./lucene-index}")
    private String indexDir;

    private IndexWriter indexWriter;
    private FSDirectory directorio;
    private final StandardAnalyzer analyzer = new StandardAnalyzer();

    @PostConstruct
    public void inicializar() throws IOException {
        directorio = FSDirectory.open(Paths.get(indexDir));
        IndexWriterConfig config = new IndexWriterConfig(analyzer);
        config.setOpenMode(IndexWriterConfig.OpenMode.CREATE_OR_APPEND);
        indexWriter = new IndexWriter(directorio, config);
        log.info("📑 Índice Lucene inicializado en: {}", indexDir);
    }

    /**
     * Indexa una página web
     */
    public void indexarPagina(PaginaWeb pagina) throws IOException {
        Document doc = new Document();

        // Campos indexados y almacenados
        doc.add(new StringField("url", pagina.getUrl(), Field.Store.YES));
        doc.add(new TextField("titulo", pagina.getTitulo() != null ? pagina.getTitulo() : "", Field.Store.YES));
        doc.add(new TextField("descripcion", pagina.getDescripcion() != null ? pagina.getDescripcion() : "", Field.Store.YES));
        doc.add(new TextField("contenido", pagina.getTextoExtraido() != null ? pagina.getTextoExtraido() : "", Field.Store.NO));
        doc.add(new StringField("dominio", pagina.getDominio() != null ? pagina.getDominio() : "", Field.Store.YES));
        doc.add(new IntPoint("profundidad", pagina.getProfundidad()));
        doc.add(new StoredField("profundidadValor", pagina.getProfundidad()));

        // Actualizar si ya existe (usando la URL como clave)
        indexWriter.updateDocument(new Term("url", pagina.getUrl()), doc);
        indexWriter.commit();

        log.debug("Indexada: {}", pagina.getUrl());
    }

    /**
     * Busca en el índice
     */
    public List<ResultadoBusqueda> buscar(String consulta, int maxResultados)
            throws IOException, ParseException {
        List<ResultadoBusqueda> resultados = new ArrayList<>();

        // Abrir reader para búsqueda
        try (DirectoryReader reader = DirectoryReader.open(directorio)) {
            IndexSearcher searcher = new IndexSearcher(reader);

            // Buscar en título, descripción y contenido
            QueryParser parser = new QueryParser("contenido", analyzer);
            Query query = parser.parse(consulta);

            // También buscar en título con mayor peso (boost)
            BooleanQuery.Builder builder = new BooleanQuery.Builder();
            builder.add(new BoostQuery(new QueryParser("titulo", analyzer).parse(consulta), 3.0f),
                        BooleanClause.Occur.SHOULD);
            builder.add(query, BooleanClause.Occur.SHOULD);

            TopDocs topDocs = searcher.search(builder.build(), maxResultados);
            log.info("🔍 Búsqueda '{}': {} resultados", consulta, topDocs.totalHits.value);

            for (ScoreDoc scoreDoc : topDocs.scoreDocs) {
                Document doc = searcher.doc(scoreDoc.doc);
                resultados.add(new ResultadoBusqueda(
                    doc.get("url"),
                    doc.get("titulo"),
                    doc.get("descripcion"),
                    scoreDoc.score
                ));
            }
        }

        return resultados;
    }

    public record ResultadoBusqueda(String url, String titulo, String descripcion, float score) {}

    @PreDestroy
    public void cerrar() throws IOException {
        if (indexWriter != null) indexWriter.close();
        if (directorio != null) directorio.close();
        log.info("📑 Índice Lucene cerrado");
    }
}
```

---

## 11. Arquitectura de Crawler Distribuido

Para crawls a gran escala, necesitamos una arquitectura distribuida:

```
┌─────────────────────────────────────────────────────────────────────┐
│                  ARQUITECTURA DISTRIBUIDA                           │
│                                                                     │
│  ┌─────────────┐    ┌─────────────────────────────────────────┐    │
│  │  Coordinador│    │          Workers (N instancias)         │    │
│  │             │    │  ┌─────────┐ ┌─────────┐ ┌─────────┐   │    │
│  │  - Asigna   │───►│  │Worker 1 │ │Worker 2 │ │Worker N │   │    │
│  │    tareas   │    │  │         │ │         │ │         │   │    │
│  │  - Monitorea│◄───│  │Descarga │ │Descarga │ │Descarga │   │    │
│  │  - Balanceo │    │  └────┬────┘ └────┬────┘ └────┬────┘   │    │
│  └─────────────┘    └───────┼───────────┼───────────┼─────────┘   │
│         │                   └─────────────────────────┘            │
│         ▼                               │                           │
│  ┌─────────────┐                        ▼                           │
│  │   Message   │              ┌─────────────────┐                  │
│  │   Queue     │              │    Storage      │                  │
│  │ (RabbitMQ/  │              │  (PostgreSQL /  │                  │
│  │  Kafka)     │              │   Elasticsearch)│                  │
│  └─────────────┘              └─────────────────┘                  │
└─────────────────────────────────────────────────────────────────────┘
```

### 11.1 Productor de mensajes (URLs a crawlear)

```java
package com.tutorial.crawler.distribuido;

import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.stereotype.Service;

// Nota: Requiere spring-boot-starter-amqp en pom.xml
@Service
public class UrlProducer {

    private static final String EXCHANGE = "crawler.exchange";
    private static final String ROUTING_KEY = "urls.pendientes";

    private final RabbitTemplate rabbitTemplate;

    public UrlProducer(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    public void publicarUrl(String url, int profundidad) {
        UrlTarea tarea = new UrlTarea(url, profundidad);
        rabbitTemplate.convertAndSend(EXCHANGE, ROUTING_KEY, tarea);
    }

    public record UrlTarea(String url, int profundidad) {}
}
```

### 11.2 Consumidor (Worker)

```java
package com.tutorial.crawler.distribuido;

import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Component
public class CrawlerWorker {

    private static final String QUEUE = "crawler.urls.queue";

    @RabbitListener(queues = QUEUE)
    public void procesarUrl(UrlProducer.UrlTarea tarea) {
        // Cada worker procesa una URL independientemente
        System.out.printf("Worker procesando: %s (profundidad: %d)%n",
            tarea.url(), tarea.profundidad());
        // ... lógica de descarga, parseo y almacenamiento
    }
}
```

---

## 12. Proyecto Completo: Motor de Búsqueda Básico

### 12.1 API REST del Crawler

```java
package com.tutorial.crawler.controller;

import com.tutorial.crawler.service.CrawlerService;
import com.tutorial.crawler.service.IndexService;
import com.tutorial.crawler.repository.PaginaWebRepository;
import com.tutorial.crawler.model.EstadoCrawl;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;
import java.util.Map;

@RestController
@RequestMapping("/api/crawler")
public class CrawlerController {

    private final CrawlerService crawlerService;
    private final IndexService indexService;
    private final PaginaWebRepository repository;

    public CrawlerController(CrawlerService crawlerService,
                              IndexService indexService,
                              PaginaWebRepository repository) {
        this.crawlerService = crawlerService;
        this.indexService = indexService;
        this.repository = repository;
    }

    /**
     * POST /api/crawler/iniciar
     * Body: { "seeds": ["https://ejemplo.com"], "maxPaginas": 50, "maxProfundidad": 2 }
     */
    @PostMapping("/iniciar")
    public ResponseEntity<Map<String, Object>> iniciar(@RequestBody Map<String, Object> config) {
        if (crawlerService.isRunning()) {
            return ResponseEntity.badRequest().body(Map.of(
                "error", "El crawler ya está ejecutándose"
            ));
        }

        List<String> seeds = (List<String>) config.get("seeds");
        int maxPaginas = (Integer) config.getOrDefault("maxPaginas", 100);
        int maxProfundidad = (Integer) config.getOrDefault("maxProfundidad", 3);

        // Ejecutar en segundo plano para no bloquear el endpoint
        Thread crawlThread = new Thread(() ->
            crawlerService.iniciarCrawl(seeds, maxPaginas, maxProfundidad));
        crawlThread.setDaemon(true);
        crawlThread.start();

        return ResponseEntity.ok(Map.of(
            "estado", "iniciado",
            "seeds", seeds,
            "maxPaginas", maxPaginas,
            "maxProfundidad", maxProfundidad
        ));
    }

    /**
     * POST /api/crawler/detener
     */
    @PostMapping("/detener")
    public ResponseEntity<Map<String, String>> detener() {
        crawlerService.detener();
        return ResponseEntity.ok(Map.of("estado", "deteniendo"));
    }

    /**
     * GET /api/crawler/estado
     */
    @GetMapping("/estado")
    public ResponseEntity<Map<String, Object>> obtenerEstado() {
        return ResponseEntity.ok(Map.of(
            "corriendo", crawlerService.isRunning(),
            "visitadas", repository.countByEstado(EstadoCrawl.VISITADA),
            "pendientes", repository.countByEstado(EstadoCrawl.PENDIENTE),
            "errores", repository.countByEstado(EstadoCrawl.ERROR),
            "excluidas", repository.countByEstado(EstadoCrawl.EXCLUIDA),
            "duplicadas", repository.countByEstado(EstadoCrawl.DUPLICADA),
            "totalDescubiertas", repository.count()
        ));
    }

    /**
     * GET /api/crawler/buscar?q=spring+boot&max=10
     */
    @GetMapping("/buscar")
    public ResponseEntity<?> buscar(
            @RequestParam String q,
            @RequestParam(defaultValue = "10") int max) {
        try {
            List<IndexService.ResultadoBusqueda> resultados =
                indexService.buscar(q, max);
            return ResponseEntity.ok(Map.of(
                "consulta", q,
                "total", resultados.size(),
                "resultados", resultados
            ));
        } catch (Exception e) {
            return ResponseEntity.internalServerError().body(Map.of(
                "error", e.getMessage()
            ));
        }
    }

    /**
     * GET /api/crawler/estadisticas/dominios
     */
    @GetMapping("/estadisticas/dominios")
    public ResponseEntity<List<Object[]>> estadisticasDominios() {
        return ResponseEntity.ok(repository.estadisticasPorDominio());
    }
}
```

---

## 13. Ejercicios Resueltos

### Ejercicio 1: Crawler de un Solo Dominio

**Enunciado**: Crea un crawler que visite todas las páginas de `quotes.toscrape.com`, extrayendo las frases y autores, y los guarde en un archivo JSON.

**Solución:**

```java
package com.tutorial.crawler.ejercicios;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import java.io.File;
import java.io.IOException;
import java.util.*;

public class Ejercicio1_CrawlerFrases {

    record Frase(String texto, String autor, List<String> etiquetas) {}

    public static void main(String[] args) throws IOException, InterruptedException {
        Set<String> visitadas = new HashSet<>();
        Queue<String> pendientes = new LinkedList<>();
        List<Frase> todasLasFrases = new ArrayList<>();

        pendientes.add("https://quotes.toscrape.com/");

        while (!pendientes.isEmpty()) {
            String url = pendientes.poll();
            if (visitadas.contains(url)) continue;

            System.out.println("📄 Visitando: " + url);
            Document doc = Jsoup.connect(url).userAgent("Mozilla/5.0").timeout(10000).get();
            visitadas.add(url);

            // Extraer frases de la página actual
            for (Element cita : doc.select("div.quote")) {
                String texto = cita.select("span.text").text();
                String autor = cita.select("small.author").text();
                List<String> etiquetas = new ArrayList<>();
                cita.select("a.tag").forEach(tag -> etiquetas.add(tag.text()));
                todasLasFrases.add(new Frase(texto, autor, etiquetas));
            }

            // Descubrir siguiente página
            Element siguiente = doc.select("li.next a").first();
            if (siguiente != null) {
                String siguienteUrl = "https://quotes.toscrape.com" + siguiente.attr("href");
                pendientes.add(siguienteUrl);
            }

            Thread.sleep(300);
        }

        System.out.println("✅ Total frases: " + todasLasFrases.size());

        // Guardar como JSON
        ObjectMapper mapper = new ObjectMapper();
        mapper.writerWithDefaultPrettyPrinter()
              .writeValue(new File("frases.json"), todasLasFrases);
        System.out.println("💾 Guardado en frases.json");
    }
}
```

---

### Ejercicio 2: Crawler con Grafo de Enlaces

**Enunciado**: Implementa un crawler que construya el grafo de enlaces entre páginas de un sitio (qué página apunta a qué otra) y lo visualice como JSON.

**Solución:**

```java
package com.tutorial.crawler.ejercicios;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import java.io.File;
import java.io.IOException;
import java.util.*;

public class Ejercicio2_GrafoEnlaces {

    // Grafo: URL -> Lista de URLs a las que apunta
    static Map<String, Set<String>> grafo = new LinkedHashMap<>();

    public static void main(String[] args) throws IOException, InterruptedException {
        String seedUrl = "https://books.toscrape.com/";
        int maxPaginas = 15;

        construirGrafo(seedUrl, maxPaginas);

        // Calcular estadísticas
        System.out.println("\n📊 Estadísticas del grafo:");
        System.out.println("Nodos (páginas): " + grafo.size());
        int totalAristas = grafo.values().stream().mapToInt(Set::size).sum();
        System.out.println("Aristas (enlaces): " + totalAristas);

        // Encontrar la página con más enlaces entrantes (in-degree)
        Map<String, Integer> inDegree = new HashMap<>();
        grafo.forEach((origen, destinos) ->
            destinos.forEach(dest ->
                inDegree.merge(dest, 1, Integer::sum)
            )
        );

        inDegree.entrySet().stream()
            .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
            .limit(5)
            .forEach(e ->
                System.out.printf("  %d enlaces entrantes: %s%n", e.getValue(), e.getKey())
            );

        // Guardar grafo como JSON
        ObjectMapper mapper = new ObjectMapper();
        Map<String, Object> resultado = new LinkedHashMap<>();
        resultado.put("nodos", grafo.size());
        resultado.put("aristas", totalAristas);
        resultado.put("grafo", grafo);

        mapper.writerWithDefaultPrettyPrinter()
              .writeValue(new File("grafo_enlaces.json"), resultado);
        System.out.println("\n💾 Grafo guardado en grafo_enlaces.json");
    }

    static void construirGrafo(String seedUrl, int maxPaginas)
            throws IOException, InterruptedException {
        Queue<String> pendientes = new LinkedList<>();
        Set<String> visitadas = new HashSet<>();
        pendientes.add(seedUrl);

        while (!pendientes.isEmpty() && visitadas.size() < maxPaginas) {
            String url = pendientes.poll();
            if (visitadas.contains(url)) continue;

            try {
                Document doc = Jsoup.connect(url).userAgent("Mozilla/5.0").timeout(10000).get();
                visitadas.add(url);

                Set<String> enlacesSalientes = new HashSet<>();

                for (Element enlace : doc.select("a[href]")) {
                    String href = enlace.attr("abs:href");
                    if (href.startsWith("https://books.toscrape.com")) {
                        // Normalizar URL
                        href = href.split("\\?")[0].split("#")[0];
                        enlacesSalientes.add(href);
                        if (!visitadas.contains(href)) {
                            pendientes.add(href);
                        }
                    }
                }

                grafo.put(url, enlacesSalientes);
                System.out.printf("[%d] %s -> %d enlaces%n",
                    visitadas.size(), url, enlacesSalientes.size());

                Thread.sleep(300);

            } catch (IOException e) {
                System.err.println("Error: " + e.getMessage());
                visitadas.add(url);
            }
        }
    }
}
```

---

### Ejercicio 3: Crawler con Detección de Cambios

**Enunciado**: Implementa un crawler que detecte cuándo el contenido de una página ha cambiado respecto a una visita anterior.

**Solución:**

```java
package com.tutorial.crawler.ejercicios;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import java.io.*;
import java.nio.file.*;
import java.security.MessageDigest;
import java.time.LocalDateTime;
import java.util.*;

public class Ejercicio3_DeteccionCambios {

    record RegistroPagina(String url, String hash, LocalDateTime ultimaVisita) {}

    static Map<String, RegistroPagina> registros = new HashMap<>();
    static final String ARCHIVO_HISTORIAL = "historial_crawl.dat";

    public static void main(String[] args) throws Exception {
        cargarHistorial();

        List<String> urlsAMonitorear = List.of(
            "https://books.toscrape.com/",
            "https://quotes.toscrape.com/"
        );

        System.out.println("🔍 Verificando cambios en " + urlsAMonitorear.size() + " páginas...\n");

        for (String url : urlsAMonitorear) {
            verificarCambio(url);
            Thread.sleep(500);
        }

        guardarHistorial();
        System.out.println("\n💾 Historial actualizado");
    }

    static void verificarCambio(String url) throws Exception {
        Document doc = Jsoup.connect(url).userAgent("Mozilla/5.0").timeout(10000).get();
        String contenido = doc.text();
        String hashActual = calcularHash(contenido);
        LocalDateTime ahora = LocalDateTime.now();

        RegistroPagina registroAnterior = registros.get(url);

        if (registroAnterior == null) {
            System.out.printf("🆕 Nueva URL registrada: %s%n", url);
        } else if (!registroAnterior.hash().equals(hashActual)) {
            System.out.printf("⚠️  CAMBIO DETECTADO en: %s%n", url);
            System.out.printf("   Última visita: %s%n", registroAnterior.ultimaVisita());
            System.out.printf("   Hash anterior: %s%n", registroAnterior.hash().substring(0, 8) + "...");
            System.out.printf("   Hash actual:   %s%n", hashActual.substring(0, 8) + "...");
        } else {
            System.out.printf("✅ Sin cambios: %s (última visita: %s)%n",
                url, registroAnterior.ultimaVisita());
        }

        registros.put(url, new RegistroPagina(url, hashActual, ahora));
    }

    static String calcularHash(String contenido) throws Exception {
        MessageDigest md = MessageDigest.getInstance("MD5");
        byte[] hash = md.digest(contenido.getBytes());
        return HexFormat.of().formatHex(hash);
    }

    @SuppressWarnings("unchecked")
    static void cargarHistorial() {
        try {
            if (Files.exists(Paths.get(ARCHIVO_HISTORIAL))) {
                try (ObjectInputStream ois = new ObjectInputStream(
                        new FileInputStream(ARCHIVO_HISTORIAL))) {
                    registros = (Map<String, RegistroPagina>) ois.readObject();
                    System.out.println("📂 Historial cargado: " + registros.size() + " registros");
                }
            }
        } catch (Exception e) {
            System.out.println("📂 No se encontró historial previo. Iniciando desde cero.");
        }
    }

    static void guardarHistorial() throws IOException {
        try (ObjectOutputStream oos = new ObjectOutputStream(
                new FileOutputStream(ARCHIVO_HISTORIAL))) {
            oos.writeObject(registros);
        }
    }
}
```

---

### Ejercicio 4: Crawler Específico de Dominio con Depth Limit

**Enunciado**: Crea un crawler completo con Spring Boot que crawlee un sitio con límite de profundidad, respete robots.txt, y exponga el progreso vía API REST.

**Solución:**

```java
package com.tutorial.crawler.ejercicios;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;
import java.io.IOException;
import java.util.*;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicInteger;

@Service
public class Ejercicio4_CrawlerConProgreso {

    private static final Logger log = LoggerFactory.getLogger(Ejercicio4_CrawlerConProgreso.class);

    // Estado del crawl (accesible por la API REST)
    private volatile boolean ejecutando = false;
    private final AtomicInteger visitadas = new AtomicInteger(0);
    private final AtomicInteger errores = new AtomicInteger(0);
    private final Set<String> urlsVisitadas = ConcurrentHashMap.newKeySet();

    public record EstadoCrawl(
        boolean ejecutando,
        int visitadas,
        int errores,
        int descubiertas
    ) {}

    public EstadoCrawl obtenerEstado() {
        return new EstadoCrawl(
            ejecutando,
            visitadas.get(),
            errores.get(),
            urlsVisitadas.size()
        );
    }

    @Async
    public void crawlearAsync(String urlBase, int maxProfundidad, int maxPaginas,
                               long delayMs) {
        ejecutando = true;
        visitadas.set(0);
        errores.set(0);
        urlsVisitadas.clear();

        // Cola con profundidad: [URL, profundidad]
        Queue<AbstractMap.SimpleEntry<String, Integer>> cola = new LinkedList<>();
        cola.add(new AbstractMap.SimpleEntry<>(urlBase, 0));

        log.info("🕷️ Iniciando crawl de {} (profundidad máx: {})", urlBase, maxProfundidad);

        while (!cola.isEmpty() && visitadas.get() < maxPaginas) {
            var entrada = cola.poll();
            String url = entrada.getKey();
            int profundidad = entrada.getValue();

            if (urlsVisitadas.contains(url)) continue;
            urlsVisitadas.add(url);

            try {
                Document doc = Jsoup.connect(url)
                        .userAgent("EjercicioCrawler/1.0")
                        .timeout(10000)
                        .get();

                visitadas.incrementAndGet();
                log.info("[{}] Profundidad {}: {} | {}", visitadas.get(), profundidad, url, doc.title());

                // Seguir enlaces si no hemos alcanzado la profundidad máxima
                if (profundidad < maxProfundidad) {
                    for (Element enlace : doc.select("a[href]")) {
                        String href = enlace.attr("abs:href");
                        if (href.startsWith(urlBase) && !urlsVisitadas.contains(href)) {
                            cola.add(new AbstractMap.SimpleEntry<>(href, profundidad + 1));
                        }
                    }
                }

                Thread.sleep(delayMs);

            } catch (IOException | InterruptedException e) {
                errores.incrementAndGet();
                log.warn("Error en {}: {}", url, e.getMessage());
                if (e instanceof InterruptedException) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }

        ejecutando = false;
        log.info("✅ Crawl completado. Visitadas: {}, Errores: {}", visitadas.get(), errores.get());
    }
}
```

---

### Ejercicio 5: Crawler con Crawl4j

**Enunciado**: Usa el framework Crawl4j para crear un crawler profesional que indexe un sitio web.

**Solución:**

```java
package com.tutorial.crawler.ejercicios;

import edu.uci.ics.crawler4j.crawler.CrawlConfig;
import edu.uci.ics.crawler4j.crawler.CrawlController;
import edu.uci.ics.crawler4j.crawler.Page;
import edu.uci.ics.crawler4j.crawler.WebCrawler;
import edu.uci.ics.crawler4j.fetcher.PageFetcher;
import edu.uci.ics.crawler4j.parser.HtmlParseData;
import edu.uci.ics.crawler4j.robotstxt.RobotstxtConfig;
import edu.uci.ics.crawler4j.robotstxt.RobotstxtServer;
import edu.uci.ics.crawler4j.url.WebURL;
import java.util.Set;
import java.util.regex.Pattern;

public class Ejercicio5_Crawl4jDemo {

    // --- Clase del Crawler ---
    public static class MiCrawler extends WebCrawler {

        private static final Pattern FILTRO_EXTENSIONES =
            Pattern.compile(".*(\\.(css|js|gif|jpg|png|mp4|mp3|pdf|zip))$");

        @Override
        public boolean shouldVisit(Page referringPage, WebURL url) {
            String href = url.getURL().toLowerCase();

            // Ignorar archivos estáticos
            if (FILTRO_EXTENSIONES.matcher(href).matches()) return false;

            // Solo visitar el dominio objetivo
            return href.startsWith("https://books.toscrape.com/");
        }

        @Override
        public void visit(Page page) {
            String url = page.getWebURL().getURL();
            System.out.printf("Visitando [profundidad=%d]: %s%n",
                page.getWebURL().getDepth(), url);

            if (page.getParseData() instanceof HtmlParseData htmlData) {
                String titulo = htmlData.getTitle();
                Set<WebURL> enlaces = htmlData.getOutgoingUrls();

                System.out.printf("  Título: %s | Texto: %d chars | Salientes: %d%n",
                    titulo,
                    htmlData.getText().length(),
                    enlaces.size()
                );
            }
        }
    }

    // --- Configuración y Ejecución ---
    public static void main(String[] args) throws Exception {
        String carpetaCrawl = "/tmp/crawl4j_datos";
        int numCrawlers = 3;

        CrawlConfig config = new CrawlConfig();
        config.setCrawlStorageFolder(carpetaCrawl);
        config.setMaxDepthOfCrawling(2);       // Profundidad máxima
        config.setMaxPagesToFetch(50);          // Máximo de páginas
        config.setPolitenessDelay(500);         // 500ms entre peticiones
        config.setResumableCrawling(false);     // No continuar si se interrumpe
        config.setUserAgentString("Crawl4jDemo/1.0");
        config.setRespectNoFollow(true);        // Respetar nofollow

        PageFetcher pageFetcher = new PageFetcher(config);
        RobotstxtConfig robotsConfig = new RobotstxtConfig();
        RobotstxtServer robotsServer = new RobotstxtServer(robotsConfig, pageFetcher);

        CrawlController controller = new CrawlController(config, pageFetcher, robotsServer);

        // Agregar URL seed
        controller.addSeed("https://books.toscrape.com/");

        System.out.println("🕷️ Iniciando Crawl4j con " + numCrawlers + " crawlers...");

        // Iniciar el crawl de forma bloqueante
        controller.start(MiCrawler.class, numCrawlers);

        System.out.println("✅ Crawl4j completado.");
    }
}
```

---

## 14. Diferencias entre Crawlers y Scrapers: Cuándo usar cada uno

### Comparativa Detallada

| Característica | Scraper | Crawler |
|---|---|---|
| **Objetivo** | Extraer datos específicos | Descubrir y mapear contenido |
| **URLs conocidas** | Sí, de antemano | No, las descubre dinámicamente |
| **Seguimiento de enlaces** | No (o limitado) | Sí, es su función principal |
| **Alcance** | Específico y definido | Amplio y dinámico |
| **Estado** | Generalmente sin estado | Mantiene estado (visitadas, pendientes) |
| **Complejidad** | Menor | Mayor |
| **Caso de uso** | Monitorear precio de 10 productos | Indexar todos los productos de una tienda |
| **Ejemplo** | Extraer todos los precios de amazon.com/deals | Descubrir todos los productos de amazon.com |

### ¿Cuándo usar Scraping?

- ✅ Quieres datos de páginas **específicas y conocidas**
- ✅ Necesitas extraer datos **estructurados** de formularios, tablas, etc.
- ✅ Monitorear **cambios de precio**, disponibilidad de stock
- ✅ Extraer datos de una **API oculta** que el sitio usa internamente
- ✅ Procesar un **conjunto finito** de URLs

### ¿Cuándo usar Crawling?

- ✅ Quieres **descubrir** todo el contenido de un sitio o dominio
- ✅ Construir un **índice de búsqueda** (como Google)
- ✅ **Auditoría SEO** (encontrar páginas rotas, falta de títulos, etc.)
- ✅ **Análisis de la estructura** de un sitio (grafo de enlaces, depth)
- ✅ **Archivar** un sitio web completo

### Combinación: Crawler + Scraper

En muchos proyectos reales, se usan ambos juntos:

```
Crawler descubre URLs ──► Scraper extrae datos de cada URL
         │                          │
    [Fase 1: Descubrimiento]   [Fase 2: Extracción]
         │                          │
    Cola de URLs              Base de datos con datos estructurados
```

---

## 15. Recursos Adicionales

### Frameworks y Bibliotecas
- [Crawl4j](https://github.com/yasserg/crawler4j) - Framework de crawling para Java
- [Jsoup](https://jsoup.org) - HTML parser y cliente HTTP simple
- [Selenium WebDriver](https://selenium.dev) - Automatización de navegador
- [Apache Nutch](https://nutch.apache.org/) - Crawler web de nivel empresarial
- [Heritrix](https://github.com/internetarchive/heritrix3) - Crawler del Internet Archive
- [Apache Lucene](https://lucene.apache.org/) - Motor de búsqueda para Java

### Estándares y Protocolos
- [RFC 9309 - Robots Exclusion Protocol](https://www.rfc-editor.org/rfc/rfc9309) - Estándar oficial de robots.txt
- [Sitemaps Protocol](https://www.sitemaps.org/protocol.html) - Guía para que los crawlers descubran contenido
- [Common Crawl](https://commoncrawl.org/) - Conjunto de datos de crawl público y gratuito

### Herramientas para Análisis y Debugging
- **Chrome DevTools > Network**: Ver peticiones XHR/Fetch que hace el sitio
- **curl**: Probar peticiones HTTP desde la línea de comandos
- **wget**: Descargar sitios completos para análisis offline
- **Wireshark**: Analizar tráfico de red a bajo nivel

### Lecturas Adicionales
- "Introduction to Information Retrieval" (Manning, Raghavan, Schütze) - Referencia estándar en IR
- "Web Data Mining" (Bing Liu) - Técnicas de extracción y análisis web
- Google's "How Google Search Works" - Para entender crawling a escala masiva

---

> 📝 **Resumen del Tutorial**:
> Has aprendido cómo funcionan los web crawlers desde sus conceptos fundamentales (BFS/DFS, frontier, filtros) hasta implementaciones avanzadas con Spring Boot, crawling multihilo, respeto a robots.txt, páginas dinámicas con Selenium, e indexación con Apache Lucene. Los ejercicios resueltos te dan ejemplos concretos para construir desde un crawler básico hasta un motor de búsqueda funcional.
