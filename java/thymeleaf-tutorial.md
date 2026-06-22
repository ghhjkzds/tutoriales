# 🌿 Thymeleaf - Guía Completa y Detallada

## Introducción

**Thymeleaf** es un motor de plantillas moderno para Java del lado del servidor, diseñado para procesar HTML, XML, JavaScript, CSS y texto plano. Su gran ventaja es que las plantillas son **HTML válido** que puede visualizarse directamente en el navegador sin necesidad de ejecutar el servidor (concepto conocido como _Natural Templating_).

Es el motor de plantillas por defecto de **Spring Boot** y se integra perfectamente con **Spring MVC** y **Spring Security**.

---

## 📋 Tabla de Contenidos

1. [Conceptos Fundamentales](#1-conceptos-fundamentales)
2. [Configuración del Entorno](#2-configuración-del-entorno)
3. [Sintaxis Básica y Expresiones](#3-sintaxis-básica-y-expresiones)
4. [Atributos de Thymeleaf](#4-atributos-de-thymeleaf)
5. [Iteraciones con th:each](#5-iteraciones-con-theach)
6. [Condicionales](#6-condicionales)
7. [Formularios y Validación](#7-formularios-y-validación)
8. [Fragmentos y Layouts](#8-fragmentos-y-layouts)
9. [Internacionalización (i18n)](#9-internacionalización-i18n)
10. [Integración con Spring Security](#10-integración-con-spring-security)
11. [JavaScript y CSS con Thymeleaf](#11-javascript-y-css-con-thymeleaf)
12. [Utilidades y Objetos de Contexto](#12-utilidades-y-objetos-de-contexto)
13. [Thymeleaf Avanzado](#13-thymeleaf-avanzado)
14. [Proyecto Completo: Gestor de Tareas](#14-proyecto-completo-gestor-de-tareas)
15. [Buenas Prácticas y Rendimiento](#15-buenas-prácticas-y-rendimiento)
16. [Recursos Adicionales](#16-recursos-adicionales)

---

## 1. Conceptos Fundamentales

### ¿Qué es Thymeleaf?

Thymeleaf es un **motor de plantillas** que procesa archivos de plantilla y produce la salida HTML final. A diferencia de otros motores como JSP o FreeMarker, las plantillas de Thymeleaf son HTML estándar enriquecido con atributos `th:*` en el espacio de nombres `xmlns:th`.

```html
<!-- Plantilla Thymeleaf - es HTML válido -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Mi Aplicación</title>
</head>
<body>
    <p th:text="${mensaje}">Texto por defecto visible en el navegador</p>
</body>
</html>
```

### Natural Templating

Una de las características más poderosas de Thymeleaf es que las plantillas se pueden abrir directamente en el navegador como HTML estático, mostrando los valores por defecto definidos en los atributos:

```html
<!-- El texto "Hola, Mundo!" se muestra en el navegador sin servidor -->
<!-- Cuando el servidor procesa la plantilla, lo reemplaza con ${nombre} -->
<p th:text="${nombre}">Hola, Mundo!</p>
```

### Modos de Procesamiento

| Modo | Descripción | Extensión típica |
|---|---|---|
| **HTML** | Procesamiento de HTML5 (más común) | `.html` |
| **XML** | Procesamiento de XML estricto | `.xml` |
| **TEXT** | Plantillas de texto plano | `.txt` |
| **JAVASCRIPT** | Plantillas JavaScript | `.js` |
| **CSS** | Plantillas CSS | `.css` |
| **RAW** | Sin procesamiento | cualquiera |

### Cómo funciona Thymeleaf con Spring MVC

```
Navegador ──► Controlador Spring MVC ──► Modelo (datos)
                        │                      │
                        ▼                      │
              Motor Thymeleaf ◄────────────────┘
                        │
                        ▼
              Plantilla HTML + Datos ──► HTML final ──► Navegador
```

---

## 2. Configuración del Entorno

### Dependencias Maven

```xml
<!-- pom.xml -->
<dependencies>
    <!-- Spring Boot Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Thymeleaf -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>

    <!-- Spring Security + Thymeleaf (opcional) -->
    <dependency>
        <groupId>org.thymeleaf.extras</groupId>
        <artifactId>thymeleaf-extras-springsecurity6</artifactId>
    </dependency>

    <!-- Spring Data JPA (opcional, para ejemplos con BD) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Validación (opcional) -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <!-- H2 en memoria para pruebas (opcional) -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

### Dependencias Gradle

```groovy
// build.gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    implementation 'org.thymeleaf.extras:thymeleaf-extras-springsecurity6'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-validation'
    runtimeOnly 'com.h2database:h2'
}
```

### Configuración en application.properties

```properties
# application.properties

# Thymeleaf
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.encoding=UTF-8
spring.thymeleaf.mode=HTML
spring.thymeleaf.cache=false  # false en desarrollo, true en producción

# Para ver los cambios sin reiniciar (requiere spring-boot-devtools)
spring.devtools.restart.enabled=true
```

### Configuración en application.yml

```yaml
spring:
  thymeleaf:
    prefix: classpath:/templates/
    suffix: .html
    encoding: UTF-8
    mode: HTML
    cache: false
  devtools:
    restart:
      enabled: true
```

### Estructura de directorios

```
src/
└── main/
    ├── java/
    │   └── com/ejemplo/app/
    │       ├── AppApplication.java
    │       ├── controller/
    │       │   └── HomeController.java
    │       ├── model/
    │       │   └── Producto.java
    │       └── service/
    │           └── ProductoService.java
    └── resources/
        ├── templates/          ← Plantillas Thymeleaf (.html)
        │   ├── index.html
        │   ├── productos/
        │   │   ├── lista.html
        │   │   └── detalle.html
        │   └── fragments/
        │       ├── header.html
        │       └── footer.html
        ├── static/             ← Archivos estáticos (CSS, JS, imágenes)
        │   ├── css/
        │   ├── js/
        │   └── images/
        ├── i18n/               ← Archivos de internacionalización
        │   ├── messages.properties
        │   └── messages_es.properties
        └── application.properties
```

### Primer Controlador y Plantilla

```java
// HomeController.java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home(Model model) {
        model.addAttribute("titulo", "Bienvenido a Thymeleaf");
        model.addAttribute("mensaje", "Hola desde el servidor");
        return "index";  // Busca templates/index.html
    }
}
```

```html
<!-- templates/index.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title th:text="${titulo}">Título por Defecto</title>
</head>
<body>
    <h1 th:text="${titulo}">Título</h1>
    <p th:text="${mensaje}">Mensaje por defecto</p>
</body>
</html>
```

---

## 3. Sintaxis Básica y Expresiones

### Tipos de Expresiones

Thymeleaf tiene 5 tipos de expresiones estándar:

| Expresión | Sintaxis | Descripción |
|---|---|---|
| Variable | `${...}` | Accede a variables del modelo/contexto |
| Selección | `*{...}` | Accede a propiedades del objeto seleccionado |
| Mensaje | `#{...}` | Accede a mensajes de i18n |
| URL | `@{...}` | Construye URLs |
| Fragmento | `~{...}` | Referencia fragmentos de plantilla |

### Expresiones de Variable `${...}`

```html
<!-- Acceso a variables del modelo -->
<p th:text="${nombre}">Nombre</p>

<!-- Acceso a propiedades de objetos -->
<p th:text="${producto.nombre}">Producto</p>
<p th:text="${producto.precio}">Precio</p>

<!-- Llamada a métodos -->
<p th:text="${nombre.toUpperCase()}">NOMBRE</p>
<p th:text="${#strings.toUpperCase(nombre)}">NOMBRE</p>

<!-- Operaciones aritméticas -->
<p th:text="${precio * 1.21}">Precio con IVA</p>

<!-- Operaciones condicionales (Elvis operator) -->
<p th:text="${nombre != null ? nombre : 'Anónimo'}">Nombre</p>
<p th:text="${nombre ?: 'Anónimo'}">Nombre</p>  <!-- Equivalente -->

<!-- Encadenamiento seguro (null-safe) -->
<p th:text="${usuario?.direccion?.ciudad}">Ciudad</p>
```

### Expresiones de Selección `*{...}`

Se usan con `th:object` para seleccionar un objeto y acceder a sus propiedades:

```html
<!-- Sin expresión de selección -->
<div>
    <p th:text="${producto.nombre}">Nombre</p>
    <p th:text="${producto.precio}">Precio</p>
    <p th:text="${producto.categoria}">Categoría</p>
</div>

<!-- Con expresión de selección (más limpio) -->
<div th:object="${producto}">
    <p th:text="*{nombre}">Nombre</p>
    <p th:text="*{precio}">Precio</p>
    <p th:text="*{categoria}">Categoría</p>
</div>
```

### Expresiones de Mensaje `#{...}`

Para internacionalización (ver sección completa en el punto 9):

```html
<!-- messages.properties: saludo=Hola, {0}! -->
<p th:text="#{saludo(${nombre})}">Hola!</p>

<!-- messages.properties: app.titulo=Mi Aplicación -->
<title th:text="#{app.titulo}">Mi App</title>
```

### Expresiones de URL `@{...}`

```html
<!-- URL absoluta -->
<a th:href="@{/productos}">Ver todos los productos</a>

<!-- URL con parámetros de ruta (path variables) -->
<a th:href="@{/productos/{id}(id=${producto.id})}">Ver producto</a>

<!-- URL con parámetros de consulta (query params) -->
<a th:href="@{/buscar(q=${termino},pagina=1)}">Buscar</a>

<!-- Combinar path variable y query params -->
<a th:href="@{/productos/{id}/editar(id=${producto.id},origen='lista')}">
    Editar
</a>

<!-- URL de recursos estáticos -->
<link rel="stylesheet" th:href="@{/css/estilos.css}">
<img th:src="@{/images/logo.png}" alt="Logo">
<script th:src="@{/js/app.js}"></script>
```

### Operadores en Expresiones

```html
<!-- Operadores de comparación -->
<span th:if="${edad > 18}">Mayor de edad</span>
<span th:if="${edad &gt; 18}">Mayor de edad</span>  <!-- Equivalente en XML -->
<span th:if="${edad gt 18}">Mayor de edad</span>    <!-- Versión textual -->

<!-- Todos los operadores textuales -->
<!-- gt (>), lt (<), ge (>=), le (<=), eq (==), ne (!=) -->

<!-- Operadores lógicos -->
<span th:if="${activo and edad > 18}">Activo y mayor</span>
<span th:if="${!activo or edad < 18}">Inactivo o menor</span>
<span th:if="${not activo}">No activo</span>

<!-- Operador de concatenación de strings -->
<p th:text="${'Hola, ' + nombre + '!'}">Hola!</p>

<!-- Literales de string con | (más legible) -->
<p th:text="|Hola, ${nombre}!|">Hola!</p>

<!-- Operador condicional ternario -->
<span th:text="${activo ? 'Activo' : 'Inactivo'}">Estado</span>

<!-- No-Operation (_): No modifica el atributo -->
<span th:text="${nombre ?: _}">Sin nombre</span>
```

---

## 4. Atributos de Thymeleaf

### th:text y th:utext

```html
<!-- th:text: escapa el HTML (seguro, recomendado) -->
<p th:text="${descripcion}">Descripción</p>
<!-- Si descripcion = "<b>Hola</b>", muestra: &lt;b&gt;Hola&lt;/b&gt; -->

<!-- th:utext: NO escapa el HTML (usar con precaución, riesgo XSS) -->
<p th:utext="${descripcionHtml}">Descripción</p>
<!-- Si descripcionHtml = "<b>Hola</b>", muestra: Hola en negrita -->
```

> ⚠️ **Seguridad**: Usa `th:utext` **solo** cuando el contenido proviene de fuentes de confianza. Nunca con datos ingresados por usuarios, ya que puede provocar ataques XSS.

### th:attr y atributos individuales

```html
<!-- th:attr: establece cualquier atributo -->
<img th:attr="src=@{/img/${producto.imagen}},alt=${producto.nombre}">

<!-- Atributos individuales (más legible, recomendado) -->
<img th:src="@{/img/{img}(img=${producto.imagen})}" th:alt="${producto.nombre}">

<!-- th:attrappend / th:attrprepend: añade al valor existente -->
<div class="btn" th:attrappend="class=${' btn-' + tipo}">Botón</div>
<!-- Resultado: class="btn btn-primary" -->

<!-- th:classappend: añade clases CSS -->
<li class="nav-item" th:classappend="${activo ? ' active' : ''}">...</li>

<!-- th:styleappend: añade estilos inline -->
<div th:styleappend="${'color:' + color}">Texto</div>
```

### th:value, th:checked, th:selected, th:disabled

```html
<!-- th:value: valor de un input -->
<input type="text" th:value="${producto.nombre}" name="nombre">

<!-- th:checked: para checkboxes -->
<input type="checkbox" th:checked="${producto.disponible}" name="disponible">

<!-- th:selected: para opciones de select -->
<select name="categoria">
    <option value="electronics" th:selected="${categoria == 'electronics'}">
        Electrónica
    </option>
    <option value="clothing" th:selected="${categoria == 'clothing'}">
        Ropa
    </option>
</select>

<!-- th:disabled: deshabilita un elemento -->
<button th:disabled="${!puedeEditar}">Editar</button>
```

### th:href, th:src, th:action

```html
<!-- th:href: URL de un enlace -->
<a th:href="@{/productos/{id}(id=${producto.id})}">Ver</a>

<!-- th:src: fuente de imagen o script -->
<img th:src="@{/images/{img}(img=${producto.imagen})}" alt="Producto">
<script th:src="@{/js/app.js}"></script>

<!-- th:action: acción de un formulario -->
<form th:action="@{/productos/guardar}" method="post">
    ...
</form>
```

### th:replace, th:insert y th:include

```html
<!-- th:replace: reemplaza el elemento completo por el fragmento -->
<div th:replace="~{fragments/header :: header}"></div>

<!-- th:insert: inserta el fragmento dentro del elemento -->
<div th:insert="~{fragments/header :: header}"></div>

<!-- th:include: incluye solo el contenido del fragmento (deprecado) -->
<div th:include="~{fragments/header :: header}"></div>
```

### th:remove

```html
<!-- Elimina el elemento y su contenido -->
<p th:remove="all">Solo visible en prototipo</p>

<!-- Opciones de th:remove -->
<!-- all: elimina el elemento y su contenido -->
<!-- body: elimina solo el contenido (mantiene el tag) -->
<!-- tag: elimina el tag pero mantiene el contenido -->
<!-- all-but-first: elimina todos los hijos excepto el primero -->
<!-- none: no elimina nada (para deshabilitar condicionalmente) -->
```

### th:with

```html
<!-- Define variables locales dentro del elemento -->
<div th:with="precioConIva=${producto.precio * 1.21}">
    <p>Precio: <span th:text="${producto.precio}">0</span></p>
    <p>Con IVA: <span th:text="${precioConIva}">0</span></p>
</div>

<!-- Múltiples variables -->
<div th:with="nombre=${usuario.nombre}, rol=${usuario.rol}">
    <p th:text="|${nombre} (${rol})|">Usuario</p>
</div>
```

---

## 5. Iteraciones con th:each

### Iteración Básica

```java
// Controlador
@GetMapping("/productos")
public String listarProductos(Model model) {
    List<Producto> productos = productoService.findAll();
    model.addAttribute("productos", productos);
    return "productos/lista";
}
```

```html
<!-- templates/productos/lista.html -->
<table>
    <thead>
        <tr>
            <th>ID</th>
            <th>Nombre</th>
            <th>Precio</th>
        </tr>
    </thead>
    <tbody>
        <tr th:each="producto : ${productos}">
            <td th:text="${producto.id}">1</td>
            <td th:text="${producto.nombre}">Nombre</td>
            <td th:text="${producto.precio}">0.00</td>
        </tr>
    </tbody>
</table>
```

### Variable de Estado (iterStat)

```html
<!-- Usando el objeto de estado de iteración -->
<tr th:each="producto, iterStat : ${productos}"
    th:class="${iterStat.odd ? 'fila-impar' : 'fila-par'}">
    
    <td th:text="${iterStat.index}">0</td>       <!-- Índice (0-based) -->
    <td th:text="${iterStat.count}">1</td>       <!-- Contador (1-based) -->
    <td th:text="${iterStat.size}">10</td>       <!-- Total de elementos -->
    <td th:text="${iterStat.current}">elem</td>  <!-- Elemento actual -->
    <td th:text="${iterStat.even}">false</td>    <!-- ¿Es par? -->
    <td th:text="${iterStat.odd}">true</td>      <!-- ¿Es impar? -->
    <td th:text="${iterStat.first}">true</td>    <!-- ¿Es el primero? -->
    <td th:text="${iterStat.last}">false</td>    <!-- ¿Es el último? -->
    
    <td th:text="${producto.nombre}">Nombre</td>
</tr>
```

### Iteración sobre Mapas

```java
// Controlador
model.addAttribute("inventario", Map.of(
    "Laptop", 15,
    "Mouse", 50,
    "Teclado", 30
));
```

```html
<!-- Iterar sobre entradas de un mapa -->
<ul>
    <li th:each="entrada : ${inventario}">
        <span th:text="${entrada.key}">Producto</span>:
        <span th:text="${entrada.value}">0</span> unidades
    </li>
</ul>
```

### Iteración Anidada

```html
<!-- Categorías con sus productos -->
<div th:each="categoria : ${categorias}">
    <h2 th:text="${categoria.nombre}">Categoría</h2>
    <ul>
        <li th:each="producto : ${categoria.productos}"
            th:text="${producto.nombre}">Producto</li>
    </ul>
</div>
```

### Formateo en Iteración

```html
<!-- Formatear números y fechas dentro de each -->
<tr th:each="venta : ${ventas}">
    <td th:text="${#temporals.format(venta.fecha, 'dd/MM/yyyy')}">Fecha</td>
    <td th:text="${#numbers.formatDecimal(venta.total, 1, 2)}">Total</td>
    <td th:text="${#strings.abbreviate(venta.descripcion, 50)}">Desc.</td>
</tr>
```

---

## 6. Condicionales

### th:if y th:unless

```html
<!-- th:if: muestra si la condición es verdadera -->
<div th:if="${usuario != null}">
    <p th:text="|Bienvenido, ${usuario.nombre}!|">Bienvenido</p>
</div>

<!-- th:unless: muestra si la condición es FALSA (opuesto a th:if) -->
<div th:unless="${usuario != null}">
    <p>Por favor, inicia sesión</p>
</div>

<!-- Con listas vacías -->
<p th:if="${#lists.isEmpty(productos)}">No hay productos disponibles</p>
<table th:unless="${#lists.isEmpty(productos)}">
    <!-- ... -->
</table>
```

### th:switch y th:case

```html
<!-- Switch-case -->
<div th:switch="${usuario.rol}">
    <p th:case="'ADMIN'">Eres administrador</p>
    <p th:case="'EDITOR'">Eres editor</p>
    <p th:case="'USER'">Eres usuario estándar</p>
    <p th:case="*">Rol desconocido</p>  <!-- Caso por defecto -->
</div>
```

### Valores Falsy en Thymeleaf

Las siguientes condiciones se evalúan como **false**:
- `null`
- `false`
- `0` (cero numérico)
- Cadena vacía `""`
- Cadenas `"false"`, `"off"`, `"no"`

```html
<!-- Todos estos son false -->
<div th:if="${nulo}">No se muestra</div>
<div th:if="${falso}">No se muestra</div>
<div th:if="${cero}">No se muestra</div>
<div th:if="${cadenaVacia}">No se muestra</div>
```

### Condicionales en Línea

```html
<!-- Condición ternaria inline -->
<span th:text="${stock > 0 ? 'En stock' : 'Agotado'}">Estado</span>

<!-- Clase condicional -->
<span th:class="${activo ? 'badge-success' : 'badge-danger'}"
      th:text="${activo ? 'Activo' : 'Inactivo'}">
    Estado
</span>

<!-- th:classappend para no perder clases existentes -->
<li class="nav-item" th:classappend="${paginaActual == 'inicio' ? ' active' : ''}">
    <a th:href="@{/}" class="nav-link">Inicio</a>
</li>
```

---

## 7. Formularios y Validación

### Formulario Básico con th:object y th:field

```java
// Modelo
public class ProductoForm {
    @NotBlank(message = "El nombre es obligatorio")
    private String nombre;

    @NotNull(message = "El precio es obligatorio")
    @DecimalMin(value = "0.01", message = "El precio debe ser mayor que 0")
    private BigDecimal precio;

    @NotBlank(message = "La categoría es obligatoria")
    private String categoria;

    private boolean disponible;

    // Getters y Setters
}
```

```java
// Controlador
@Controller
@RequestMapping("/productos")
public class ProductoController {

    @GetMapping("/nuevo")
    public String mostrarFormulario(Model model) {
        model.addAttribute("productoForm", new ProductoForm());
        model.addAttribute("categorias", List.of("Electrónica", "Ropa", "Hogar"));
        return "productos/formulario";
    }

    @PostMapping("/guardar")
    public String guardarProducto(@Valid @ModelAttribute ProductoForm productoForm,
                                   BindingResult result,
                                   Model model) {
        if (result.hasErrors()) {
            model.addAttribute("categorias", List.of("Electrónica", "Ropa", "Hogar"));
            return "productos/formulario";
        }
        productoService.guardar(productoForm);
        return "redirect:/productos";
    }
}
```

```html
<!-- templates/productos/formulario.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
    <form th:action="@{/productos/guardar}" th:object="${productoForm}" method="post">

        <!-- Campo de texto -->
        <div>
            <label for="nombre">Nombre:</label>
            <input type="text" id="nombre" th:field="*{nombre}"
                   th:class="${#fields.hasErrors('nombre')} ? 'input-error' : ''">
            <!-- Mensajes de error -->
            <span th:if="${#fields.hasErrors('nombre')}"
                  th:errors="*{nombre}" class="error">Error</span>
        </div>

        <!-- Campo numérico -->
        <div>
            <label for="precio">Precio:</label>
            <input type="number" id="precio" th:field="*{precio}" step="0.01">
            <span th:if="${#fields.hasErrors('precio')}"
                  th:errors="*{precio}" class="error">Error</span>
        </div>

        <!-- Select -->
        <div>
            <label for="categoria">Categoría:</label>
            <select id="categoria" th:field="*{categoria}">
                <option value="">-- Seleccionar --</option>
                <option th:each="cat : ${categorias}"
                        th:value="${cat}"
                        th:text="${cat}">Categoría</option>
            </select>
            <span th:if="${#fields.hasErrors('categoria')}"
                  th:errors="*{categoria}" class="error">Error</span>
        </div>

        <!-- Checkbox -->
        <div>
            <input type="checkbox" id="disponible" th:field="*{disponible}">
            <label for="disponible">Disponible</label>
        </div>

        <!-- Token CSRF (generado automáticamente por Spring Security) -->
        <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}">

        <button type="submit">Guardar</button>
        <a th:href="@{/productos}">Cancelar</a>
    </form>
</body>
</html>
```

### th:field en Detalle

`th:field` genera automáticamente los atributos `id`, `name` y `value`:

```html
<!-- th:field="*{nombre}" genera: -->
<input type="text" id="nombre" name="nombre" value="[valor actual]">
```

### Formulario de Edición

```java
@GetMapping("/editar/{id}")
public String mostrarFormularioEdicion(@PathVariable Long id, Model model) {
    Producto producto = productoService.findById(id);
    model.addAttribute("productoForm", producto);
    model.addAttribute("categorias", List.of("Electrónica", "Ropa", "Hogar"));
    return "productos/formulario";
}
```

```html
<!-- El mismo formulario funciona para crear y editar -->
<!-- Añadimos un campo oculto para el ID en edición -->
<form th:action="@{/productos/guardar}" th:object="${productoForm}" method="post">
    <input type="hidden" th:field="*{id}">
    <!-- resto del formulario... -->
</form>
```

### Radio Buttons y Checkboxes Múltiples

```java
public class UsuarioForm {
    private String genero;           // Radio button
    private List<String> intereses;  // Checkboxes múltiples
}
```

```html
<!-- Radio buttons -->
<div>
    <label>
        <input type="radio" th:field="*{genero}" value="M"> Masculino
    </label>
    <label>
        <input type="radio" th:field="*{genero}" value="F"> Femenino
    </label>
    <label>
        <input type="radio" th:field="*{genero}" value="O"> Otro
    </label>
</div>

<!-- Checkboxes múltiples -->
<div>
    <label>
        <input type="checkbox" th:field="*{intereses}" value="deportes"> Deportes
    </label>
    <label>
        <input type="checkbox" th:field="*{intereses}" value="musica"> Música
    </label>
    <label>
        <input type="checkbox" th:field="*{intereses}" value="tecnologia"> Tecnología
    </label>
</div>
```

### Mostrar Errores Globales

```html
<!-- Errores globales del formulario (no asociados a un campo) -->
<div th:if="${#fields.hasGlobalErrors()}">
    <ul>
        <li th:each="error : ${#fields.globalErrors()}" th:text="${error}">Error</li>
    </ul>
</div>

<!-- Todos los errores -->
<div th:if="${#fields.hasAnyErrors()}">
    <ul>
        <li th:each="error : ${#fields.allErrors()}" th:text="${error}">Error</li>
    </ul>
</div>
```

### Mensajes Flash (RedirectAttributes)

```java
// Controlador
@PostMapping("/guardar")
public String guardar(@Valid ProductoForm form, BindingResult result,
                       RedirectAttributes redirectAttrs) {
    if (result.hasErrors()) return "productos/formulario";

    productoService.guardar(form);
    redirectAttrs.addFlashAttribute("mensajeExito", "Producto guardado correctamente");
    return "redirect:/productos";
}
```

```html
<!-- Mostrar mensaje flash -->
<div th:if="${mensajeExito}" class="alert alert-success">
    <p th:text="${mensajeExito}">Éxito</p>
</div>
<div th:if="${mensajeError}" class="alert alert-danger">
    <p th:text="${mensajeError}">Error</p>
</div>
```

---

## 8. Fragmentos y Layouts

### Definir Fragmentos

```html
<!-- templates/fragments/comunes.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">

<!-- Fragmento de cabecera -->
<header th:fragment="header">
    <nav>
        <a th:href="@{/}">Inicio</a>
        <a th:href="@{/productos}">Productos</a>
        <a th:href="@{/contacto}">Contacto</a>
    </nav>
</header>

<!-- Fragmento de pie de página -->
<footer th:fragment="footer">
    <p>&copy; 2024 Mi Empresa. Todos los derechos reservados.</p>
</footer>

<!-- Fragmento con parámetros -->
<div th:fragment="alerta(tipo, mensaje)">
    <div th:class="|alert alert-${tipo}|">
        <p th:text="${mensaje}">Mensaje</p>
    </div>
</div>

</html>
```

### Usar Fragmentos

```html
<!-- templates/index.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Inicio</title>
</head>
<body>
    <!-- th:replace: reemplaza el elemento por el fragmento -->
    <div th:replace="~{fragments/comunes :: header}"></div>

    <main>
        <h1>Bienvenido</h1>

        <!-- Fragmento con parámetros -->
        <div th:replace="~{fragments/comunes :: alerta('success', 'Operación exitosa')}"></div>
    </main>

    <!-- th:insert: inserta el fragmento dentro del elemento -->
    <footer th:insert="~{fragments/comunes :: footer}"></footer>
</body>
</html>
```

### Layout con Decoración (Thymeleaf Layout Dialect)

Para un sistema de layout más potente, usa **Thymeleaf Layout Dialect**:

```xml
<!-- Dependencia adicional -->
<dependency>
    <groupId>nz.net.ultraq.thymeleaf</groupId>
    <artifactId>thymeleaf-layout-dialect</artifactId>
</dependency>
```

```html
<!-- templates/layouts/base.html - Plantilla base -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">
<head>
    <meta charset="UTF-8">
    <title layout:title-pattern="$CONTENT_TITLE - $LAYOUT_TITLE">Mi App</title>
    <link rel="stylesheet" th:href="@{/css/bootstrap.min.css}">
    <link rel="stylesheet" th:href="@{/css/estilos.css}">
    <!-- Sección para CSS adicional de páginas hijas -->
    <th:block layout:fragment="css-adicional"></th:block>
</head>
<body>
    <!-- Cabecera fija -->
    <header th:insert="~{fragments/comunes :: header}"></header>

    <!-- Contenido principal: las páginas hijas reemplazarán esto -->
    <main class="container mt-4">
        <th:block layout:fragment="contenido">
            <!-- Contenido por defecto -->
        </th:block>
    </main>

    <!-- Pie de página fijo -->
    <footer th:insert="~{fragments/comunes :: footer}"></footer>

    <!-- Scripts base -->
    <script th:src="@{/js/bootstrap.bundle.min.js}"></script>
    <!-- Sección para scripts adicionales de páginas hijas -->
    <th:block layout:fragment="scripts-adicionales"></th:block>
</body>
</html>
```

```html
<!-- templates/productos/lista.html - Página hija -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layouts/base}">
<head>
    <title>Lista de Productos</title>
    <!-- CSS adicional solo para esta página -->
    <th:block layout:fragment="css-adicional">
        <link rel="stylesheet" th:href="@{/css/productos.css}">
    </th:block>
</head>
<body>
    <!-- Este fragmento reemplaza layout:fragment="contenido" del base -->
    <th:block layout:fragment="contenido">
        <h1>Productos</h1>
        <table>
            <!-- ... -->
        </table>
    </th:block>

    <!-- Scripts adicionales solo para esta página -->
    <th:block layout:fragment="scripts-adicionales">
        <script th:src="@{/js/productos.js}"></script>
    </th:block>
</body>
</html>
```

### Fragmentos Inline (Sin Archivo Separado)

```html
<!-- En la misma página, definir y usar fragmentos -->
<div>
    <!-- Definir un fragmento dentro de la misma plantilla -->
    <div th:fragment="tarjeta-producto(producto)">
        <div class="card">
            <h3 th:text="${producto.nombre}">Nombre</h3>
            <p th:text="${producto.precio}">Precio</p>
        </div>
    </div>

    <!-- Usar el fragmento en otra parte -->
    <div th:each="p : ${productos}">
        <div th:replace="~{this :: tarjeta-producto(${p})}"></div>
    </div>
</div>
```

---

## 9. Internacionalización (i18n)

### Configuración

```java
// MessageSourceConfig.java
@Configuration
public class MessageSourceConfig {

    @Bean
    public MessageSource messageSource() {
        ReloadableResourceBundleMessageSource messageSource =
            new ReloadableResourceBundleMessageSource();
        messageSource.setBasename("classpath:i18n/messages");
        messageSource.setDefaultEncoding("UTF-8");
        return messageSource;
    }

    @Bean
    public LocaleResolver localeResolver() {
        SessionLocaleResolver localeResolver = new SessionLocaleResolver();
        localeResolver.setDefaultLocale(new Locale("es"));
        return localeResolver;
    }

    @Bean
    public LocaleChangeInterceptor localeChangeInterceptor() {
        LocaleChangeInterceptor interceptor = new LocaleChangeInterceptor();
        interceptor.setParamName("lang");  // ?lang=es, ?lang=en
        return interceptor;
    }
}

// WebConfig.java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Autowired
    private LocaleChangeInterceptor localeChangeInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(localeChangeInterceptor);
    }
}
```

### Archivos de Mensajes

```properties
# src/main/resources/i18n/messages.properties (por defecto / inglés)
app.titulo=My Application
nav.inicio=Home
nav.productos=Products
btn.guardar=Save
btn.cancelar=Cancel
msg.exito=Operation successful
msg.error=An error occurred
producto.nombre=Name
producto.precio=Price
producto.sinStock=Out of stock
# Con parámetros:
bienvenida=Welcome, {0}!
productos.total=Total products: {0}
```

```properties
# src/main/resources/i18n/messages_es.properties (Español)
app.titulo=Mi Aplicación
nav.inicio=Inicio
nav.productos=Productos
btn.guardar=Guardar
btn.cancelar=Cancelar
msg.exito=Operación exitosa
msg.error=Se ha producido un error
producto.nombre=Nombre
producto.precio=Precio
producto.sinStock=Sin stock
# Con parámetros:
bienvenida=¡Bienvenido, {0}!
productos.total=Total de productos: {0}
```

```properties
# src/main/resources/i18n/messages_en.properties (Inglés explícito)
# (mismos valores que messages.properties)
```

### Usar Mensajes en Plantillas

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title th:text="#{app.titulo}">Mi App</title>
</head>
<body>
    <nav>
        <a th:href="@{/}" th:text="#{nav.inicio}">Inicio</a>
        <a th:href="@{/productos}" th:text="#{nav.productos}">Productos</a>
    </nav>

    <!-- Mensaje con parámetros -->
    <h1 th:text="#{bienvenida(${usuario.nombre})}">Bienvenido</h1>
    <p th:text="#{productos.total(${#lists.size(productos)})}">Total: 0</p>

    <!-- Cambio de idioma -->
    <div>
        <a th:href="@{/(lang=es)}">Español</a> |
        <a th:href="@{/(lang=en)}">English</a>
    </div>

    <!-- Combinar con condicionales -->
    <span th:text="${producto.stock > 0 ? #{producto.enStock} : #{producto.sinStock}}">
        Estado
    </span>
</body>
</html>
```

---

## 10. Integración con Spring Security

### Configuración

```xml
<!-- Dependencia -->
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity6</artifactId>
</dependency>
```

```html
<!-- Añadir el namespace en la plantilla -->
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
```

### Mostrar Información del Usuario Autenticado

```html
<!-- Nombre del usuario -->
<span sec:authentication="name">Usuario</span>
<span sec:authentication="principal.username">Usuario</span>

<!-- Email u otros atributos del principal (si tu UserDetails los tiene) -->
<span sec:authentication="principal.email">email@ejemplo.com</span>

<!-- Roles del usuario -->
<span sec:authentication="principal.authorities">Roles</span>
```

### Control de Visibilidad por Rol

```html
<!-- Mostrar solo si está autenticado -->
<div sec:authorize="isAuthenticated()">
    <p>Contenido solo para usuarios autenticados</p>
</div>

<!-- Mostrar solo si NO está autenticado -->
<div sec:authorize="isAnonymous()">
    <a th:href="@{/login}">Iniciar Sesión</a>
    <a th:href="@{/registro}">Registrarse</a>
</div>

<!-- Mostrar solo para un rol específico -->
<div sec:authorize="hasRole('ADMIN')">
    <a th:href="@{/admin}">Panel de Administración</a>
</div>

<!-- Múltiples roles -->
<div sec:authorize="hasAnyRole('ADMIN', 'EDITOR')">
    <a th:href="@{/productos/nuevo}">Nuevo Producto</a>
</div>

<!-- Con autoridad completa -->
<div sec:authorize="hasAuthority('ROLE_ADMIN')">
    <p>Solo para admins</p>
</div>

<!-- Combinaciones complejas -->
<div sec:authorize="isAuthenticated() and hasRole('USER')">
    <p>Usuario autenticado con rol USER</p>
</div>
```

### Formulario de Login

```html
<!-- templates/login.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
    <form th:action="@{/login}" method="post">
        <div>
            <label for="username">Usuario:</label>
            <input type="text" id="username" name="username" autofocus>
        </div>
        <div>
            <label for="password">Contraseña:</label>
            <input type="password" id="password" name="password">
        </div>

        <!-- Mensaje de error de login -->
        <div th:if="${param.error}" class="alert alert-danger">
            Usuario o contraseña incorrectos.
        </div>

        <!-- Mensaje de cierre de sesión -->
        <div th:if="${param.logout}" class="alert alert-success">
            Sesión cerrada correctamente.
        </div>

        <!-- CSRF token -->
        <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}">

        <button type="submit">Entrar</button>
    </form>
</body>
</html>
```

### Formulario de Logout

```html
<!-- Botón de cerrar sesión (debe ser un formulario POST) -->
<form th:action="@{/logout}" method="post" style="display:inline">
    <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}">
    <button type="submit">Cerrar Sesión</button>
</form>
```

---

## 11. JavaScript y CSS con Thymeleaf

### Pasar Variables a JavaScript

```html
<!-- Método 1: Inline con th:inline="javascript" -->
<script th:inline="javascript">
    // [[${variable}]] es la sintaxis inline para JavaScript
    var nombreUsuario = [[${usuario.nombre}]];
    var productoId = [[${producto.id}]];
    var precios = [[${precios}]];  // Arrays y objetos se serializan automáticamente como JSON

    // Condición inline
    var isAdmin = [[${#authorization.expression('hasRole(''ADMIN'')')}]];

    // Mensaje i18n inline
    var msgExito = [[#{msg.exito}]];
</script>
```

```html
<!-- Método 2: Atributos data- (recomendado para datos simples) -->
<div id="app"
     th:data-usuario-id="${usuario.id}"
     th:data-usuario-nombre="${usuario.nombre}"
     th:data-api-url="@{/api}">
</div>

<script>
    const app = document.getElementById('app');
    const usuarioId = app.dataset.usuarioId;
    const apiUrl = app.dataset.apiUrl;
</script>
```

### CSS Dinámico con Thymeleaf

```html
<!-- Archivo CSS como plantilla Thymeleaf -->
<!-- templates/estilos-dinamicos.css -->
```

```css
/* Con th:inline="css" */
```

```html
<style th:inline="css">
    .mi-color {
        color: /*[[${colorPrincipal}]]*/ #333;
        background: /*[[${colorFondo}]]*/ #fff;
    }
</style>
```

### Condicionales CSS

```html
<!-- Cambio dinámico de clases según estado -->
<tr th:each="producto : ${productos}"
    th:classappend="${producto.stock == 0 ? ' sin-stock' : ''}">
    <td th:text="${producto.nombre}">Nombre</td>
    <td th:text="${producto.stock}">Stock</td>
</tr>

<!-- Usando th:class para reemplazar todo el atributo class -->
<button th:class="${activo ? 'btn btn-primary' : 'btn btn-secondary'}">
    Acción
</button>
```

---

## 12. Utilidades y Objetos de Contexto

Thymeleaf provee objetos de utilidad accesibles con `#`:

### #strings - Manipulación de Cadenas

```html
<!-- Verificaciones -->
<span th:if="${#strings.isEmpty(texto)}">Vacío</span>
<span th:if="${#strings.contains(texto, 'buscar')}">Contiene</span>
<span th:if="${#strings.startsWith(texto, 'pre')}">Empieza con</span>
<span th:if="${#strings.endsWith(texto, 'suf')}">Termina con</span>

<!-- Transformaciones -->
<p th:text="${#strings.toUpperCase(nombre)}">NOMBRE</p>
<p th:text="${#strings.toLowerCase(nombre)}">nombre</p>
<p th:text="${#strings.trim(texto)}">Texto sin espacios</p>
<p th:text="${#strings.abbreviate(descripcion, 100)}">Descripción corta...</p>
<p th:text="${#strings.replace(texto, 'viejo', 'nuevo')}">Texto</p>
<p th:text="${#strings.concat(nombre, ' ', apellido)}">Nombre Apellido</p>

<!-- Subcadenas y búsqueda -->
<p th:text="${#strings.substring(texto, 0, 10)}">Primeros 10 chars</p>
<p th:text="${#strings.indexOf(texto, 'buscar')}">Posición</p>
<p th:text="${#strings.length(texto)}">Longitud</p>
```

### #numbers - Formateo de Números

```html
<!-- Formateo decimal -->
<span th:text="${#numbers.formatDecimal(precio, 1, 2)}">1,234.56</span>
<!-- Parámetros: (valor, minDigitosEnteros, maxDecimales) -->

<!-- Formateo con separadores personalizados -->
<span th:text="${#numbers.formatDecimal(precio, 1, 'COMMA', 2, 'POINT')}">
    1,234.56
</span>

<!-- Formateo de moneda -->
<span th:text="${#numbers.formatCurrency(precio)}">€1.234,56</span>

<!-- Formateo de porcentaje -->
<span th:text="${#numbers.formatPercent(ratio, 1, 2)}">12.34%</span>

<!-- Secuencias numéricas -->
<span th:each="i : ${#numbers.sequence(1, 10)}" th:text="${i}">1</span>
<span th:each="i : ${#numbers.sequence(0, 100, 10)}" th:text="${i}">0</span>
```

### #dates y #temporals - Fechas

```html
<!-- Con java.util.Date (#dates) -->
<span th:text="${#dates.format(fecha, 'dd/MM/yyyy')}">01/01/2024</span>
<span th:text="${#dates.format(fecha, 'dd/MM/yyyy HH:mm')}">01/01/2024 12:00</span>
<span th:text="${#dates.year(fecha)}">2024</span>
<span th:text="${#dates.month(fecha)}">1</span>
<span th:text="${#dates.day(fecha)}">1</span>

<!-- Con java.time.* (#temporals) - Recomendado con Java 8+ -->
<span th:text="${#temporals.format(fechaLocal, 'dd/MM/yyyy')}">Fecha</span>
<span th:text="${#temporals.format(fechaHoraLocal, 'dd/MM/yyyy HH:mm:ss')}">Fecha Hora</span>
<span th:text="${#temporals.formatISO(fechaLocal)}">2024-01-01</span>
<span th:text="${#temporals.year(fechaLocal)}">2024</span>
<span th:text="${#temporals.dayOfWeekName(fechaLocal)}">Lunes</span>
```

### #lists y #sets - Colecciones

```html
<!-- Tamaño -->
<span th:text="${#lists.size(lista)}">0</span>

<!-- Verificaciones -->
<span th:if="${#lists.isEmpty(lista)}">Sin elementos</span>
<span th:if="${#lists.contains(lista, elemento)}">Contiene</span>
<span th:if="${#lists.containsAll(lista, otraLista)}">Contiene todos</span>

<!-- Operaciones -->
<span th:text="${#lists.sort(lista)}">Lista ordenada</span>
```

### #maps - Mapas

```html
<!-- Tamaño -->
<span th:text="${#maps.size(mapa)}">0</span>

<!-- Verificaciones -->
<span th:if="${#maps.isEmpty(mapa)}">Vacío</span>
<span th:if="${#maps.containsKey(mapa, 'clave')}">Tiene la clave</span>
<span th:if="${#maps.containsValue(mapa, 'valor')}">Tiene el valor</span>
```

### #objects y #bools

```html
<!-- #objects -->
<span th:text="${#objects.nullSafe(objeto, 'Por defecto')}">Por defecto</span>

<!-- #bools -->
<span th:if="${#bools.isTrue(valor)}">Verdadero</span>
<span th:if="${#bools.isFalse(valor)}">Falso</span>
```

### Variables de Contexto de Petición

```html
<!-- Variables de contexto HTTP disponibles en plantillas -->

<!-- Parámetros de la petición -->
<span th:text="${param.q}">Búsqueda</span>
<span th:each="p : ${param.tags}" th:text="${p}">Tag</span>

<!-- Atributos de la sesión -->
<span th:text="${session.usuario}">Usuario</span>
<span th:if="${session.carrito != null}">Carrito activo</span>

<!-- Atributos de la aplicación (ServletContext) -->
<span th:text="${application.version}">1.0</span>

<!-- Información de la petición -->
<span th:text="${#request.contextPath}">Ruta del contexto</span>
<span th:text="${#request.requestURL}">URL completa</span>
<span th:text="${#request.method}">GET/POST</span>

<!-- Información de la sesión HTTP -->
<span th:text="${#session.id}">ID de sesión</span>

<!-- Locale actual -->
<span th:text="${#locale}">es_ES</span>
```

---

## 13. Thymeleaf Avanzado

### Expresiones Inline

Para mostrar valores directamente en el texto del cuerpo HTML sin `th:text`:

```html
<!-- Activar modo inline en un bloque -->
<p th:inline="text">
    Hola, [[${nombre}]]! Tienes [[${mensajes}]] mensajes nuevos.
</p>

<!-- Escapar para que NO sea procesado como expresión Thymeleaf -->
<p th:inline="text">
    La sintaxis [[${...}]] es de Thymeleaf (esto NO será procesado: [(\${nombre})])
</p>

<!-- Inline en JavaScript (ya visto) -->
<script th:inline="javascript">
    const datos = [[${objeto}]];
</script>
```

### Procesamiento de Plantillas por Programa

```java
// Procesar plantilla desde código Java (sin HTTP)
@Service
public class EmailService {

    @Autowired
    private TemplateEngine templateEngine;

    public String generarHtmlEmail(String nombre, String enlace) {
        Context context = new Context();
        context.setVariable("nombre", nombre);
        context.setVariable("enlace", enlace);
        return templateEngine.process("emails/bienvenida", context);
    }

    public void enviarEmailBienvenida(String email, String nombre) {
        String html = generarHtmlEmail(nombre, "https://miapp.com/activar/token123");
        // Enviar con JavaMailSender...
    }
}
```

```html
<!-- templates/emails/bienvenida.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
    <h1 th:text="|Hola, ${nombre}!|">Hola!</h1>
    <p>Gracias por registrarte.</p>
    <a th:href="${enlace}">Activar cuenta</a>
</body>
</html>
```

### Dialectos Personalizados

Puedes crear tus propios atributos `th:*`:

```java
// CustomDialect.java
@Component
public class CustomDialect extends AbstractProcessorDialect {

    public CustomDialect() {
        super("Custom Dialect", "custom", 1000);
    }

    @Override
    public Set<IProcessor> getProcessors(String dialectPrefix) {
        Set<IProcessor> processors = new HashSet<>();
        processors.add(new CustomAttributeTagProcessor(dialectPrefix));
        return processors;
    }
}
```

### Caché de Plantillas

```java
// Configuración avanzada de Thymeleaf
@Configuration
public class ThymeleafConfig {

    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
        SpringTemplateEngine engine = new SpringTemplateEngine();
        engine.setTemplateResolver(templateResolver);
        engine.setEnableSpringELCompiler(true);  // Mejora el rendimiento
        return engine;
    }

    @Bean
    public ClassLoaderTemplateResolver templateResolver() {
        ClassLoaderTemplateResolver resolver = new ClassLoaderTemplateResolver();
        resolver.setPrefix("templates/");
        resolver.setSuffix(".html");
        resolver.setTemplateMode(TemplateMode.HTML);
        resolver.setCharacterEncoding("UTF-8");
        resolver.setCacheable(true);          // Activar caché
        resolver.setCacheTTLMs(3600000L);     // 1 hora de caché
        return resolver;
    }
}
```

### Preprocesamiento con `__${...}__`

El preprocesamiento permite evaluar expresiones antes que el motor principal las procese:

```html
<!-- __${...}__ se evalúa primero -->
<!-- Si campo = 'nombre', genera: th:text="${persona.nombre}" -->
<span th:text="${persona.__${campo}__}">Valor dinámico</span>

<!-- Uso en fragmentos parametrizados -->
<div th:fragment="campo-entidad(entidad, propiedad)">
    <span th:text="${__${entidad}__.__${propiedad}__}">Valor</span>
</div>
```

### Manejo de Errores en Plantillas

```html
<!-- Expresión con manejo de null -->
<span th:text="${usuario != null ? usuario.nombre : 'Invitado'}">Invitado</span>

<!-- Operador Elvis ?: -->
<span th:text="${usuario?.nombre ?: 'Invitado'}">Invitado</span>

<!-- null-safe navigation ?. -->
<span th:text="${usuario?.direccion?.ciudad ?: 'Sin ciudad'}">Ciudad</span>
```

---

## 14. Proyecto Completo: Gestor de Tareas

### Descripción

Aplicación web completa con Spring Boot + Thymeleaf + Bootstrap para gestionar tareas personales con:

- CRUD de tareas
- Filtrado por estado y prioridad
- Autenticación básica
- Mensajes flash
- Paginación
- Validación de formularios

### Estructura del Proyecto

```
gestor-tareas/
├── pom.xml
└── src/main/
    ├── java/com/ejemplo/tareas/
    │   ├── GestorTareasApplication.java
    │   ├── config/
    │   │   └── SecurityConfig.java
    │   ├── controller/
    │   │   ├── HomeController.java
    │   │   └── TareaController.java
    │   ├── model/
    │   │   ├── Tarea.java
    │   │   ├── Prioridad.java
    │   │   └── Estado.java
    │   ├── repository/
    │   │   └── TareaRepository.java
    │   └── service/
    │       └── TareaService.java
    └── resources/
        ├── templates/
        │   ├── layouts/base.html
        │   ├── fragments/navbar.html
        │   ├── index.html
        │   ├── login.html
        │   └── tareas/
        │       ├── lista.html
        │       └── formulario.html
        ├── static/css/estilos.css
        └── application.properties
```

### Entidades y Enumeraciones

```java
// Prioridad.java
public enum Prioridad {
    BAJA, MEDIA, ALTA, CRITICA;

    public String getClaseBadge() {
        return switch (this) {
            case BAJA -> "bg-secondary";
            case MEDIA -> "bg-info";
            case ALTA -> "bg-warning";
            case CRITICA -> "bg-danger";
        };
    }
}

// Estado.java
public enum Estado {
    PENDIENTE, EN_PROGRESO, COMPLETADA, CANCELADA;

    public String getClaseBadge() {
        return switch (this) {
            case PENDIENTE -> "bg-secondary";
            case EN_PROGRESO -> "bg-primary";
            case COMPLETADA -> "bg-success";
            case CANCELADA -> "bg-dark";
        };
    }
}

// Tarea.java
@Entity
@Table(name = "tareas")
public class Tarea {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotBlank(message = "El título es obligatorio")
    @Size(min = 3, max = 100, message = "El título debe tener entre 3 y 100 caracteres")
    private String titulo;

    @Size(max = 500, message = "La descripción no puede superar 500 caracteres")
    private String descripcion;

    @NotNull(message = "La prioridad es obligatoria")
    @Enumerated(EnumType.STRING)
    private Prioridad prioridad;

    @Enumerated(EnumType.STRING)
    private Estado estado = Estado.PENDIENTE;

    private LocalDate fechaVencimiento;

    @CreatedDate
    private LocalDateTime fechaCreacion;

    // Getters y Setters...
}
```

### Repositorio y Servicio

```java
// TareaRepository.java
@Repository
public interface TareaRepository extends JpaRepository<Tarea, Long> {
    List<Tarea> findByEstado(Estado estado);
    List<Tarea> findByPrioridad(Prioridad prioridad);
    Page<Tarea> findAll(Pageable pageable);
    List<Tarea> findByTituloContainingIgnoreCase(String titulo);
}

// TareaService.java
@Service
@Transactional
public class TareaService {

    @Autowired
    private TareaRepository tareaRepository;

    public Page<Tarea> listar(int pagina, int tamaño) {
        return tareaRepository.findAll(
            PageRequest.of(pagina, tamaño, Sort.by("fechaCreacion").descending())
        );
    }

    public Tarea guardar(Tarea tarea) {
        if (tarea.getId() == null) {
            tarea.setFechaCreacion(LocalDateTime.now());
        }
        return tareaRepository.save(tarea);
    }

    public Tarea buscarPorId(Long id) {
        return tareaRepository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("Tarea no encontrada: " + id));
    }

    public void eliminar(Long id) {
        tareaRepository.deleteById(id);
    }

    public Tarea cambiarEstado(Long id, Estado nuevoEstado) {
        Tarea tarea = buscarPorId(id);
        tarea.setEstado(nuevoEstado);
        return tareaRepository.save(tarea);
    }
}
```

### Controlador Principal

```java
// TareaController.java
@Controller
@RequestMapping("/tareas")
public class TareaController {

    @Autowired
    private TareaService tareaService;

    @GetMapping
    public String lista(Model model,
                        @RequestParam(defaultValue = "0") int pagina,
                        @RequestParam(defaultValue = "10") int tamanio) {
        Page<Tarea> tareas = tareaService.listar(pagina, tamanio);
        model.addAttribute("tareas", tareas);
        model.addAttribute("paginaActual", pagina);
        model.addAttribute("totalPaginas", tareas.getTotalPages());
        model.addAttribute("estados", Estado.values());
        model.addAttribute("prioridades", Prioridad.values());
        return "tareas/lista";
    }

    @GetMapping("/nueva")
    public String nuevaForm(Model model) {
        model.addAttribute("tarea", new Tarea());
        model.addAttribute("prioridades", Prioridad.values());
        return "tareas/formulario";
    }

    @PostMapping("/guardar")
    public String guardar(@Valid @ModelAttribute Tarea tarea,
                           BindingResult result,
                           Model model,
                           RedirectAttributes flash) {
        if (result.hasErrors()) {
            model.addAttribute("prioridades", Prioridad.values());
            return "tareas/formulario";
        }
        tareaService.guardar(tarea);
        flash.addFlashAttribute("exito", "Tarea guardada correctamente");
        return "redirect:/tareas";
    }

    @GetMapping("/editar/{id}")
    public String editarForm(@PathVariable Long id, Model model) {
        model.addAttribute("tarea", tareaService.buscarPorId(id));
        model.addAttribute("prioridades", Prioridad.values());
        return "tareas/formulario";
    }

    @PostMapping("/eliminar/{id}")
    public String eliminar(@PathVariable Long id, RedirectAttributes flash) {
        tareaService.eliminar(id);
        flash.addFlashAttribute("exito", "Tarea eliminada correctamente");
        return "redirect:/tareas";
    }

    @PostMapping("/estado/{id}")
    public String cambiarEstado(@PathVariable Long id,
                                 @RequestParam Estado estado,
                                 RedirectAttributes flash) {
        tareaService.cambiarEstado(id, estado);
        flash.addFlashAttribute("exito", "Estado actualizado");
        return "redirect:/tareas";
    }
}
```

### Plantilla Base (Layout)

```html
<!-- templates/layouts/base.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title layout:title-pattern="$CONTENT_TITLE - Gestor de Tareas">Gestor de Tareas</title>
    <link rel="stylesheet"
          th:href="@{/webjars/bootstrap/5.3.0/css/bootstrap.min.css}">
    <link rel="stylesheet" th:href="@{/css/estilos.css}">
    <th:block layout:fragment="css"></th:block>
</head>
<body>
    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <div class="container">
            <a class="navbar-brand" th:href="@{/}">
                🗂️ Gestor de Tareas
            </a>
            <div class="navbar-nav ms-auto">
                <a class="nav-link" th:href="@{/tareas}">Mis Tareas</a>
                <div sec:authorize="isAuthenticated()">
                    <span class="navbar-text me-3">
                        Hola, <strong sec:authentication="name">Usuario</strong>
                    </span>
                    <form th:action="@{/logout}" method="post" class="d-inline">
                        <input type="hidden"
                               th:name="${_csrf.parameterName}"
                               th:value="${_csrf.token}">
                        <button type="submit" class="btn btn-outline-light btn-sm">
                            Salir
                        </button>
                    </form>
                </div>
                <div sec:authorize="isAnonymous()">
                    <a class="nav-link" th:href="@{/login}">Iniciar Sesión</a>
                </div>
            </div>
        </div>
    </nav>

    <!-- Mensajes Flash -->
    <div class="container mt-3">
        <div th:if="${exito}" class="alert alert-success alert-dismissible fade show">
            <span th:text="${exito}">Éxito</span>
            <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
        </div>
        <div th:if="${error}" class="alert alert-danger alert-dismissible fade show">
            <span th:text="${error}">Error</span>
            <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
        </div>
    </div>

    <!-- Contenido principal -->
    <main class="container mt-4">
        <th:block layout:fragment="contenido">
            <p>Contenido por defecto</p>
        </th:block>
    </main>

    <!-- Footer -->
    <footer class="footer mt-auto py-3 bg-light">
        <div class="container text-center">
            <span class="text-muted">
                Gestor de Tareas &copy;
                <span th:text="${#temporals.year(#temporals.createNow())}">2024</span>
            </span>
        </div>
    </footer>

    <script th:src="@{/webjars/bootstrap/5.3.0/js/bootstrap.bundle.min.js}"></script>
    <th:block layout:fragment="scripts"></th:block>
</body>
</html>
```

### Lista de Tareas

```html
<!-- templates/tareas/lista.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layouts/base}">
<head>
    <title>Mis Tareas</title>
</head>
<body>
    <th:block layout:fragment="contenido">
        <div class="d-flex justify-content-between align-items-center mb-4">
            <h1>📋 Mis Tareas</h1>
            <a th:href="@{/tareas/nueva}" class="btn btn-primary">
                ➕ Nueva Tarea
            </a>
        </div>

        <!-- Tabla de tareas -->
        <div class="card">
            <div class="card-body">
                <div th:if="${tareas.isEmpty()}" class="text-center py-5">
                    <p class="text-muted">No tienes tareas. ¡Crea una!</p>
                    <a th:href="@{/tareas/nueva}" class="btn btn-primary">
                        Crear primera tarea
                    </a>
                </div>

                <table th:unless="${tareas.isEmpty()}"
                       class="table table-hover table-striped">
                    <thead class="table-dark">
                        <tr>
                            <th>#</th>
                            <th>Título</th>
                            <th>Prioridad</th>
                            <th>Estado</th>
                            <th>Vencimiento</th>
                            <th>Acciones</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr th:each="tarea, stat : ${tareas}"
                            th:classappend="${tarea.estado.name() == 'COMPLETADA'} ? ' table-success' :
                                           (${tarea.estado.name() == 'CANCELADA'} ? ' table-secondary' : '')">
                            <td th:text="${stat.count}">1</td>
                            <td>
                                <strong th:text="${tarea.titulo}">Título</strong>
                                <p th:if="${tarea.descripcion != null and !tarea.descripcion.isEmpty()}"
                                   th:text="${#strings.abbreviate(tarea.descripcion, 60)}"
                                   class="text-muted small mb-0">
                                    Descripción
                                </p>
                            </td>
                            <td>
                                <span th:class="|badge ${tarea.prioridad.claseBadge}|"
                                      th:text="${tarea.prioridad}">
                                    Prioridad
                                </span>
                            </td>
                            <td>
                                <!-- Formulario para cambiar estado inline -->
                                <form th:action="@{/tareas/estado/{id}(id=${tarea.id})}"
                                      method="post" class="d-inline">
                                    <input type="hidden"
                                           th:name="${_csrf.parameterName}"
                                           th:value="${_csrf.token}">
                                    <select name="estado"
                                            class="form-select form-select-sm"
                                            onchange="this.form.submit()"
                                            th:disabled="${tarea.estado.name() == 'CANCELADA'}">
                                        <option th:each="estado : ${estados}"
                                                th:value="${estado}"
                                                th:text="${estado}"
                                                th:selected="${tarea.estado == estado}">
                                            Estado
                                        </option>
                                    </select>
                                </form>
                            </td>
                            <td>
                                <span th:if="${tarea.fechaVencimiento != null}"
                                      th:text="${#temporals.format(tarea.fechaVencimiento, 'dd/MM/yyyy')}"
                                      th:class="${tarea.fechaVencimiento.isBefore(#temporals.createToday()) and
                                                 tarea.estado.name() != 'COMPLETADA'} ? 'text-danger fw-bold' : ''">
                                    Fecha
                                </span>
                                <span th:unless="${tarea.fechaVencimiento != null}"
                                      class="text-muted">—</span>
                            </td>
                            <td>
                                <a th:href="@{/tareas/editar/{id}(id=${tarea.id})}"
                                   class="btn btn-sm btn-outline-primary">
                                    ✏️ Editar
                                </a>
                                <form th:action="@{/tareas/eliminar/{id}(id=${tarea.id})}"
                                      method="post"
                                      class="d-inline"
                                      onsubmit="return confirm('¿Eliminar esta tarea?')">
                                    <input type="hidden"
                                           th:name="${_csrf.parameterName}"
                                           th:value="${_csrf.token}">
                                    <button type="submit"
                                            class="btn btn-sm btn-outline-danger">
                                        🗑️ Eliminar
                                    </button>
                                </form>
                            </td>
                        </tr>
                    </tbody>
                </table>

                <!-- Paginación -->
                <nav th:if="${totalPaginas > 1}">
                    <ul class="pagination justify-content-center">
                        <li th:class="${paginaActual == 0} ? 'page-item disabled' : 'page-item'">
                            <a class="page-link"
                               th:href="@{/tareas(pagina=${paginaActual - 1})}">
                                Anterior
                            </a>
                        </li>
                        <li th:each="i : ${#numbers.sequence(0, totalPaginas - 1)}"
                            th:class="${i == paginaActual} ? 'page-item active' : 'page-item'">
                            <a class="page-link"
                               th:href="@{/tareas(pagina=${i})}"
                               th:text="${i + 1}">1</a>
                        </li>
                        <li th:class="${paginaActual == totalPaginas - 1} ? 'page-item disabled' : 'page-item'">
                            <a class="page-link"
                               th:href="@{/tareas(pagina=${paginaActual + 1})}">
                                Siguiente
                            </a>
                        </li>
                    </ul>
                </nav>
            </div>
        </div>
    </th:block>
</body>
</html>
```

### Formulario de Tarea

```html
<!-- templates/tareas/formulario.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      layout:decorate="~{layouts/base}">
<head>
    <title th:text="${tarea.id != null} ? 'Editar Tarea' : 'Nueva Tarea'">Tarea</title>
</head>
<body>
    <th:block layout:fragment="contenido">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header">
                        <h2 th:text="${tarea.id != null} ? '✏️ Editar Tarea' : '➕ Nueva Tarea'">
                            Tarea
                        </h2>
                    </div>
                    <div class="card-body">
                        <form th:action="@{/tareas/guardar}"
                              th:object="${tarea}"
                              method="post"
                              novalidate>

                            <!-- ID oculto para edición -->
                            <input type="hidden" th:field="*{id}">

                            <!-- Título -->
                            <div class="mb-3">
                                <label for="titulo" class="form-label">
                                    Título <span class="text-danger">*</span>
                                </label>
                                <input type="text"
                                       id="titulo"
                                       class="form-control"
                                       th:field="*{titulo}"
                                       th:classappend="${#fields.hasErrors('titulo')} ? ' is-invalid' : ''"
                                       placeholder="Describe brevemente la tarea">
                                <div th:if="${#fields.hasErrors('titulo')}"
                                     class="invalid-feedback"
                                     th:errors="*{titulo}">
                                    Error
                                </div>
                            </div>

                            <!-- Descripción -->
                            <div class="mb-3">
                                <label for="descripcion" class="form-label">Descripción</label>
                                <textarea id="descripcion"
                                          class="form-control"
                                          th:field="*{descripcion}"
                                          rows="3"
                                          placeholder="Detalles adicionales (opcional)"></textarea>
                                <div th:if="${#fields.hasErrors('descripcion')}"
                                     class="invalid-feedback d-block"
                                     th:errors="*{descripcion}">
                                    Error
                                </div>
                            </div>

                            <!-- Prioridad y Fecha en la misma fila -->
                            <div class="row">
                                <div class="col-md-6 mb-3">
                                    <label for="prioridad" class="form-label">
                                        Prioridad <span class="text-danger">*</span>
                                    </label>
                                    <select id="prioridad"
                                            class="form-select"
                                            th:field="*{prioridad}"
                                            th:classappend="${#fields.hasErrors('prioridad')} ? ' is-invalid' : ''">
                                        <option value="">-- Seleccionar --</option>
                                        <option th:each="p : ${prioridades}"
                                                th:value="${p}"
                                                th:text="${p}">
                                            Prioridad
                                        </option>
                                    </select>
                                    <div th:if="${#fields.hasErrors('prioridad')}"
                                         class="invalid-feedback"
                                         th:errors="*{prioridad}">
                                        Error
                                    </div>
                                </div>

                                <div class="col-md-6 mb-3">
                                    <label for="fechaVencimiento" class="form-label">
                                        Fecha de vencimiento
                                    </label>
                                    <input type="date"
                                           id="fechaVencimiento"
                                           class="form-control"
                                           th:field="*{fechaVencimiento}">
                                </div>
                            </div>

                            <!-- Botones -->
                            <div class="d-flex gap-2">
                                <button type="submit" class="btn btn-primary">
                                    💾 Guardar
                                </button>
                                <a th:href="@{/tareas}" class="btn btn-secondary">
                                    ❌ Cancelar
                                </a>
                            </div>

                            <!-- CSRF -->
                            <input type="hidden"
                                   th:name="${_csrf.parameterName}"
                                   th:value="${_csrf.token}">
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </th:block>
</body>
</html>
```

---

## 15. Buenas Prácticas y Rendimiento

### Buenas Prácticas

#### 1. Usar `th:text` en lugar de `th:utext`

```html
<!-- ✅ Correcto: escapa el HTML, previene XSS -->
<p th:text="${comentarioUsuario}">Comentario</p>

<!-- ❌ Peligroso: no escapa, posible XSS si viene de usuario -->
<p th:utext="${comentarioUsuario}">Comentario</p>
```

#### 2. Usar `th:replace` en lugar de `th:include`

```html
<!-- ✅ Preferido: reemplaza el elemento completo -->
<div th:replace="~{fragments/header :: header}"></div>

<!-- ⚠️ Deprecado: usa th:insert o th:replace -->
<div th:include="~{fragments/header :: header}"></div>
```

#### 3. Usar expresiones URL `@{...}` siempre

```html
<!-- ✅ Correcto: gestiona el context path automáticamente -->
<a th:href="@{/productos}">Productos</a>

<!-- ❌ Incorrecto: falla si la app no está en raíz -->
<a href="/productos">Productos</a>
```

#### 4. Organizar plantillas en carpetas por dominio

```
templates/
├── layouts/      ← Layouts base
├── fragments/    ← Componentes reutilizables
├── usuarios/     ← Plantillas de usuarios
├── productos/    ← Plantillas de productos
└── error/        ← Páginas de error (404, 500, etc.)
```

#### 5. Páginas de error personalizadas

```html
<!-- templates/error/404.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
    <h1>404 - Página no encontrada</h1>
    <p th:text="${#request.requestURL}">URL</p>
    <a th:href="@{/}">Volver al inicio</a>
</body>
</html>

<!-- templates/error/500.html -->
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
    <h1>500 - Error interno del servidor</h1>
    <p>Lo sentimos, algo salió mal.</p>
    <a th:href="@{/}">Volver al inicio</a>
</body>
</html>
```

#### 6. Usar `th:with` para evitar cálculos repetidos

```html
<!-- ❌ Repetitivo y menos eficiente -->
<span th:text="${producto.precio * 1.21}">Precio</span>
<span th:text="${producto.precio * 1.21 * 0.5}">Oferta</span>

<!-- ✅ Calcula una sola vez -->
<div th:with="precioConIva=${producto.precio * 1.21}">
    <span th:text="${precioConIva}">Precio</span>
    <span th:text="${precioConIva * 0.5}">Oferta</span>
</div>
```

#### 7. Validación del lado del servidor siempre

```java
// ✅ SIEMPRE validar en el controlador, no solo en la plantilla
@PostMapping("/guardar")
public String guardar(@Valid @ModelAttribute ProductoForm form,
                       BindingResult result) {
    if (result.hasErrors()) {
        return "productos/formulario";
    }
    // ...
}
```

### Rendimiento

#### Activar caché en producción

```properties
# application-prod.properties
spring.thymeleaf.cache=true
```

#### DevTools para recargar sin reiniciar (desarrollo)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

#### Compilador SpEL

```java
// Mejora el rendimiento de evaluación de expresiones
@Bean
public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
    SpringTemplateEngine engine = new SpringTemplateEngine();
    engine.setTemplateResolver(templateResolver);
    engine.setEnableSpringELCompiler(true);  // Activar
    return engine;
}
```

#### Minimizar lógica en plantillas

```java
// ✅ Prepara los datos en el controlador o servicio
model.addAttribute("productosActivos", productoService.findActivos());
model.addAttribute("totalProductos", productoService.count());

// En lugar de hacer cálculos complejos en la plantilla
```

### Depuración

#### Ver el HTML generado

Con las herramientas de desarrollador del navegador (F12 → Inspector), puedes ver el HTML final generado por Thymeleaf.

#### Logs de Thymeleaf

```properties
# application.properties
logging.level.org.thymeleaf=DEBUG
logging.level.org.springframework.web=DEBUG
```

#### Verificar que la plantilla existe

```java
// Si obtienes "Error resolving template", verifica:
// 1. La plantilla está en src/main/resources/templates/
// 2. El nombre devuelto por el controlador coincide exactamente
// 3. La extensión es .html (o la configurada)
@GetMapping("/ruta")
public String metodo() {
    return "carpeta/nombre-plantilla";  // Sin extensión .html
}
```

---

## 16. Recursos Adicionales

### Documentación Oficial

- 📖 [Thymeleaf - Documentación Oficial](https://www.thymeleaf.org/documentation.html)
- 📖 [Tutorial: Thymeleaf + Spring](https://www.thymeleaf.org/doc/tutorials/3.1/thymeleafspring.html)
- 📖 [Tutorial: Uso de Thymeleaf](https://www.thymeleaf.org/doc/tutorials/3.1/usingthymeleaf.html)
- 📖 [Spring Boot - Thymeleaf](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#web.servlet.spring-mvc.template-engines)

### Extensiones y Dialectos

- 🔧 [Thymeleaf Layout Dialect](https://github.com/ultraq/thymeleaf-layout-dialect) - Sistema de layouts decoradores
- 🔧 [Thymeleaf Extras Spring Security](https://github.com/thymeleaf/thymeleaf-extras-springsecurity) - Integración con Spring Security
- 🔧 [Thymeleaf Extras Java 8 Time](https://github.com/thymeleaf/thymeleaf-extras-java8time) - Soporte `java.time.*`

### Herramientas

- 🛠️ [Spring Initializr](https://start.spring.io/) - Generador de proyectos Spring Boot
- 🛠️ [Thymeleaf Plugin para IntelliJ IDEA](https://plugins.jetbrains.com/plugin/9461-thymeleaf) - Autocompletado y validación
- 🛠️ [Eclipse Thymeleaf Plugin](https://marketplace.eclipse.org/content/thymeleaf-plugin-eclipse)

### Libros y Cursos

- 📚 [Spring in Action (Manning)](https://www.manning.com/books/spring-in-action-sixth-edition)
- 📚 [Learning Spring Boot (Packt)](https://www.packtpub.com/product/learning-spring-boot-3-0/9781803233307)

### Plantillas de Bootstrap para Spring Boot + Thymeleaf

- 🎨 [SB Admin 2](https://startbootstrap.com/theme/sb-admin-2)
- 🎨 [AdminLTE](https://adminlte.io/)

---

## Resumen de Atributos Principales

| Atributo | Descripción |
|---|---|
| `th:text` | Establece el texto del elemento (con escape HTML) |
| `th:utext` | Establece texto sin escape HTML (cuidado con XSS) |
| `th:href` | URL de un enlace `<a>` |
| `th:src` | Fuente de imagen o script |
| `th:action` | Acción de un formulario |
| `th:value` | Valor de un input |
| `th:field` | Binding bidireccional con objeto de formulario |
| `th:object` | Selecciona un objeto para usar con `*{...}` |
| `th:each` | Iteración sobre colecciones |
| `th:if` | Muestra el elemento si la condición es verdadera |
| `th:unless` | Muestra el elemento si la condición es falsa |
| `th:switch` / `th:case` | Estructura switch-case |
| `th:fragment` | Define un fragmento reutilizable |
| `th:replace` | Reemplaza el elemento por un fragmento |
| `th:insert` | Inserta un fragmento dentro del elemento |
| `th:class` | Establece el atributo class |
| `th:classappend` | Añade clases CSS al atributo class existente |
| `th:with` | Define variables locales |
| `th:remove` | Elimina el elemento según la opción |
| `th:inline` | Activa el modo inline (text, javascript, css) |
| `th:attr` | Establece atributos arbitrarios |
| `th:attrappend` | Añade al valor de un atributo existente |

---

> ⭐ **¡Thymeleaf es una excelente elección para desarrollar aplicaciones web con Spring Boot!** Su integración natural con Spring MVC, la filosofía de Natural Templating y su rica biblioteca de expresiones y utilidades lo convierten en una herramienta poderosa y fácil de aprender.
