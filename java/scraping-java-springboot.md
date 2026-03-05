# 🕷️ Web Scraping en Java con Spring Boot - Tutorial Completo

## Introducción

El **Web Scraping** es la técnica de extracción automática de datos de sitios web. Consiste en analizar el HTML (y a veces ejecutar JavaScript) de una página para extraer información estructurada: precios, noticias, imágenes, tablas, etc.

Este tutorial te guiará desde los conceptos más básicos hasta técnicas avanzadas, usando **Java** y el ecosistema de **Spring Boot**, con ejemplos prácticos y ejercicios resueltos.

> ⚠️ **Nota Legal y Ética**: Antes de hacer scraping de cualquier sitio web, revisa su archivo `robots.txt` (ej. `https://ejemplo.com/robots.txt`) y sus Términos de Servicio. Respeta siempre las restricciones indicadas, no sobrecargues los servidores con peticiones excesivas y utiliza esta técnica solo para fines legítimos y éticos.

---

## 📋 Tabla de Contenidos

1. [Conceptos Fundamentales](#1-conceptos-fundamentales)
2. [Configuración del Entorno](#2-configuración-del-entorno)
3. [Nivel Básico: Jsoup - Tu primer scraper](#3-nivel-básico-jsoup---tu-primer-scraper)
4. [Nivel Intermedio: Scraping con Spring Boot](#4-nivel-intermedio-scraping-con-spring-boot)
5. [Nivel Intermedio: Manejo de Sesiones y Autenticación](#5-nivel-intermedio-manejo-de-sesiones-y-autenticación)
6. [Nivel Avanzado: Selenium y páginas dinámicas (JavaScript)](#6-nivel-avanzado-selenium-y-páginas-dinámicas-javascript)
7. [Nivel Avanzado: Playwright para Java](#7-nivel-avanzado-playwright-para-java)
8. [Almacenamiento de Datos Extraídos](#8-almacenamiento-de-datos-extraídos)
9. [Manejo de Errores, Reintentos y Rate Limiting](#9-manejo-de-errores-reintentos-y-rate-limiting)
10. [Scraping Paralelo y Concurrente](#10-scraping-paralelo-y-concurrente)
11. [Proyecto Completo: Monitor de Precios](#11-proyecto-completo-monitor-de-precios)
12. [Ejercicios Resueltos](#12-ejercicios-resueltos)
13. [Buenas Prácticas y Anti-patrones](#13-buenas-prácticas-y-anti-patrones)
14. [Recursos Adicionales](#14-recursos-adicionales)

---

## 1. Conceptos Fundamentales

### ¿Qué es el Web Scraping?

El Web Scraping (también llamado _web data extraction_ o _web harvesting_) es el proceso de:

1. **Descargar** el contenido HTML de una URL
2. **Parsear** (analizar) la estructura HTML
3. **Extraer** los datos de interés usando selectores CSS, XPath u otras técnicas
4. **Almacenar** los datos en un formato útil (JSON, CSV, base de datos)

### Diferencia entre Scraping y Crawling

| Característica | Scraping | Crawling |
|---|---|---|
| **Objetivo** | Extraer datos de páginas concretas | Descubrir y visitar múltiples páginas |
| **Alcance** | Una o pocas páginas conocidas | Muchas páginas descubiertas dinámicamente |
| **Navegación** | No sigue enlaces (o los sigue limitadamente) | Sigue enlaces para descubrir nuevo contenido |
| **Ejemplo** | Extraer precio de un producto | Indexar todos los productos de una tienda |

### Arquitectura de un Scraper

```
┌─────────────────────────────────────────────────────────────┐
│                     SCRAPER PIPELINE                        │
│                                                             │
│  URL(s)  ──►  HTTP Client  ──►  HTML Parser  ──►  Extractor │
│                   │                  │               │      │
│                   │            (Jsoup/Selenium)       │      │
│                   │                  │               ▼      │
│              Headers/Cookies   DOM Navigation    Data Model │
│              User-Agent        CSS Selectors         │      │
│              Rate Limiting     XPath                 ▼      │
│                                               Storage/Export│
└─────────────────────────────────────────────────────────────┘
```

### Tecnologías Principales en Java para Scraping

| Biblioteca | Uso | Tipo de páginas |
|---|---|---|
| **Jsoup** | Parseo HTML y scraping básico | Páginas estáticas (HTML puro) |
| **Apache HttpClient** | Peticiones HTTP avanzadas | Estáticas con control fino |
| **OkHttp** | Cliente HTTP moderno | Estáticas con control fino |
| **Selenium WebDriver** | Automatización de navegador | Páginas dinámicas con JavaScript |
| **Playwright for Java** | Automatización moderna | Páginas dinámicas complejas |
| **HtmlUnit** | Navegador headless ligero | Páginas con JavaScript básico |

---

## 2. Configuración del Entorno

### Requisitos Previos

- Java 17 o superior
- Maven 3.6+ o Gradle 7+
- IDE (IntelliJ IDEA recomendado, Eclipse o VS Code)
- Spring Boot 3.x

### Crear Proyecto Spring Boot

Puedes crear el proyecto en [start.spring.io](https://start.spring.io) o con el CLI:

```bash
# Con Spring Initializr CLI
spring init \
  --dependencies=web,data-jpa,h2 \
  --build=maven \
  --java-version=17 \
  --name=scraper-demo \
  scraper-demo
```

### Configuración de `pom.xml`

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
        <relativePath/>
    </parent>

    <groupId>com.tutorial</groupId>
    <artifactId>scraper-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>scraper-demo</name>
    <description>Tutorial de Web Scraping con Spring Boot</description>

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

        <!-- Jsoup - HTML Parser -->
        <dependency>
            <groupId>org.jsoup</groupId>
            <artifactId>jsoup</artifactId>
            <version>1.17.2</version>
        </dependency>

        <!-- Selenium WebDriver -->
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>4.16.1</version>
        </dependency>

        <!-- WebDriverManager (descarga automática de drivers) -->
        <dependency>
            <groupId>io.github.bonigarcia</groupId>
            <artifactId>webdrivermanager</artifactId>
            <version>5.6.3</version>
        </dependency>

        <!-- Lombok (opcional, reduce boilerplate) -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <!-- Jackson para JSON -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>

        <!-- Apache Commons CSV (para exportar datos) -->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-csv</artifactId>
            <version>1.10.0</version>
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
scraper-demo/
├── src/
│   ├── main/
│   │   ├── java/com/tutorial/scraper/
│   │   │   ├── ScraperDemoApplication.java
│   │   │   ├── config/
│   │   │   │   └── ScraperConfig.java
│   │   │   ├── model/
│   │   │   │   └── Producto.java
│   │   │   ├── repository/
│   │   │   │   └── ProductoRepository.java
│   │   │   ├── service/
│   │   │   │   ├── ScraperService.java
│   │   │   │   └── ExportService.java
│   │   │   └── controller/
│   │   │       └── ScraperController.java
│   │   └── resources/
│   │       └── application.properties
│   └── test/
│       └── java/com/tutorial/scraper/
│           └── ScraperServiceTest.java
└── pom.xml
```

### Configuración `application.properties`

```properties
# Servidor
server.port=8080

# Base de datos H2 (en memoria, para desarrollo)
spring.datasource.url=jdbc:h2:mem:scraperdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true

# H2 Console (solo desarrollo)
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Configuración del scraper
scraper.user-agent=Mozilla/5.0 (compatible; ScraperBot/1.0)
scraper.timeout-ms=10000
scraper.delay-between-requests-ms=1000
scraper.max-retries=3
```

---

## 3. Nivel Básico: Jsoup - Tu primer scraper

### ¿Qué es Jsoup?

Jsoup es una biblioteca Java para trabajar con HTML real. Proporciona:
- Descarga de HTML desde URLs
- Parseo de HTML con una API similar a jQuery
- Selectores CSS para navegación del DOM
- Limpieza y saneamiento de HTML

### 3.1 Conceptos de Selectores CSS

Antes de empezar, es fundamental entender los selectores CSS:

```
Selector          Ejemplo        Descripción
─────────────────────────────────────────────────────────
tag               p              Todos los <p>
.clase            .precio        Elementos con class="precio"
#id               #titulo        Elemento con id="titulo"
[attr]            [href]         Elementos con atributo href
[attr=val]        [type=text]    Elementos donde type="text"
tag.clase         div.card       <div> con class="card"
padre hijo        div p          <p> dentro de cualquier <div>
padre > hijo      ul > li        <li> directo hijo de <ul>
elemento + sig    h1 + p         <p> inmediatamente después de <h1>
```

### 3.2 Primer Scraper: Extrayendo títulos de una web

```java
package com.tutorial.scraper.basico;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import java.io.IOException;

public class PrimerScraper {

    public static void main(String[] args) throws IOException {
        // 1. Conectar y descargar el HTML
        Document doc = Jsoup.connect("https://books.toscrape.com/")
                .userAgent("Mozilla/5.0 (compatible; ScraperDemo/1.0)")
                .timeout(10000)  // 10 segundos de timeout
                .get();

        // 2. Extraer el título de la página
        System.out.println("Título de la página: " + doc.title());

        // 3. Extraer todos los títulos de libros
        // En books.toscrape.com, los títulos están en: article.product_pod h3 a
        Elements titulos = doc.select("article.product_pod h3 a");

        System.out.println("\nLibros encontrados: " + titulos.size());
        System.out.println("─".repeat(50));

        for (Element titulo : titulos) {
            // El título completo está en el atributo "title"
            String nombre = titulo.attr("title");
            System.out.println("📚 " + nombre);
        }
    }
}
```

**Salida esperada:**
```
Título de la página: All products | Books to Scrape - Sandbox
Libros encontrados: 20
──────────────────────────────────────────────────
📚 A Light in the Attic
📚 Tipping the Velvet
📚 Soumission
...
```

### 3.3 Extraer múltiples campos

```java
package com.tutorial.scraper.basico;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

// Modelo simple de datos
record Libro(String titulo, String precio, String disponibilidad, int estrellas) {}

public class ScraperLibros {

    public static void main(String[] args) throws IOException {
        List<Libro> libros = extraerLibros("https://books.toscrape.com/");
        libros.forEach(System.out::println);
    }

    public static List<Libro> extraerLibros(String url) throws IOException {
        List<Libro> libros = new ArrayList<>();

        Document doc = Jsoup.connect(url)
                .userAgent("Mozilla/5.0")
                .timeout(10000)
                .get();

        // Cada libro está en un <article class="product_pod">
        Elements articulos = doc.select("article.product_pod");

        for (Element articulo : articulos) {
            // Título: en el atributo title del enlace dentro de h3
            String titulo = articulo.select("h3 a").attr("title");

            // Precio: en <p class="price_color">
            String precio = articulo.select("p.price_color").text();

            // Disponibilidad: en <p class="instock availability">
            String disponibilidad = articulo.select("p.instock.availability").text().trim();

            // Estrellas: la clase del <p class="star-rating One/Two/Three...">
            String claseEstrellas = articulo.select("p.star-rating").attr("class");
            int estrellas = convertirEstrellas(claseEstrellas);

            libros.add(new Libro(titulo, precio, disponibilidad, estrellas));
        }

        return libros;
    }

    private static int convertirEstrellas(String claseCSS) {
        // "star-rating Three" -> 3
        return switch (claseCSS) {
            case "star-rating One"   -> 1;
            case "star-rating Two"   -> 2;
            case "star-rating Three" -> 3;
            case "star-rating Four"  -> 4;
            case "star-rating Five"  -> 5;
            default -> 0;
        };
    }
}
```

### 3.4 Navegación entre páginas (paginación básica)

```java
package com.tutorial.scraper.basico;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class ScraperConPaginacion {

    private static final String BASE_URL = "https://books.toscrape.com/catalogue/";

    public static void main(String[] args) throws IOException {
        List<Libro> todosLosLibros = new ArrayList<>();

        // Empezar por la primera página
        String urlActual = "https://books.toscrape.com/catalogue/page-1.html";
        int pagina = 1;

        while (urlActual != null) {
            System.out.println("📄 Scrapeando página " + pagina + ": " + urlActual);

            Document doc = Jsoup.connect(urlActual)
                    .userAgent("Mozilla/5.0")
                    .timeout(10000)
                    .get();

            // Extraer libros de la página actual
            List<Libro> librosEnPagina = extraerLibrosDePagina(doc);
            todosLosLibros.addAll(librosEnPagina);
            System.out.println("  ✅ " + librosEnPagina.size() + " libros extraídos");

            // Buscar enlace a la siguiente página
            Element siguienteBoton = doc.select("li.next a").first();
            if (siguienteBoton != null) {
                urlActual = BASE_URL + siguienteBoton.attr("href");
                pagina++;

                // Pausa entre peticiones (¡importante para no sobrecargar el servidor!)
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            } else {
                urlActual = null; // No hay más páginas
            }
        }

        System.out.println("\n✅ Total libros extraídos: " + todosLosLibros.size());
    }

    private static List<Libro> extraerLibrosDePagina(Document doc) {
        List<Libro> libros = new ArrayList<>();
        Elements articulos = doc.select("article.product_pod");

        for (Element articulo : articulos) {
            String titulo = articulo.select("h3 a").attr("title");
            String precio = articulo.select("p.price_color").text();
            String disponibilidad = articulo.select("p.instock.availability").text().trim();
            String claseEstrellas = articulo.select("p.star-rating").attr("class");
            int estrellas = convertirEstrellas(claseEstrellas);
            libros.add(new Libro(titulo, precio, disponibilidad, estrellas));
        }

        return libros;
    }

    private static int convertirEstrellas(String claseCSS) {
        return switch (claseCSS) {
            case "star-rating One"   -> 1;
            case "star-rating Two"   -> 2;
            case "star-rating Three" -> 3;
            case "star-rating Four"  -> 4;
            case "star-rating Five"  -> 5;
            default -> 0;
        };
    }
}
```

### 3.5 Parsear HTML local (para pruebas)

```java
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;

public class ParsearHTMLLocal {
    public static void main(String[] args) {
        String html = """
            <html>
              <body>
                <div class="productos">
                  <div class="producto" data-id="1">
                    <h2 class="nombre">Laptop Pro</h2>
                    <span class="precio">1299.99</span>
                    <span class="stock">En stock</span>
                  </div>
                  <div class="producto" data-id="2">
                    <h2 class="nombre">Mouse Inalámbrico</h2>
                    <span class="precio">29.99</span>
                    <span class="stock">Agotado</span>
                  </div>
                </div>
              </body>
            </html>
            """;

        Document doc = Jsoup.parse(html);

        // Iterar sobre cada producto
        doc.select("div.producto").forEach(producto -> {
            String id = producto.attr("data-id");
            String nombre = producto.select(".nombre").text();
            String precio = producto.select(".precio").text();
            String stock = producto.select(".stock").text();
            System.out.printf("ID:%s | %s | $%s | %s%n", id, nombre, precio, stock);
        });
    }
}
```

---

## 4. Nivel Intermedio: Scraping con Spring Boot

### 4.1 Modelo de Datos

```java
package com.tutorial.scraper.model;

import jakarta.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "productos")
public class Producto {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String nombre;

    private String precio;

    private String url;

    private String categoria;

    @Column(name = "fecha_extraccion")
    private LocalDateTime fechaExtraccion;

    // Constructores
    public Producto() {}

    public Producto(String nombre, String precio, String url, String categoria) {
        this.nombre = nombre;
        this.precio = precio;
        this.url = url;
        this.categoria = categoria;
        this.fechaExtraccion = LocalDateTime.now();
    }

    // Getters y Setters
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }

    public String getNombre() { return nombre; }
    public void setNombre(String nombre) { this.nombre = nombre; }

    public String getPrecio() { return precio; }
    public void setPrecio(String precio) { this.precio = precio; }

    public String getUrl() { return url; }
    public void setUrl(String url) { this.url = url; }

    public String getCategoria() { return categoria; }
    public void setCategoria(String categoria) { this.categoria = categoria; }

    public LocalDateTime getFechaExtraccion() { return fechaExtraccion; }
    public void setFechaExtraccion(LocalDateTime fechaExtraccion) {
        this.fechaExtraccion = fechaExtraccion;
    }

    @Override
    public String toString() {
        return "Producto{id=%d, nombre='%s', precio='%s', url='%s'}"
            .formatted(id, nombre, precio, url);
    }
}
```

### 4.2 Repositorio JPA

```java
package com.tutorial.scraper.repository;

import com.tutorial.scraper.model.Producto;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;
import java.util.List;

@Repository
public interface ProductoRepository extends JpaRepository<Producto, Long> {

    List<Producto> findByCategoria(String categoria);

    List<Producto> findByNombreContainingIgnoreCase(String nombre);

    @Query("SELECT p FROM Producto p ORDER BY p.fechaExtraccion DESC")
    List<Producto> findAllOrderByFechaDesc();

    boolean existsByNombreAndUrl(String nombre, String url);
}
```

### 4.3 Configuración del Scraper

```java
package com.tutorial.scraper.config;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;

@Configuration
@ConfigurationProperties(prefix = "scraper")
public class ScraperConfig {

    private String userAgent = "Mozilla/5.0 (compatible; ScraperBot/1.0)";
    private int timeoutMs = 10000;
    private long delayBetweenRequestsMs = 1000;
    private int maxRetries = 3;

    // Getters y Setters
    public String getUserAgent() { return userAgent; }
    public void setUserAgent(String userAgent) { this.userAgent = userAgent; }

    public int getTimeoutMs() { return timeoutMs; }
    public void setTimeoutMs(int timeoutMs) { this.timeoutMs = timeoutMs; }

    public long getDelayBetweenRequestsMs() { return delayBetweenRequestsMs; }
    public void setDelayBetweenRequestsMs(long delay) { this.delayBetweenRequestsMs = delay; }

    public int getMaxRetries() { return maxRetries; }
    public void setMaxRetries(int maxRetries) { this.maxRetries = maxRetries; }
}
```

### 4.4 Servicio de Scraping

```java
package com.tutorial.scraper.service;

import com.tutorial.scraper.config.ScraperConfig;
import com.tutorial.scraper.model.Producto;
import com.tutorial.scraper.repository.ProductoRepository;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Service;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

@Service
public class ScraperService {

    private static final Logger log = LoggerFactory.getLogger(ScraperService.class);

    private final ScraperConfig config;
    private final ProductoRepository productoRepository;

    public ScraperService(ScraperConfig config, ProductoRepository productoRepository) {
        this.config = config;
        this.productoRepository = productoRepository;
    }

    /**
     * Descarga y parsea un documento HTML desde una URL.
     * Incluye manejo de reintentos.
     */
    public Document descargarPagina(String url) throws IOException {
        int intentos = 0;
        IOException ultimaExcepcion = null;

        while (intentos < config.getMaxRetries()) {
            try {
                log.debug("Descargando URL: {} (intento {})", url, intentos + 1);
                return Jsoup.connect(url)
                        .userAgent(config.getUserAgent())
                        .timeout(config.getTimeoutMs())
                        .followRedirects(true)
                        .ignoreHttpErrors(false)
                        .get();
            } catch (IOException e) {
                intentos++;
                ultimaExcepcion = e;
                log.warn("Error al descargar {} (intento {}): {}", url, intentos, e.getMessage());

                if (intentos < config.getMaxRetries()) {
                    esperarEntreReintentos(intentos);
                }
            }
        }

        throw ultimaExcepcion;
    }

    /**
     * Extrae productos de books.toscrape.com y los guarda en base de datos.
     */
    public List<Producto> scrapearLibros() throws IOException, InterruptedException {
        List<Producto> productosGuardados = new ArrayList<>();
        String urlActual = "https://books.toscrape.com/catalogue/page-1.html";

        while (urlActual != null) {
            log.info("Scrapeando: {}", urlActual);
            Document doc = descargarPagina(urlActual);

            List<Producto> productosEnPagina = extraerProductosDePagina(doc, urlActual);

            // Guardar solo los que no existen ya
            for (Producto p : productosEnPagina) {
                if (!productoRepository.existsByNombreAndUrl(p.getNombre(), p.getUrl())) {
                    productosGuardados.add(productoRepository.save(p));
                }
            }

            // Buscar siguiente página
            Element siguiente = doc.select("li.next a").first();
            if (siguiente != null) {
                urlActual = "https://books.toscrape.com/catalogue/" + siguiente.attr("href");
                Thread.sleep(config.getDelayBetweenRequestsMs());
            } else {
                urlActual = null;
            }
        }

        log.info("Scraping completado. Total productos guardados: {}", productosGuardados.size());
        return productosGuardados;
    }

    private List<Producto> extraerProductosDePagina(Document doc, String paginaUrl) {
        List<Producto> productos = new ArrayList<>();
        Elements articulos = doc.select("article.product_pod");

        for (Element articulo : articulos) {
            String nombre = articulo.select("h3 a").attr("title");
            String precio = articulo.select("p.price_color").text();
            String urlRelativa = articulo.select("h3 a").attr("href");
            String urlProducto = "https://books.toscrape.com/catalogue/" + urlRelativa;
            String categoria = "Libros";

            productos.add(new Producto(nombre, precio, urlProducto, categoria));
        }

        return productos;
    }

    private void esperarEntreReintentos(int intento) {
        long espera = (long) Math.pow(2, intento) * 1000; // Backoff exponencial
        log.debug("Esperando {}ms antes del reintento...", espera);
        try {
            Thread.sleep(espera);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

### 4.5 Controlador REST

```java
package com.tutorial.scraper.controller;

import com.tutorial.scraper.model.Producto;
import com.tutorial.scraper.repository.ProductoRepository;
import com.tutorial.scraper.service.ScraperService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;
import java.util.Map;

@RestController
@RequestMapping("/api/scraper")
public class ScraperController {

    private final ScraperService scraperService;
    private final ProductoRepository productoRepository;

    public ScraperController(ScraperService scraperService,
                              ProductoRepository productoRepository) {
        this.scraperService = scraperService;
        this.productoRepository = productoRepository;
    }

    /**
     * POST /api/scraper/iniciar
     * Inicia el proceso de scraping
     */
    @PostMapping("/iniciar")
    public ResponseEntity<Map<String, Object>> iniciarScraping() {
        try {
            List<Producto> productos = scraperService.scrapearLibros();
            return ResponseEntity.ok(Map.of(
                "estado", "completado",
                "productosGuardados", productos.size(),
                "mensaje", "Scraping completado exitosamente"
            ));
        } catch (Exception e) {
            return ResponseEntity.internalServerError().body(Map.of(
                "estado", "error",
                "mensaje", e.getMessage()
            ));
        }
    }

    /**
     * GET /api/scraper/productos
     * Retorna todos los productos extraídos
     */
    @GetMapping("/productos")
    public ResponseEntity<List<Producto>> obtenerProductos() {
        return ResponseEntity.ok(productoRepository.findAllOrderByFechaDesc());
    }

    /**
     * GET /api/scraper/productos/buscar?nombre=laptop
     * Busca productos por nombre
     */
    @GetMapping("/productos/buscar")
    public ResponseEntity<List<Producto>> buscarProductos(@RequestParam String nombre) {
        return ResponseEntity.ok(
            productoRepository.findByNombreContainingIgnoreCase(nombre)
        );
    }

    /**
     * GET /api/scraper/productos/categoria/{categoria}
     */
    @GetMapping("/productos/categoria/{categoria}")
    public ResponseEntity<List<Producto>> porCategoria(@PathVariable String categoria) {
        return ResponseEntity.ok(productoRepository.findByCategoria(categoria));
    }
}
```

---

## 5. Nivel Intermedio: Manejo de Sesiones y Autenticación

### 5.1 Scraping con cookies y cabeceras personalizadas

Muchos sitios requieren que te autentiques o que envíes cookies específicas:

```java
package com.tutorial.scraper.intermedio;

import org.jsoup.Connection;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

public class ScraperConSesion {

    private static final String LOGIN_URL = "https://example.com/login";
    private static final String TARGET_URL = "https://example.com/datos-privados";

    public static void main(String[] args) throws IOException {
        // Paso 1: Hacer login y obtener cookies de sesión
        Map<String, String> cookies = hacerLogin("usuario@ejemplo.com", "mipassword");

        if (cookies.isEmpty()) {
            System.err.println("Error: No se pudo hacer login");
            return;
        }

        System.out.println("✅ Login exitoso. Cookies: " + cookies.keySet());

        // Paso 2: Usar las cookies para acceder a páginas protegidas
        Document pagina = accederPaginaProtegida(TARGET_URL, cookies);
        System.out.println("Título: " + pagina.title());
    }

    private static Map<String, String> hacerLogin(String usuario, String password)
            throws IOException {
        // Primero, obtener el formulario de login para extraer tokens CSRF
        Connection.Response loginPage = Jsoup.connect(LOGIN_URL)
                .method(Connection.Method.GET)
                .userAgent("Mozilla/5.0")
                .execute();

        Document loginDoc = loginPage.parse();
        Map<String, String> cookiesIniciales = loginPage.cookies();

        // Extraer token CSRF del formulario (si existe)
        String csrfToken = loginDoc.select("input[name=_csrf]").attr("value");

        // Hacer POST con credenciales
        Map<String, String> formData = new HashMap<>();
        formData.put("username", usuario);
        formData.put("password", password);
        if (!csrfToken.isEmpty()) {
            formData.put("_csrf", csrfToken);
        }

        Connection.Response response = Jsoup.connect(LOGIN_URL)
                .method(Connection.Method.POST)
                .userAgent("Mozilla/5.0")
                .data(formData)
                .cookies(cookiesIniciales)
                .followRedirects(true)
                .execute();

        return response.cookies();
    }

    private static Document accederPaginaProtegida(String url, Map<String, String> cookies)
            throws IOException {
        return Jsoup.connect(url)
                .userAgent("Mozilla/5.0")
                .cookies(cookies)
                .timeout(10000)
                .get();
    }
}
```

### 5.2 Manejo de formularios y búsquedas

```java
package com.tutorial.scraper.intermedio;

import org.jsoup.Connection;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import java.io.IOException;

public class ScraperConFormulario {

    public static void main(String[] args) throws IOException {
        // Ejemplo: buscar en un sitio que usa formularios GET
        String resultados = buscarEnSitio("Java programming");
        System.out.println(resultados);
    }

    public static String buscarEnSitio(String termino) throws IOException {
        // Algunos sitios usan GET para búsquedas
        Document doc = Jsoup.connect("https://books.toscrape.com/catalogue/search.html")
                .data("q", termino)  // Parámetro de búsqueda
                .method(Connection.Method.GET)
                .userAgent("Mozilla/5.0")
                .timeout(10000)
                .get();

        StringBuilder resultado = new StringBuilder();
        Elements resultadosSearch = doc.select("article.product_pod");

        resultado.append("Resultados para '").append(termino).append("':\n");
        for (Element item : resultadosSearch) {
            resultado.append("- ").append(item.select("h3 a").attr("title")).append("\n");
        }

        return resultado.toString();
    }
}
```

### 5.3 Scraping de APIs JSON (REST)

Muchos sitios modernos cargan datos vía API REST. Es preferible usar la API directamente:

```java
package com.tutorial.scraper.intermedio;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import java.io.IOException;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.time.Duration;

public class ScraperAPI {

    private static final HttpClient httpClient = HttpClient.newBuilder()
            .connectTimeout(Duration.ofSeconds(10))
            .followRedirects(HttpClient.Redirect.ALWAYS)
            .build();

    private static final ObjectMapper objectMapper = new ObjectMapper();

    public static void main(String[] args) throws IOException, InterruptedException {
        // Ejemplo con JSONPlaceholder (API de prueba)
        String jsonRespuesta = hacerGetJSON("https://jsonplaceholder.typicode.com/posts?_limit=5");
        JsonNode posts = objectMapper.readTree(jsonRespuesta);

        System.out.println("Posts obtenidos:");
        posts.forEach(post -> {
            System.out.printf("  [%d] %s%n",
                post.get("id").asInt(),
                post.get("title").asText()
            );
        });
    }

    public static String hacerGetJSON(String url) throws IOException, InterruptedException {
        HttpRequest request = HttpRequest.newBuilder()
                .uri(URI.create(url))
                .header("User-Agent", "ScraperBot/1.0")
                .header("Accept", "application/json")
                .GET()
                .build();

        HttpResponse<String> response = httpClient.send(request,
                HttpResponse.BodyHandlers.ofString());

        if (response.statusCode() != 200) {
            throw new IOException("HTTP Error: " + response.statusCode());
        }

        return response.body();
    }

    /**
     * Técnica: encontrar la URL de la API a partir del HTML
     * Muchos sitios cargan datos de APIs internas
     */
    public static void encontrarAPIInterna(String url) throws IOException {
        Document doc = Jsoup.connect(url).get();

        // Buscar scripts que contengan la URL de la API
        doc.select("script").forEach(script -> {
            String contenido = script.html();
            if (contenido.contains("api") || contenido.contains("json")) {
                // Extraer URLs con regex básico
                System.out.println("Script encontrado con posible API:");
                System.out.println(contenido.substring(0, Math.min(200, contenido.length())));
            }
        });

        // Buscar atributos data-* que contengan URLs
        doc.select("[data-url], [data-api], [data-endpoint]").forEach(el -> {
            System.out.println("Data attribute encontrado: " + el.toString());
        });
    }
}
```

---

## 6. Nivel Avanzado: Selenium y páginas dinámicas (JavaScript)

### ¿Cuándo usar Selenium?

Jsoup solo obtiene el HTML inicial. Si el sitio usa JavaScript para renderizar contenido (React, Vue, Angular), necesitas un navegador real. Selenium controla Chrome/Firefox programáticamente.

### 6.1 Configuración de Selenium con WebDriverManager

```java
package com.tutorial.scraper.avanzado;

import io.github.bonigarcia.wdm.WebDriverManager;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;
import java.util.List;

public class ScraperSeleniumBasico {

    public static void main(String[] args) {
        // WebDriverManager descarga el ChromeDriver automáticamente
        WebDriverManager.chromedriver().setup();

        // Opciones del navegador
        ChromeOptions options = new ChromeOptions();
        options.addArguments("--headless");          // Sin interfaz gráfica
        options.addArguments("--no-sandbox");
        options.addArguments("--disable-dev-shm-usage");
        options.addArguments("--disable-gpu");
        options.addArguments("--window-size=1920,1080");
        options.addArguments("--user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
                           + "AppleWebKit/537.36 Chrome/120.0 Safari/537.36");

        WebDriver driver = new ChromeDriver(options);

        try {
            // Navegar a la página
            driver.get("https://books.toscrape.com/");

            // Esperar a que cargue el contenido
            WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
            wait.until(ExpectedConditions.presenceOfElementLocated(
                    By.cssSelector("article.product_pod")));

            // Extraer datos
            List<WebElement> libros = driver.findElements(By.cssSelector("article.product_pod"));
            System.out.println("Libros encontrados: " + libros.size());

            for (WebElement libro : libros) {
                String titulo = libro.findElement(By.cssSelector("h3 a")).getAttribute("title");
                String precio = libro.findElement(By.cssSelector("p.price_color")).getText();
                System.out.println("📚 " + titulo + " - " + precio);
            }

        } finally {
            driver.quit(); // Siempre cerrar el navegador
        }
    }
}
```

### 6.2 Scraper avanzado: esperas, scroll y clics

```java
package com.tutorial.scraper.avanzado;

import io.github.bonigarcia.wdm.WebDriverManager;
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.interactions.Actions;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.Select;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;
import java.util.List;

public class ScraperSeleniumAvanzado {

    private WebDriver driver;
    private WebDriverWait wait;

    public ScraperSeleniumAvanzado() {
        WebDriverManager.chromedriver().setup();
        ChromeOptions options = new ChromeOptions();
        options.addArguments("--headless", "--no-sandbox",
                "--disable-dev-shm-usage", "--disable-gpu");
        driver = new ChromeDriver(options);
        wait = new WebDriverWait(driver, Duration.ofSeconds(15));
    }

    /**
     * Scroll hasta el final de la página (para páginas con carga infinita)
     */
    public void scrollHastaFinal() throws InterruptedException {
        JavascriptExecutor js = (JavascriptExecutor) driver;
        long alturaAnterior = 0;

        while (true) {
            // Scroll al final
            js.executeScript("window.scrollTo(0, document.body.scrollHeight);");
            Thread.sleep(2000); // Esperar que cargue nuevo contenido

            long alturaActual = (Long) js.executeScript(
                "return document.body.scrollHeight;");

            if (alturaActual == alturaAnterior) {
                break; // No hay más contenido
            }
            alturaAnterior = alturaActual;
        }
    }

    /**
     * Hacer clic en un elemento, esperando a que sea clickeable
     */
    public void hacerClic(By selector) {
        WebElement elemento = wait.until(
            ExpectedConditions.elementToBeClickable(selector));
        elemento.click();
    }

    /**
     * Seleccionar una opción en un dropdown
     */
    public void seleccionarOpcion(By selectSelector, String valor) {
        WebElement selectElement = wait.until(
            ExpectedConditions.presenceOfElementLocated(selectSelector));
        Select select = new Select(selectElement);
        select.selectByVisibleText(valor);
    }

    /**
     * Esperar a que un elemento DESAPAREZCA (útil para spinners de carga)
     */
    public void esperarQueDesaparezca(By selector) {
        wait.until(ExpectedConditions.invisibilityOfElementLocated(selector));
    }

    /**
     * Extraer texto de elemento con espera implícita
     */
    public String extraerTexto(By selector) {
        return wait.until(ExpectedConditions.visibilityOfElementLocated(selector))
                   .getText();
    }

    /**
     * Ejecutar JavaScript directamente
     */
    public Object ejecutarJS(String script, Object... args) {
        JavascriptExecutor js = (JavascriptExecutor) driver;
        return js.executeScript(script, args);
    }

    /**
     * Tomar captura de pantalla (útil para debugging)
     */
    public byte[] tomarCaptura() {
        return ((TakesScreenshot) driver).getScreenshotAs(OutputType.BYTES);
    }

    public void cerrar() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

### 6.3 Page Object Model (POM) - Patrón de diseño para Selenium

El patrón Page Object Model organiza el código de scraping por páginas:

```java
package com.tutorial.scraper.avanzado.pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;
import java.util.List;
import java.util.stream.Collectors;

// Clase que representa la página de catálogo
public class CatalogoPagina {

    private final WebDriver driver;
    private final WebDriverWait wait;

    // Selectores definidos como constantes
    private static final By ARTICULOS = By.cssSelector("article.product_pod");
    private static final By TITULO    = By.cssSelector("h3 a");
    private static final By PRECIO    = By.cssSelector("p.price_color");
    private static final By SIGUIENTE = By.cssSelector("li.next a");

    public CatalogoPagina(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }

    public void navegar(String url) {
        driver.get(url);
        wait.until(ExpectedConditions.presenceOfElementLocated(ARTICULOS));
    }

    public record LibroInfo(String titulo, String precio) {}

    public List<LibroInfo> obtenerLibros() {
        List<WebElement> articulos = driver.findElements(ARTICULOS);
        return articulos.stream()
            .map(articulo -> new LibroInfo(
                articulo.findElement(TITULO).getAttribute("title"),
                articulo.findElement(PRECIO).getText()
            ))
            .collect(Collectors.toList());
    }

    public boolean hayPaginaSiguiente() {
        return !driver.findElements(SIGUIENTE).isEmpty();
    }

    public void irAPaginaSiguiente() {
        driver.findElement(SIGUIENTE).click();
        wait.until(ExpectedConditions.stalenessOf(
            driver.findElements(ARTICULOS).get(0)));
        wait.until(ExpectedConditions.presenceOfElementLocated(ARTICULOS));
    }
}
```

---

## 7. Nivel Avanzado: Playwright para Java

Playwright es una alternativa moderna a Selenium, con mejor soporte para páginas dinámicas:

```java
package com.tutorial.scraper.avanzado;

import com.microsoft.playwright.*;
import com.microsoft.playwright.options.WaitForSelectorState;
import java.util.List;

public class ScraperPlaywright {

    public static void main(String[] args) {
        try (Playwright playwright = Playwright.create()) {
            Browser browser = playwright.chromium().launch(
                new BrowserType.LaunchOptions().setHeadless(true)
            );

            BrowserContext context = browser.newContext(
                new Browser.NewContextOptions()
                    .setUserAgent("Mozilla/5.0 (compatible; ScraperBot/1.0)")
            );

            Page page = context.newPage();

            // Navegar y esperar carga
            page.navigate("https://books.toscrape.com/");
            page.waitForSelector("article.product_pod");

            // Extraer datos con selectores CSS
            List<String> titulos = page.querySelectorAll("article.product_pod h3 a")
                .stream()
                .map(el -> el.getAttribute("title"))
                .toList();

            List<String> precios = page.querySelectorAll("p.price_color")
                .stream()
                .map(ElementHandle::textContent)
                .toList();

            System.out.println("Libros encontrados: " + titulos.size());
            for (int i = 0; i < titulos.size(); i++) {
                System.out.printf("📚 %s - %s%n", titulos.get(i), precios.get(i));
            }

            // Ejemplo: esperar una red request específica
            // page.waitForResponse("**/api/data*", () -> page.click("#boton"));

            browser.close();
        }
    }
}
```

---

## 8. Almacenamiento de Datos Extraídos

### 8.1 Exportar a CSV

```java
package com.tutorial.scraper.service;

import com.tutorial.scraper.model.Producto;
import org.apache.commons.csv.CSVFormat;
import org.apache.commons.csv.CSVPrinter;
import org.springframework.stereotype.Service;
import java.io.*;
import java.util.List;

@Service
public class ExportService {

    /**
     * Exportar lista de productos a CSV
     */
    public byte[] exportarCSV(List<Producto> productos) throws IOException {
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        BufferedWriter writer = new BufferedWriter(new OutputStreamWriter(baos, "UTF-8"));

        CSVFormat format = CSVFormat.DEFAULT.builder()
            .setHeader("ID", "Nombre", "Precio", "Categoría", "URL", "Fecha Extracción")
            .build();

        try (CSVPrinter printer = new CSVPrinter(writer, format)) {
            for (Producto p : productos) {
                printer.printRecord(
                    p.getId(),
                    p.getNombre(),
                    p.getPrecio(),
                    p.getCategoria(),
                    p.getUrl(),
                    p.getFechaExtraccion()
                );
            }
        }

        return baos.toByteArray();
    }

    /**
     * Exportar a JSON
     */
    public String exportarJSON(List<Producto> productos) throws IOException {
        com.fasterxml.jackson.databind.ObjectMapper mapper =
            new com.fasterxml.jackson.databind.ObjectMapper();
        mapper.findAndRegisterModules(); // Para LocalDateTime
        return mapper.writerWithDefaultPrettyPrinter().writeValueAsString(productos);
    }
}
```

### 8.2 Endpoint para descarga de archivos

```java
@GetMapping("/productos/exportar/csv")
public ResponseEntity<byte[]> exportarCSV() throws IOException {
    List<Producto> productos = productoRepository.findAll();
    byte[] csv = exportService.exportarCSV(productos);

    return ResponseEntity.ok()
        .header("Content-Disposition", "attachment; filename=productos.csv")
        .header("Content-Type", "text/csv; charset=UTF-8")
        .body(csv);
}

@GetMapping("/productos/exportar/json")
public ResponseEntity<String> exportarJSON() throws IOException {
    List<Producto> productos = productoRepository.findAll();
    String json = exportService.exportarJSON(productos);

    return ResponseEntity.ok()
        .header("Content-Disposition", "attachment; filename=productos.json")
        .header("Content-Type", "application/json")
        .body(json);
}
```

---

## 9. Manejo de Errores, Reintentos y Rate Limiting

### 9.1 Rate Limiter con Bucket4j (Spring Boot)

```java
package com.tutorial.scraper.config;

import io.github.bucket4j.Bandwidth;
import io.github.bucket4j.Bucket;
import io.github.bucket4j.Refill;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.time.Duration;

@Configuration
public class RateLimiterConfig {

    @Bean
    public Bucket scraperBucket() {
        // Permite máximo 10 peticiones por minuto
        Bandwidth limit = Bandwidth.classic(10, Refill.greedy(10, Duration.ofMinutes(1)));
        return Bucket.builder()
                .addLimit(limit)
                .build();
    }
}
```

### 9.2 Servicio con Rate Limiting

```java
@Service
public class RateLimitedScraperService {

    private final Bucket bucket;

    public RateLimitedScraperService(Bucket bucket) {
        this.bucket = bucket;
    }

    public Document descargarConRateLimit(String url) throws IOException, InterruptedException {
        // Esperar hasta que haya tokens disponibles
        bucket.asBlocking().consume(1);

        return Jsoup.connect(url)
                .userAgent("Mozilla/5.0")
                .timeout(10000)
                .get();
    }
}
```

### 9.3 Manejo Robusto de Errores HTTP

```java
package com.tutorial.scraper.service;

import org.jsoup.HttpStatusException;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.io.IOException;
import java.net.SocketTimeoutException;

public class ScraperConManejoErrores {

    private static final Logger log = LoggerFactory.getLogger(ScraperConManejoErrores.class);

    public Document descargar(String url) throws IOException {
        try {
            return Jsoup.connect(url)
                    .userAgent("Mozilla/5.0")
                    .timeout(10000)
                    .ignoreHttpErrors(false) // Lanza excepción en errores HTTP
                    .get();

        } catch (HttpStatusException e) {
            int statusCode = e.getStatusCode();
            log.error("Error HTTP {} al acceder a: {}", statusCode, url);

            if (statusCode == 404) {
                throw new RuntimeException("Página no encontrada: " + url, e);
            } else if (statusCode == 403) {
                throw new RuntimeException("Acceso prohibido (403). ¿Necesitas autenticación?", e);
            } else if (statusCode == 429) {
                log.warn("Rate limit alcanzado (429). Esperando 60 segundos...");
                try {
                    Thread.sleep(60000);
                } catch (InterruptedException ie) {
                    Thread.currentThread().interrupt();
                }
                return descargar(url); // Reintentar
            } else if (statusCode >= 500) {
                throw new RuntimeException("Error del servidor (" + statusCode + "): " + url, e);
            }
            throw e;

        } catch (SocketTimeoutException e) {
            log.warn("Timeout al acceder a: {}", url);
            throw new IOException("Timeout de conexión: " + url, e);
        }
    }
}
```

---

## 10. Scraping Paralelo y Concurrente

### 10.1 Scraping con CompletableFuture

```java
package com.tutorial.scraper.avanzado;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;
import java.io.IOException;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.stream.Collectors;

@Service
public class ScraperParalelo {

    // Pool de hilos para peticiones concurrentes
    private final ExecutorService executor = Executors.newFixedThreadPool(5);

    /**
     * Descarga múltiples URLs en paralelo
     */
    public List<Document> descargarEnParalelo(List<String> urls) {
        List<CompletableFuture<Document>> futuros = urls.stream()
            .map(url -> CompletableFuture.supplyAsync(() -> {
                try {
                    Thread.sleep(200); // Pequeña pausa para no saturar
                    return Jsoup.connect(url)
                            .userAgent("Mozilla/5.0")
                            .timeout(10000)
                            .get();
                } catch (IOException | InterruptedException e) {
                    throw new RuntimeException("Error al descargar: " + url, e);
                }
            }, executor))
            .collect(Collectors.toList());

        // Esperar a que todos terminen
        CompletableFuture<Void> todos = CompletableFuture.allOf(
            futuros.toArray(new CompletableFuture[0]));

        todos.join();

        return futuros.stream()
            .map(CompletableFuture::join)
            .collect(Collectors.toList());
    }

    /**
     * Método asíncrono con Spring @Async
     */
    @Async
    public CompletableFuture<List<String>> extraerEnlacesAsync(String url) {
        try {
            Document doc = Jsoup.connect(url).get();
            List<String> enlaces = doc.select("a[href]").stream()
                .map(el -> el.attr("abs:href"))
                .filter(href -> href.startsWith("https://"))
                .distinct()
                .collect(Collectors.toList());
            return CompletableFuture.completedFuture(enlaces);
        } catch (IOException e) {
            return CompletableFuture.failedFuture(e);
        }
    }
}
```

### 10.2 Scraping programado con Spring Scheduler

```java
package com.tutorial.scraper.scheduler;

import com.tutorial.scraper.service.ScraperService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class ScraperScheduler {

    private static final Logger log = LoggerFactory.getLogger(ScraperScheduler.class);
    private final ScraperService scraperService;

    public ScraperScheduler(ScraperService scraperService) {
        this.scraperService = scraperService;
    }

    /**
     * Ejecutar scraping cada 6 horas
     * Formato cron: segundo minuto hora día-mes mes día-semana
     */
    @Scheduled(cron = "0 0 */6 * * *")
    public void scraperProgramado() {
        log.info("⏰ Iniciando scraping programado...");
        try {
            scraperService.scrapearLibros();
            log.info("✅ Scraping programado completado");
        } catch (Exception e) {
            log.error("❌ Error en scraping programado: {}", e.getMessage(), e);
        }
    }

    /**
     * Ejecutar cada 30 minutos durante horario laboral (8am - 6pm)
     */
    @Scheduled(cron = "0 */30 8-18 * * MON-FRI")
    public void scraperHorarioLaboral() {
        log.info("⏰ Scraping en horario laboral...");
        // ...lógica de scraping...
    }
}
```

---

## 11. Proyecto Completo: Monitor de Precios

Este proyecto demuestra un sistema completo de monitoreo de precios usando scraping:

### 11.1 Arquitectura del Proyecto

```
┌─────────────────────────────────────────────────────┐
│              MONITOR DE PRECIOS                     │
│                                                     │
│  Scheduler ──► ScraperService ──► Parser            │
│      │              │               │               │
│   (cada N hrs)  Jsoup/Selenium   Selectores CSS     │
│                      │               │               │
│                 PriceHistory    ProductoDTO          │
│                      │               │               │
│                 Repository ◄── Mapper ◄──────────────│
│                      │                              │
│                      ▼                              │
│              AlertService ──► Email/Webhook         │
│                      │                              │
│              REST API ──► Frontend/Dashboard        │
└─────────────────────────────────────────────────────┘
```

### 11.2 Entidad de Historial de Precios

```java
@Entity
@Table(name = "historial_precios")
public class HistorialPrecio {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String productoNombre;
    private String productoUrl;
    private Double precio;
    private LocalDateTime fecha;

    // Constructor
    public HistorialPrecio(String nombre, String url, Double precio) {
        this.productoNombre = nombre;
        this.productoUrl = url;
        this.precio = precio;
        this.fecha = LocalDateTime.now();
    }

    // Getters/Setters omitidos por brevedad
}
```

### 11.3 Servicio de Alertas

```java
@Service
public class AlertaService {

    private static final Logger log = LoggerFactory.getLogger(AlertaService.class);
    private final HistorialPrecioRepository historialRepo;

    public AlertaService(HistorialPrecioRepository historialRepo) {
        this.historialRepo = historialRepo;
    }

    /**
     * Detecta si el precio ha bajado más de un umbral
     */
    public boolean precioHaBajado(String url, Double precioActual, double umbralPorcentaje) {
        return historialRepo.findUltimoPrecio(url)
            .map(historial -> {
                double precioAnterior = historial.getPrecio();
                double cambio = ((precioAnterior - precioActual) / precioAnterior) * 100;
                if (cambio >= umbralPorcentaje) {
                    log.info("🔔 ¡Precio bajó {}% para {}! Antes: {} Ahora: {}",
                        String.format("%.1f", cambio), url, precioAnterior, precioActual);
                    return true;
                }
                return false;
            })
            .orElse(false);
    }
}
```

---

## 12. Ejercicios Resueltos

### Ejercicio 1: Extraer noticias de una web informativa

**Enunciado**: Extraer los títulos, resúmenes y fechas de los últimos artículos de `https://news.ycombinator.com/` (Hacker News).

**Solución:**

```java
package com.tutorial.scraper.ejercicios;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class Ejercicio1_HackerNews {

    record Noticia(String titulo, String url, int puntos, String autor) {}

    public static void main(String[] args) throws IOException {
        List<Noticia> noticias = extraerNoticias();
        System.out.printf("%-5s %-60s %-8s%n", "Pts", "Título", "Autor");
        System.out.println("─".repeat(80));
        noticias.forEach(n ->
            System.out.printf("%-5d %-60s %-8s%n",
                n.puntos(),
                n.titulo().length() > 57 ? n.titulo().substring(0, 57) + "..." : n.titulo(),
                n.autor()
            )
        );
    }

    public static List<Noticia> extraerNoticias() throws IOException {
        List<Noticia> noticias = new ArrayList<>();

        Document doc = Jsoup.connect("https://news.ycombinator.com/")
                .userAgent("Mozilla/5.0")
                .timeout(10000)
                .get();

        // En HN, cada noticia ocupa dos filas <tr>
        Elements filasTitulo = doc.select("tr.athing");

        for (Element fila : filasTitulo) {
            // El título está en .titleline > a
            Element enlaceTitulo = fila.select(".titleline > a").first();
            if (enlaceTitulo == null) continue;

            String titulo = enlaceTitulo.text();
            String url = enlaceTitulo.attr("href");

            // La fila siguiente contiene puntos y autor
            Element filaMeta = fila.nextElementSibling();
            if (filaMeta == null) continue;

            String puntosTexto = filaMeta.select(".score").text();
            int puntos = puntosTexto.isEmpty() ? 0 :
                Integer.parseInt(puntosTexto.replace(" points", "").replace(" point", ""));

            String autor = filaMeta.select(".hnuser").text();

            noticias.add(new Noticia(titulo, url, puntos, autor));
        }

        return noticias;
    }
}
```

---

### Ejercicio 2: Scraper con paginación completa y exportación CSV

**Enunciado**: Extraer TODOS los libros de `books.toscrape.com` (50 páginas, ~1000 libros) y exportarlos a CSV.

**Solución:**

```java
package com.tutorial.scraper.ejercicios;

import org.apache.commons.csv.CSVFormat;
import org.apache.commons.csv.CSVPrinter;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import java.io.*;
import java.util.ArrayList;
import java.util.List;

public class Ejercicio2_LibrosCSV {

    record Libro(String titulo, String precio, int estrellas, String disponibilidad,
                 String urlDetalle) {}

    public static void main(String[] args) throws IOException, InterruptedException {
        System.out.println("📚 Iniciando extracción de todos los libros...");
        List<Libro> libros = extraerTodosLosLibros();
        System.out.println("✅ Total extraídos: " + libros.size());

        exportarCSV(libros, "libros_completo.csv");
        System.out.println("💾 Exportado a libros_completo.csv");
    }

    public static List<Libro> extraerTodosLosLibros() throws IOException, InterruptedException {
        List<Libro> todos = new ArrayList<>();
        String url = "https://books.toscrape.com/catalogue/page-1.html";
        int pagina = 1;

        while (url != null) {
            System.out.printf("  Página %d: %s%n", pagina, url);

            Document doc = Jsoup.connect(url)
                    .userAgent("Mozilla/5.0").timeout(10000).get();

            todos.addAll(extraerDePagina(doc));

            Element siguiente = doc.select("li.next a").first();
            url = (siguiente != null)
                ? "https://books.toscrape.com/catalogue/" + siguiente.attr("href")
                : null;

            pagina++;
            Thread.sleep(300); // Pausa cortés
        }

        return todos;
    }

    private static List<Libro> extraerDePagina(Document doc) {
        List<Libro> libros = new ArrayList<>();
        for (Element art : doc.select("article.product_pod")) {
            String titulo = art.select("h3 a").attr("title");
            String precio = art.select("p.price_color").text();
            String claseStar = art.select("p.star-rating").attr("class");
            int estrellas = convertirEstrellas(claseStar);
            String disp = art.select("p.instock.availability").text().trim();
            String urlRelativa = art.select("h3 a").attr("href");
            String urlDetalle = "https://books.toscrape.com/catalogue/" + urlRelativa;
            libros.add(new Libro(titulo, precio, estrellas, disp, urlDetalle));
        }
        return libros;
    }

    public static void exportarCSV(List<Libro> libros, String archivo)
            throws IOException {
        try (Writer writer = new FileWriter(archivo);
             CSVPrinter printer = new CSVPrinter(writer,
                 CSVFormat.DEFAULT.builder()
                     .setHeader("Título", "Precio", "Estrellas", "Disponibilidad", "URL")
                     .build())) {
            for (Libro l : libros) {
                printer.printRecord(
                    l.titulo(), l.precio(), l.estrellas(), l.disponibilidad(), l.urlDetalle());
            }
        }
    }

    private static int convertirEstrellas(String claseCSS) {
        return switch (claseCSS) {
            case "star-rating One"   -> 1;
            case "star-rating Two"   -> 2;
            case "star-rating Three" -> 3;
            case "star-rating Four"  -> 4;
            case "star-rating Five"  -> 5;
            default -> 0;
        };
    }
}
```

---

### Ejercicio 3: Scraper de tabla HTML y conversión a JSON

**Enunciado**: Extraer una tabla HTML de datos (por ejemplo, una tabla de Wikipedia) y convertirla a JSON.

**Solución:**

```java
package com.tutorial.scraper.ejercicios;

import com.fasterxml.jackson.databind.ObjectMapper;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import java.io.IOException;
import java.util.ArrayList;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;

public class Ejercicio3_TablaAJSON {

    public static void main(String[] args) throws IOException {
        // Extraer tabla de países de Wikipedia
        String url = "https://en.wikipedia.org/wiki/List_of_countries_by_population_(United_Nations)";
        List<Map<String, String>> tabla = extraerPrimerTabla(url);

        // Mostrar solo los primeros 10 registros
        ObjectMapper mapper = new ObjectMapper();
        System.out.println(mapper.writerWithDefaultPrettyPrinter()
            .writeValueAsString(tabla.subList(0, Math.min(10, tabla.size()))));
    }

    public static List<Map<String, String>> extraerPrimerTabla(String url) throws IOException {
        Document doc = Jsoup.connect(url)
                .userAgent("Mozilla/5.0")
                .timeout(15000)
                .get();

        // Buscar la primera tabla
        Element tabla = doc.select("table.wikitable").first();
        if (tabla == null) {
            throw new RuntimeException("No se encontró ninguna tabla en: " + url);
        }

        // Extraer encabezados
        List<String> encabezados = new ArrayList<>();
        for (Element th : tabla.select("thead th, tr:first-child th")) {
            encabezados.add(limpiarTexto(th.text()));
        }

        // Extraer filas de datos
        List<Map<String, String>> filas = new ArrayList<>();
        for (Element fila : tabla.select("tbody tr")) {
            Elements celdas = fila.select("td, th");
            if (celdas.isEmpty()) continue;

            Map<String, String> registro = new LinkedHashMap<>();
            for (int i = 0; i < Math.min(encabezados.size(), celdas.size()); i++) {
                registro.put(encabezados.get(i), limpiarTexto(celdas.get(i).text()));
            }
            filas.add(registro);
        }

        return filas;
    }

    private static String limpiarTexto(String texto) {
        // Eliminar referencias de Wikipedia como [1], [2], etc.
        return texto.replaceAll("\\[\\d+\\]", "").trim();
    }
}
```

---

### Ejercicio 4: Scraper con Spring Boot y API REST

**Enunciado**: Crear un endpoint REST que reciba una URL y devuelva todos los metadatos SEO de esa página (título, descripción, keywords, OG tags).

**Solución:**

```java
package com.tutorial.scraper.ejercicios;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.springframework.web.bind.annotation.*;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

@RestController
@RequestMapping("/api/seo")
public class Ejercicio4_AnalyzadorSEO {

    /**
     * GET /api/seo/analizar?url=https://ejemplo.com
     */
    @GetMapping("/analizar")
    public Map<String, Object> analizarSEO(@RequestParam String url) throws IOException {
        Document doc = Jsoup.connect(url)
                .userAgent("Mozilla/5.0")
                .timeout(10000)
                .get();

        Map<String, Object> seo = new HashMap<>();

        // Título
        seo.put("title", doc.title());
        seo.put("titleLength", doc.title().length());

        // Meta tags básicos
        seo.put("description", getMetaContent(doc, "description"));
        seo.put("keywords", getMetaContent(doc, "keywords"));
        seo.put("robots", getMetaContent(doc, "robots"));
        seo.put("viewport", getMetaContent(doc, "viewport"));

        // Open Graph
        Map<String, String> og = new HashMap<>();
        doc.select("meta[property^=og:]").forEach(meta ->
            og.put(meta.attr("property"), meta.attr("content"))
        );
        seo.put("openGraph", og);

        // Twitter Cards
        Map<String, String> twitter = new HashMap<>();
        doc.select("meta[name^=twitter:]").forEach(meta ->
            twitter.put(meta.attr("name"), meta.attr("content"))
        );
        seo.put("twitterCard", twitter);

        // Conteo de encabezados
        Map<String, Integer> headings = new HashMap<>();
        for (String tag : new String[]{"h1", "h2", "h3", "h4", "h5", "h6"}) {
            headings.put(tag, doc.select(tag).size());
        }
        seo.put("headings", headings);

        // Imágenes sin alt
        long imagenesSinAlt = doc.select("img").stream()
            .filter(img -> img.attr("alt").isEmpty())
            .count();
        seo.put("imagenesSinAlt", imagenesSinAlt);

        // Total de enlaces
        seo.put("totalEnlaces", doc.select("a[href]").size());

        // Canonical URL
        Element canonical = doc.select("link[rel=canonical]").first();
        seo.put("canonical", canonical != null ? canonical.attr("href") : null);

        return seo;
    }

    private String getMetaContent(Document doc, String name) {
        Element meta = doc.select("meta[name=" + name + "]").first();
        return meta != null ? meta.attr("content") : "";
    }
}
```

---

### Ejercicio 5: Comparador de Precios con Alertas

**Enunciado**: Crear un sistema que monitoree el precio de un producto en un sitio y envíe una alerta cuando baje más del 10%.

**Solución:**

```java
package com.tutorial.scraper.ejercicios;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

@Component
public class Ejercicio5_MonitorPrecios {

    private static final Logger log = LoggerFactory.getLogger(Ejercicio5_MonitorPrecios.class);

    // Almacenamiento simple en memoria (en producción usar base de datos)
    private final Map<String, Double> ultimosPrecios = new HashMap<>();

    // Lista de productos a monitorear: nombre -> URL
    private static final Map<String, String> PRODUCTOS_A_MONITOREAR = Map.of(
        "Libro Python",
        "https://books.toscrape.com/catalogue/learning-python_20/index.html"
    );

    @Scheduled(fixedDelay = 3600000) // Cada hora
    public void monitorizarPrecios() {
        log.info("🔍 Iniciando monitoreo de precios...");

        PRODUCTOS_A_MONITOREAR.forEach((nombre, url) -> {
            try {
                double precioActual = obtenerPrecio(url);
                log.info("  📦 {}: £{}", nombre, precioActual);

                Double precioAnterior = ultimosPrecios.get(url);
                if (precioAnterior != null) {
                    double cambio = ((precioAnterior - precioActual) / precioAnterior) * 100;
                    if (cambio >= 10.0) {
                        enviarAlerta(nombre, url, precioAnterior, precioActual, cambio);
                    }
                }

                ultimosPrecios.put(url, precioActual);
            } catch (IOException e) {
                log.error("Error al obtener precio de {}: {}", nombre, e.getMessage());
            }
        });
    }

    private double obtenerPrecio(String url) throws IOException {
        Document doc = Jsoup.connect(url)
                .userAgent("Mozilla/5.0")
                .timeout(10000)
                .get();

        String precioTexto = doc.select("p.price_color").text();
        // Eliminar símbolo de moneda y convertir
        return Double.parseDouble(precioTexto.replaceAll("[^0-9.]", ""));
    }

    private void enviarAlerta(String nombre, String url, double anterior,
                               double actual, double cambio) {
        // En producción: enviar email, notificación push, webhook, etc.
        log.warn("🔔 ¡ALERTA DE PRECIO!");
        log.warn("   Producto: {}", nombre);
        log.warn("   URL: {}", url);
        log.warn("   Precio anterior: £{}", anterior);
        log.warn("   Precio actual: £{}", actual);
        log.warn("   Reducción: {}%", String.format("%.1f", cambio));
    }
}
```

---

## 13. Buenas Prácticas y Anti-patrones

### ✅ Buenas Prácticas

1. **Respeta el `robots.txt`**: Siempre verifica `https://sitio.com/robots.txt` antes de hacer scraping.

2. **Identifícate correctamente**: Usa un User-Agent descriptivo con información de contacto.
   ```java
   .userAgent("MiScraper/1.0 (contacto: tu@email.com)")
   ```

3. **Implementa delays**: No hagas peticiones demasiado rápido. Al menos 1 segundo entre peticiones.

4. **Maneja errores graciosamente**: No dejes que un error en una página detenga todo el proceso.

5. **Implementa caché**: Guarda los resultados para no tener que volver a descargar la misma página.

6. **Valida los datos extraídos**: Los selectores CSS pueden cambiar. Valida que los datos tienen el formato esperado.

7. **Usa selectores robustos**: Prefiere selectores que sean semánticamente estables (ids, roles, data-attributes) sobre los que dependen de la estructura del DOM.

8. **Registra todo (logging)**: Mantén logs detallados para poder diagnosticar problemas.

### ❌ Anti-patrones a Evitar

1. **No hacer flood**: Nunca hagas miles de peticiones en segundos. Puedes ser baneado o causar problemas al servidor.

2. **No ignorar `robots.txt`**: Es ilegal en algunos países y contrario a la ética.

3. **No guardar datos sensibles**: No extraigas ni almacenes datos personales sin consentimiento (GDPR/LOPD).

4. **No usar selectores frágiles**: Selectores como `div > div > div:nth-child(3) > span` se romperán con cualquier cambio de diseño.

5. **No olvidar cerrar recursos**: Siempre cierra Selenium WebDriver, conexiones HTTP, etc.

6. **No hardcodear credenciales**: Usa variables de entorno o un gestor de secretos.

### 🔒 Aspectos Legales y Éticos

```
⚠️  IMPORTANTE - Consideraciones Legales:

1. Los datos scrapeados pueden estar protegidos por derechos de autor
2. Los Términos de Servicio (ToS) de un sitio pueden prohibir el scraping
3. El GDPR (Europa) y LOPD (España) regulan el uso de datos personales
4. En algunos países, el scraping sin autorización puede ser ilegal
5. La sentencia hiQ vs. LinkedIn (EEUU) abrió debate legal sobre datos públicos

Recomendación: Siempre obtén permiso explícito cuando sea posible.
Preferiblemente, usa APIs oficiales si están disponibles.
```

---

## 14. Recursos Adicionales

### Documentación Oficial
- [Jsoup Documentation](https://jsoup.org/cookbook/)
- [Selenium WebDriver Java](https://www.selenium.dev/documentation/webdriver/)
- [Playwright for Java](https://playwright.dev/java/)
- [Spring Boot Reference](https://docs.spring.io/spring-boot/docs/current/reference/html/)

### Sitios de Práctica (diseñados para scraping)
- [books.toscrape.com](https://books.toscrape.com) - Tienda de libros ficticia
- [quotes.toscrape.com](https://quotes.toscrape.com) - Citas famosas
- [toscrape.com](https://toscrape.com) - Portal con varios sitios de práctica
- [scraping-club.com](https://scraping-club.com) - Ejercicios de práctica

### Herramientas Complementarias
- **Chrome DevTools** (F12): Para inspeccionar elementos y encontrar selectores CSS
- **XPath Helper** (extensión Chrome): Para probar selectores XPath
- **Postman/Insomnia**: Para explorar APIs REST que puedes scrapear
- **Wireshark**: Para analizar el tráfico de red de una aplicación

### Libros Recomendados
- "Web Scraping with Java" - Guía práctica
- "Mining the Social Web" - Para scraping de redes sociales
- "Clean Code" de Robert C. Martin - Para escribir código de scraping mantenible

---

> 📝 **Resumen del Tutorial**:
> Has aprendido desde los conceptos básicos de web scraping con Jsoup, pasando por la integración con Spring Boot (servicios, repositorios JPA, API REST), hasta técnicas avanzadas como Selenium para páginas dinámicas, scraping paralelo, rate limiting y patrones de diseño como Page Object Model. Con los ejercicios resueltos, tienes una base sólida para crear scrapers robustos y mantenibles en Java.
