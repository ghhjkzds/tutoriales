# 🧪 Testing en Aplicaciones - Tutorial Completo

## Introducción

El **testing** (pruebas de software) es una práctica fundamental en el desarrollo de aplicaciones modernas. Escribir pruebas te permite detectar errores de forma temprana, refactorizar con confianza, documentar el comportamiento esperado del código y colaborar más fácilmente en equipo.

Este tutorial cubre los conceptos clave del testing con ejemplos prácticos en **Spring Boot (Java)**, **Python** y **Go**.

---

## 📋 Tabla de Contenidos

1. [Conceptos Fundamentales de Testing](#1-conceptos-fundamentales-de-testing)
2. [Tipos de Pruebas](#2-tipos-de-pruebas)
3. [Testing en Spring Boot (Java)](#3-testing-en-spring-boot-java)
4. [Testing en Python](#4-testing-en-python)
5. [Testing en Go](#5-testing-en-go)
6. [Cobertura de Código](#6-cobertura-de-código)
7. [Testing en CI/CD](#7-testing-en-cicd)
8. [Buenas Prácticas](#8-buenas-prácticas)
9. [Recursos Adicionales](#9-recursos-adicionales)

---

## 1. Conceptos Fundamentales de Testing

### ¿Por qué hacer testing?

```
Sin tests                          Con tests
┌──────────────────────────┐      ┌──────────────────────────────────────┐
│ Miedo a refactorizar     │      │ Refactorización segura                │
│ Bugs en producción       │      │ Detección temprana de errores         │
│ Documentación obsoleta   │      │ Tests como documentación viva         │
│ Integración dolorosa     │      │ Integración continua (CI) fiable      │
│ Deuda técnica acumulada  │      │ Código más limpio y mantenible        │
└──────────────────────────┘      └──────────────────────────────────────┘
```

### La Pirámide de Testing

```
           /\
          /  \
         / E2E\         ← Pocos, lentos, costosos
        /──────\
       /  Integ. \      ← Cantidad media
      /────────────\
     /  Unit Tests  \   ← Muchos, rápidos, baratos
    /────────────────\
```

| Nivel | Qué prueba | Velocidad | Costo |
|-------|-----------|-----------|-------|
| **Unitario** | Una función / clase en aislamiento | Muy rápido | Bajo |
| **Integración** | Varios componentes juntos | Medio | Medio |
| **Extremo a extremo (E2E)** | El sistema completo, como usuario real | Lento | Alto |

### Conceptos Clave

| Término | Definición |
|---------|-----------|
| **SUT** (System Under Test) | El código que estás probando |
| **Test doble** | Objeto que reemplaza a una dependencia real |
| **Mock** | Imita el comportamiento de un objeto; verifica interacciones |
| **Stub** | Devuelve respuestas predefinidas sin lógica real |
| **Spy** | Objeto real que también registra cómo fue llamado |
| **Fixture** | Datos o estado inicial necesarios para los tests |
| **AAA** | Patrón **Arrange → Act → Assert** |

### El Patrón AAA

```
// Arrange: prepara el contexto y los datos
// Act:     ejecuta el código bajo prueba
// Assert:  verifica que el resultado es el esperado
```

---

## 2. Tipos de Pruebas

### 2.1 Pruebas Unitarias

Prueban una sola unidad de código (función, método, clase) en completo aislamiento. Todas las dependencias externas (bases de datos, APIs, etc.) se reemplazan por dobles.

**Características:**
- Rápidas (ms)
- Deterministas (mismo resultado siempre)
- Sin efectos secundarios

### 2.2 Pruebas de Integración

Prueban la colaboración entre varios componentes: servicio + repositorio + base de datos, controlador + servicio, etc.

**Características:**
- Más lentas que las unitarias
- Pueden usar base de datos real (H2, SQLite) o en memoria
- Detectan problemas de configuración y contrato entre capas

### 2.3 Pruebas de Extremo a Extremo (E2E)

Simulan el flujo completo del usuario: desde la petición HTTP hasta la respuesta, pasando por todas las capas.

**Características:**
- Lentas y costosas de mantener
- Alta confianza en el comportamiento real
- Herramientas: Selenium, Playwright, Cypress, Postman/Newman

### 2.4 Pruebas de Rendimiento

Verifican que el sistema responde dentro de los tiempos aceptables bajo carga.
Herramientas: JMeter, Gatling, k6, Locust.

### 2.5 Pruebas de Regresión

Garantizan que los cambios nuevos no rompen funcionalidades existentes. Normalmente son la suite de tests unitarios + integración ejecutada en cada PR.

---

## 3. Testing en Spring Boot (Java)

### 3.1 Dependencias

Añade en tu `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

`spring-boot-starter-test` incluye automáticamente:
- **JUnit 5** – framework de tests
- **Mockito** – mocking
- **AssertJ** – aserciones fluidas
- **Hamcrest** – matchers
- **JSONPath** – validación de JSON
- **MockMvc** – pruebas de controladores HTTP

### 3.2 Pruebas Unitarias con JUnit 5 y Mockito

Supón que tienes esta lógica de negocio:

```java
// src/main/java/com/ejemplo/service/ProductoService.java
@Service
public class ProductoService {

    private final ProductoRepository productoRepository;

    public ProductoService(ProductoRepository productoRepository) {
        this.productoRepository = productoRepository;
    }

    public Producto crearProducto(String nombre, double precio) {
        if (nombre == null || nombre.isBlank()) {
            throw new IllegalArgumentException("El nombre no puede estar vacío");
        }
        if (precio <= 0) {
            throw new IllegalArgumentException("El precio debe ser mayor que cero");
        }
        Producto producto = new Producto(nombre, precio);
        return productoRepository.save(producto);
    }

    public List<Producto> obtenerProductosCaros(double precioMinimo) {
        return productoRepository.findByPrecioGreaterThan(precioMinimo);
    }
}
```

Su test unitario:

```java
// src/test/java/com/ejemplo/service/ProductoServiceTest.java
import org.junit.jupiter.api.*;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.*;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.List;

import static org.assertj.core.api.Assertions.*;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)  // activa Mockito con JUnit 5
class ProductoServiceTest {

    @Mock
    private ProductoRepository productoRepository; // mock de la dependencia

    @InjectMocks
    private ProductoService productoService;       // SUT con el mock inyectado

    // ─── crearProducto ───────────────────────────────────────────────────────

    @Test
    @DisplayName("crearProducto: guarda y devuelve el producto correctamente")
    void crearProducto_datosValidos_devuelveProductoGuardado() {
        // Arrange
        Producto productoGuardado = new Producto("Laptop", 1200.0);
        productoGuardado.setId(1L);
        when(productoRepository.save(any(Producto.class))).thenReturn(productoGuardado);

        // Act
        Producto resultado = productoService.crearProducto("Laptop", 1200.0);

        // Assert
        assertThat(resultado.getId()).isEqualTo(1L);
        assertThat(resultado.getNombre()).isEqualTo("Laptop");
        assertThat(resultado.getPrecio()).isEqualTo(1200.0);
        verify(productoRepository, times(1)).save(any(Producto.class)); // verificar interacción
    }

    @Test
    @DisplayName("crearProducto: lanza excepción si el nombre está vacío")
    void crearProducto_nombreVacio_lanzaExcepcion() {
        assertThatThrownBy(() -> productoService.crearProducto("", 100.0))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessageContaining("nombre");

        verifyNoInteractions(productoRepository); // el repositorio nunca se debe llamar
    }

    @Test
    @DisplayName("crearProducto: lanza excepción si el precio es negativo")
    void crearProducto_precioNegativo_lanzaExcepcion() {
        assertThatThrownBy(() -> productoService.crearProducto("Laptop", -50.0))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessageContaining("precio");
    }

    // ─── obtenerProductosCaros ────────────────────────────────────────────────

    @Test
    @DisplayName("obtenerProductosCaros: delega la consulta al repositorio")
    void obtenerProductosCaros_devuelveListaDelRepositorio() {
        // Arrange
        List<Producto> esperados = List.of(
                new Producto("Laptop", 1200.0),
                new Producto("Tablet", 800.0)
        );
        when(productoRepository.findByPrecioGreaterThan(500.0)).thenReturn(esperados);

        // Act
        List<Producto> resultado = productoService.obtenerProductosCaros(500.0);

        // Assert
        assertThat(resultado).hasSize(2);
        assertThat(resultado).extracting(Producto::getNombre)
                             .containsExactly("Laptop", "Tablet");
    }
}
```

### 3.3 Anotaciones Clave de JUnit 5

| Anotación | Descripción |
|-----------|-------------|
| `@Test` | Marca un método como caso de prueba |
| `@DisplayName` | Nombre legible para el test |
| `@BeforeEach` | Se ejecuta antes de cada test |
| `@AfterEach` | Se ejecuta después de cada test |
| `@BeforeAll` | Se ejecuta una vez antes de todos los tests |
| `@AfterAll` | Se ejecuta una vez al final |
| `@Disabled` | Deshabilita un test temporalmente |
| `@ParameterizedTest` | Test con múltiples entradas |
| `@ValueSource` | Fuente de valores para tests parametrizados |
| `@CsvSource` | Fuente de pares CSV para tests parametrizados |
| `@Nested` | Agrupa tests relacionados en clases internas |

### 3.4 Tests Parametrizados

```java
@ParameterizedTest
@ValueSource(strings = {"", " ", "\t", "\n"})
@DisplayName("crearProducto: rechaza cualquier nombre en blanco")
void crearProducto_nombreEnBlanco_lanzaExcepcion(String nombreInvalido) {
    assertThatThrownBy(() -> productoService.crearProducto(nombreInvalido, 100.0))
            .isInstanceOf(IllegalArgumentException.class);
}

@ParameterizedTest
@CsvSource({
    "Laptop, 1200.0",
    "Teclado, 45.99",
    "Monitor, 350.0"
})
@DisplayName("crearProducto: acepta nombres y precios válidos")
void crearProducto_datosValidos(String nombre, double precio) {
    when(productoRepository.save(any())).thenAnswer(inv -> inv.getArgument(0));

    Producto resultado = productoService.crearProducto(nombre, precio);

    assertThat(resultado.getNombre()).isEqualTo(nombre);
    assertThat(resultado.getPrecio()).isEqualTo(precio);
}
```

### 3.5 Tests de Controladores con MockMvc (`@WebMvcTest`)

`@WebMvcTest` carga únicamente la capa web (controladores, filtros, etc.) sin levantar el contexto completo de Spring. Es más rápido que `@SpringBootTest`.

```java
// src/main/java/com/ejemplo/controller/ProductoController.java
@RestController
@RequestMapping("/api/productos")
public class ProductoController {

    private final ProductoService productoService;

    public ProductoController(ProductoService productoService) {
        this.productoService = productoService;
    }

    @GetMapping
    public List<Producto> listarTodos() {
        return productoService.obtenerTodos();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Producto> obtenerPorId(@PathVariable Long id) {
        return productoService.buscarPorId(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PostMapping
    public ResponseEntity<Producto> crear(@RequestBody @Valid ProductoRequest request) {
        Producto creado = productoService.crearProducto(request.getNombre(), request.getPrecio());
        return ResponseEntity.status(HttpStatus.CREATED).body(creado);
    }
}
```

```java
// src/test/java/com/ejemplo/controller/ProductoControllerTest.java
import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.hamcrest.Matchers.*;

@WebMvcTest(ProductoController.class)
class ProductoControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean                          // mock gestionado por Spring
    private ProductoService productoService;

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    @DisplayName("GET /api/productos → 200 con lista de productos")
    void listarTodos_devuelveListaJson() throws Exception {
        // Arrange
        List<Producto> productos = List.of(
                new Producto(1L, "Laptop", 1200.0),
                new Producto(2L, "Teclado", 45.99)
        );
        when(productoService.obtenerTodos()).thenReturn(productos);

        // Act & Assert
        mockMvc.perform(get("/api/productos")
                        .contentType(MediaType.APPLICATION_JSON))
               .andExpect(status().isOk())
               .andExpect(jsonPath("$", hasSize(2)))
               .andExpect(jsonPath("$[0].nombre", is("Laptop")))
               .andExpect(jsonPath("$[1].nombre", is("Teclado")));
    }

    @Test
    @DisplayName("GET /api/productos/{id} → 404 si no existe")
    void obtenerPorId_noExiste_devuelve404() throws Exception {
        when(productoService.buscarPorId(99L)).thenReturn(Optional.empty());

        mockMvc.perform(get("/api/productos/99"))
               .andExpect(status().isNotFound());
    }

    @Test
    @DisplayName("POST /api/productos → 201 con el producto creado")
    void crear_datosValidos_devuelve201() throws Exception {
        // Arrange
        ProductoRequest request = new ProductoRequest("Monitor", 350.0);
        Producto creado = new Producto(3L, "Monitor", 350.0);
        when(productoService.crearProducto("Monitor", 350.0)).thenReturn(creado);

        // Act & Assert
        mockMvc.perform(post("/api/productos")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(request)))
               .andExpect(status().isCreated())
               .andExpect(jsonPath("$.id", is(3)))
               .andExpect(jsonPath("$.nombre", is("Monitor")));
    }

    @Test
    @DisplayName("POST /api/productos → 400 si los datos son inválidos")
    void crear_datosInvalidos_devuelve400() throws Exception {
        ProductoRequest requestInvalida = new ProductoRequest("", -10.0);

        mockMvc.perform(post("/api/productos")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(requestInvalida)))
               .andExpect(status().isBadRequest());
    }
}
```

### 3.6 Tests de Repositorios con `@DataJpaTest`

`@DataJpaTest` carga únicamente la capa de persistencia y usa una base de datos en memoria (H2 por defecto).

```java
// src/test/java/com/ejemplo/repository/ProductoRepositoryTest.java
@DataJpaTest
class ProductoRepositoryTest {

    @Autowired
    private TestEntityManager entityManager;

    @Autowired
    private ProductoRepository productoRepository;

    @BeforeEach
    void setUp() {
        entityManager.persist(new Producto("Laptop", 1200.0));
        entityManager.persist(new Producto("Teclado", 45.99));
        entityManager.persist(new Producto("Monitor", 350.0));
        entityManager.flush();
    }

    @Test
    @DisplayName("findByPrecioGreaterThan: devuelve sólo los productos caros")
    void findByPrecioGreaterThan_devuelveProductosCorrectos() {
        List<Producto> caros = productoRepository.findByPrecioGreaterThan(100.0);

        assertThat(caros).hasSize(2);
        assertThat(caros).extracting(Producto::getNombre)
                         .containsExactlyInAnyOrder("Laptop", "Monitor");
    }

    @Test
    @DisplayName("findByNombreContainingIgnoreCase: búsqueda insensible a mayúsculas")
    void buscarPorNombre_insensibleMayusculas() {
        List<Producto> resultado = productoRepository
                .findByNombreContainingIgnoreCase("laptop");

        assertThat(resultado).hasSize(1);
        assertThat(resultado.get(0).getNombre()).isEqualTo("Laptop");
    }
}
```

### 3.7 Tests de Integración Completos con `@SpringBootTest`

`@SpringBootTest` levanta el contexto completo de la aplicación. Úsalo para pruebas de integración o E2E.

```java
// src/test/java/com/ejemplo/integration/ProductoIntegrationTest.java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.ANY) // usa H2
class ProductoIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private ProductoRepository productoRepository;

    @BeforeEach
    void limpiarBD() {
        productoRepository.deleteAll();
    }

    @Test
    @DisplayName("Flujo completo: crear producto y luego obtenerlo")
    void flujoCrearYObtener() {
        // Crear
        ProductoRequest request = new ProductoRequest("Auriculares", 89.99);
        ResponseEntity<Producto> respuestaCrear = restTemplate.postForEntity(
                "/api/productos", request, Producto.class);

        assertThat(respuestaCrear.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        Long idCreado = respuestaCrear.getBody().getId();

        // Obtener por ID
        ResponseEntity<Producto> respuestaObtener = restTemplate.getForEntity(
                "/api/productos/" + idCreado, Producto.class);

        assertThat(respuestaObtener.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(respuestaObtener.getBody().getNombre()).isEqualTo("Auriculares");
    }
}
```

### 3.8 Stubbing Avanzado con Mockito

```java
// Lanzar excepción
when(productoRepository.save(any())).thenThrow(new RuntimeException("Error BD"));

// Responder con el argumento recibido (muy útil)
when(productoRepository.save(any())).thenAnswer(inv -> inv.getArgument(0));

// Comportamiento diferente en llamadas sucesivas
when(productoRepository.count())
        .thenReturn(0L)   // primera llamada
        .thenReturn(1L);  // segunda llamada

// Capturar argumentos para inspeccionarlos
ArgumentCaptor<Producto> captor = ArgumentCaptor.forClass(Producto.class);
verify(productoRepository).save(captor.capture());
assertThat(captor.getValue().getNombre()).isEqualTo("Laptop");

// Verificar que NUNCA se llamó
verify(productoRepository, never()).delete(any());

// Verificar orden de llamadas
InOrder inOrder = inOrder(productoRepository, eventoService);
inOrder.verify(productoRepository).save(any());
inOrder.verify(eventoService).publicar(any());
```

### 3.9 Testing con WireMock (APIs externas)

Cuando tu servicio consume una API REST externa, usa WireMock para simularla:

```xml
<dependency>
    <groupId>com.github.tomakehurst</groupId>
    <artifactId>wiremock-jre8</artifactId>
    <scope>test</scope>
</dependency>
```

```java
@SpringBootTest
@AutoConfigureWireMock(port = 0) // puerto aleatorio
class PagoServiceTest {

    @Value("${wiremock.server.port}")
    private int wireMockPort;

    @Test
    void procesarPago_servicioExternoOk_devuelveConfirmacion() {
        // Configurar el stub de la API externa
        stubFor(post(urlEqualTo("/api/pagos"))
                .willReturn(aResponse()
                        .withStatus(200)
                        .withHeader("Content-Type", "application/json")
                        .withBody("{\"transaccionId\": \"TXN-123\", \"estado\": \"APROBADO\"}")));

        // Ejecutar el servicio que internamente llama a la API
        Confirmacion confirmacion = pagoService.procesar(new PagoRequest(100.0, "4111..."));

        assertThat(confirmacion.getTransaccionId()).isEqualTo("TXN-123");
        assertThat(confirmacion.getEstado()).isEqualTo("APROBADO");
    }
}
```

### 3.10 Resumen de Anotaciones de Spring Boot Test

| Anotación | Descripción |
|-----------|-------------|
| `@SpringBootTest` | Contexto completo; tests de integración/E2E |
| `@WebMvcTest` | Solo capa web; requiere `@MockBean` para servicios |
| `@DataJpaTest` | Solo capa JPA + H2; repositorios |
| `@MockBean` | Crea un mock de Spring y lo inyecta en el contexto |
| `@SpyBean` | Espía un bean real de Spring |
| `@AutoConfigureMockMvc` | Habilita MockMvc en contexto completo |
| `@TestPropertySource` | Propiedades específicas para tests |
| `@Sql` | Ejecuta scripts SQL antes/después de un test |

---

## 4. Testing en Python

### 4.1 Frameworks Principales

| Framework | Descripción |
|-----------|-------------|
| `unittest` | Módulo de la biblioteca estándar; estilo xUnit |
| `pytest` | El más popular; sintaxis simple, plugins potentes |
| `nose2` | Sucesor de nose; menos utilizado hoy en día |

> 💡 **Recomendación**: Usa **pytest** para proyectos nuevos. Es más sencillo, más expresivo y tiene un ecosistema de plugins enorme.

### 4.2 Instalación

```bash
pip install pytest pytest-cov pytest-mock
```

### 4.3 Código de Ejemplo

```python
# src/producto_service.py
from dataclasses import dataclass, field
from typing import List, Optional


@dataclass
class Producto:
    nombre: str
    precio: float
    id: Optional[int] = None


class ProductoRepository:
    """Interfaz (protocolo) del repositorio."""
    def guardar(self, producto: Producto) -> Producto: ...
    def buscar_por_id(self, id: int) -> Optional[Producto]: ...
    def buscar_por_precio_mayor_a(self, precio_minimo: float) -> List[Producto]: ...


class ProductoService:
    def __init__(self, repositorio: ProductoRepository):
        self._repositorio = repositorio

    def crear_producto(self, nombre: str, precio: float) -> Producto:
        if not nombre or not nombre.strip():
            raise ValueError("El nombre no puede estar vacío")
        if precio <= 0:
            raise ValueError("El precio debe ser mayor que cero")
        producto = Producto(nombre=nombre, precio=precio)
        return self._repositorio.guardar(producto)

    def obtener_productos_caros(self, precio_minimo: float) -> List[Producto]:
        return self._repositorio.buscar_por_precio_mayor_a(precio_minimo)
```

### 4.4 Pruebas Unitarias con pytest

```python
# tests/test_producto_service.py
import pytest
from unittest.mock import MagicMock, patch, call
from src.producto_service import Producto, ProductoService


@pytest.fixture
def repositorio_mock():
    """Fixture que crea un mock del repositorio para cada test."""
    return MagicMock()


@pytest.fixture
def servicio(repositorio_mock):
    """Fixture que crea el servicio con el repositorio mock."""
    return ProductoService(repositorio=repositorio_mock)


# ─── crear_producto ───────────────────────────────────────────────────────────

class TestCrearProducto:

    def test_datos_validos_devuelve_producto_guardado(self, servicio, repositorio_mock):
        # Arrange
        producto_guardado = Producto(nombre="Laptop", precio=1200.0, id=1)
        repositorio_mock.guardar.return_value = producto_guardado

        # Act
        resultado = servicio.crear_producto("Laptop", 1200.0)

        # Assert
        assert resultado.id == 1
        assert resultado.nombre == "Laptop"
        assert resultado.precio == 1200.0
        repositorio_mock.guardar.assert_called_once()

    @pytest.mark.parametrize("nombre_invalido", ["", " ", "\t", "\n"])
    def test_nombre_en_blanco_lanza_excepcion(self, servicio, repositorio_mock, nombre_invalido):
        with pytest.raises(ValueError, match="nombre"):
            servicio.crear_producto(nombre_invalido, 100.0)

        repositorio_mock.guardar.assert_not_called()

    def test_precio_negativo_lanza_excepcion(self, servicio):
        with pytest.raises(ValueError, match="precio"):
            servicio.crear_producto("Laptop", -50.0)

    def test_precio_cero_lanza_excepcion(self, servicio):
        with pytest.raises(ValueError):
            servicio.crear_producto("Laptop", 0)


# ─── obtener_productos_caros ──────────────────────────────────────────────────

class TestObtenerProductosCaros:

    def test_delega_al_repositorio_y_devuelve_lista(self, servicio, repositorio_mock):
        # Arrange
        esperados = [
            Producto(nombre="Laptop", precio=1200.0),
            Producto(nombre="Monitor", precio=350.0),
        ]
        repositorio_mock.buscar_por_precio_mayor_a.return_value = esperados

        # Act
        resultado = servicio.obtener_productos_caros(100.0)

        # Assert
        assert len(resultado) == 2
        assert resultado[0].nombre == "Laptop"
        repositorio_mock.buscar_por_precio_mayor_a.assert_called_once_with(100.0)

    def test_devuelve_lista_vacia_si_no_hay_productos(self, servicio, repositorio_mock):
        repositorio_mock.buscar_por_precio_mayor_a.return_value = []

        resultado = servicio.obtener_productos_caros(9999.0)

        assert resultado == []
```

### 4.5 Pruebas con unittest

```python
# tests/test_producto_service_unittest.py
import unittest
from unittest.mock import MagicMock
from src.producto_service import Producto, ProductoService


class TestProductoService(unittest.TestCase):

    def setUp(self):
        """Se ejecuta antes de cada test."""
        self.repositorio_mock = MagicMock()
        self.servicio = ProductoService(repositorio=self.repositorio_mock)

    def test_crear_producto_valido(self):
        # Arrange
        self.repositorio_mock.guardar.return_value = Producto("Laptop", 1200.0, id=1)

        # Act
        resultado = self.servicio.crear_producto("Laptop", 1200.0)

        # Assert
        self.assertEqual(resultado.nombre, "Laptop")
        self.repositorio_mock.guardar.assert_called_once()

    def test_nombre_vacio_lanza_valor_error(self):
        with self.assertRaises(ValueError):
            self.servicio.crear_producto("", 100.0)

    def test_precio_negativo_lanza_valor_error(self):
        with self.assertRaises(ValueError):
            self.servicio.crear_producto("Laptop", -1.0)


if __name__ == "__main__":
    unittest.main()
```

### 4.6 Mocking Avanzado

```python
from unittest.mock import patch, MagicMock, PropertyMock

# Parchear una función de módulo externo
def test_llamada_a_api_externa(servicio):
    with patch("src.producto_service.requests.get") as mock_get:
        mock_get.return_value.json.return_value = {"precio": 99.99}
        mock_get.return_value.status_code = 200

        resultado = servicio.obtener_precio_externo("laptop")

        assert resultado == 99.99
        mock_get.assert_called_once_with("https://api.ejemplo.com/precios/laptop")

# Parchear con decorador
@patch("src.producto_service.requests.post")
def test_enviar_notificacion(mock_post, servicio):
    mock_post.return_value.ok = True

    servicio.notificar_creacion(Producto("Monitor", 350.0, id=5))

    mock_post.assert_called_once()
    args, kwargs = mock_post.call_args
    assert "Monitor" in str(kwargs.get("json", args))

# Parchear una propiedad
with patch.object(MiClase, "mi_propiedad", new_callable=PropertyMock) as mock_prop:
    mock_prop.return_value = "valor_mockeado"
    # ...
```

### 4.7 Fixtures y Configuración con pytest

```python
# tests/conftest.py  ← pytest carga este archivo automáticamente
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from src.models import Base


@pytest.fixture(scope="session")
def engine():
    """Motor de BD en memoria compartido por toda la sesión de tests."""
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    yield engine
    Base.metadata.drop_all(engine)


@pytest.fixture(scope="function")
def db_session(engine):
    """Sesión de BD que se revierte después de cada test."""
    connection = engine.connect()
    transaction = connection.begin()
    Session = sessionmaker(bind=connection)
    session = Session()

    yield session

    session.close()
    transaction.rollback()
    connection.close()


@pytest.fixture
def producto_ejemplo():
    return {"nombre": "Laptop Pro", "precio": 1499.99}
```

### 4.8 Testing de APIs con FastAPI

```python
# src/main.py
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class ProductoIn(BaseModel):
    nombre: str
    precio: float

@app.get("/productos/{id}")
def obtener_producto(id: int):
    producto = db.buscar(id)
    if not producto:
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    return producto

@app.post("/productos", status_code=201)
def crear_producto(data: ProductoIn):
    return db.guardar(data)
```

```python
# tests/test_api.py
from fastapi.testclient import TestClient
from unittest.mock import patch
from src.main import app

client = TestClient(app)


def test_obtener_producto_existente():
    with patch("src.main.db") as mock_db:
        mock_db.buscar.return_value = {"id": 1, "nombre": "Laptop", "precio": 1200.0}

        response = client.get("/productos/1")

        assert response.status_code == 200
        assert response.json()["nombre"] == "Laptop"


def test_obtener_producto_no_existente():
    with patch("src.main.db") as mock_db:
        mock_db.buscar.return_value = None

        response = client.get("/productos/999")

        assert response.status_code == 404


def test_crear_producto_valido():
    with patch("src.main.db") as mock_db:
        mock_db.guardar.return_value = {"id": 5, "nombre": "Monitor", "precio": 350.0}

        response = client.post("/productos", json={"nombre": "Monitor", "precio": 350.0})

        assert response.status_code == 201
        assert response.json()["id"] == 5
```

### 4.9 Testing con Django

```python
# tests/test_views.py
from django.test import TestCase, Client
from django.urls import reverse
from myapp.models import Producto


class ProductoViewTest(TestCase):

    def setUp(self):
        self.client = Client()
        Producto.objects.create(nombre="Laptop", precio=1200.0)

    def test_listar_productos_devuelve_200(self):
        response = self.client.get(reverse("productos-list"))
        self.assertEqual(response.status_code, 200)

    def test_listar_productos_contiene_laptop(self):
        response = self.client.get(reverse("productos-list"))
        self.assertContains(response, "Laptop")

    def test_crear_producto_redirige(self):
        response = self.client.post(reverse("productos-create"), {
            "nombre": "Teclado",
            "precio": 45.99,
        })
        self.assertEqual(response.status_code, 302)
        self.assertTrue(Producto.objects.filter(nombre="Teclado").exists())
```

### 4.10 Ejecutar Tests con pytest

```bash
# Ejecutar todos los tests
pytest

# Con salida detallada
pytest -v

# Tests de un archivo concreto
pytest tests/test_producto_service.py

# Tests que coincidan con un patrón de nombre
pytest -k "crear_producto"

# Detener al primer fallo
pytest -x

# Ver los más lentos
pytest --durations=10

# Con cobertura
pytest --cov=src --cov-report=html
```

---

## 5. Testing en Go

### 5.1 Testing en Go: Lo Básico

Go incluye testing nativo con el paquete `testing`. No necesitas instalar nada extra para pruebas básicas.

```bash
# Ejecutar todos los tests del módulo
go test ./...

# Con detalles
go test -v ./...

# Tests de un paquete concreto
go test ./internal/producto/...

# Con cobertura
go test -cover ./...
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out
```

### 5.2 Código de Ejemplo

```go
// internal/producto/producto.go
package producto

import "errors"

// Producto representa un artículo en el catálogo.
type Producto struct {
    ID     int
    Nombre string
    Precio float64
}

// Repositorio define las operaciones de persistencia.
type Repositorio interface {
    Guardar(p Producto) (Producto, error)
    BuscarPorID(id int) (Producto, error)
    BuscarPorPrecioMayorA(precioMinimo float64) ([]Producto, error)
}

// Servicio contiene la lógica de negocio.
type Servicio struct {
    repo Repositorio
}

// NuevoServicio crea un nuevo Servicio con la dependencia inyectada.
func NuevoServicio(repo Repositorio) *Servicio {
    return &Servicio{repo: repo}
}

// CrearProducto valida los datos y delega el guardado al repositorio.
func (s *Servicio) CrearProducto(nombre string, precio float64) (Producto, error) {
    if nombre == "" {
        return Producto{}, errors.New("el nombre no puede estar vacío")
    }
    if precio <= 0 {
        return Producto{}, errors.New("el precio debe ser mayor que cero")
    }
    return s.repo.Guardar(Producto{Nombre: nombre, Precio: precio})
}

// ObtenerProductosCaros devuelve productos con precio superior al mínimo.
func (s *Servicio) ObtenerProductosCaros(precioMinimo float64) ([]Producto, error) {
    return s.repo.BuscarPorPrecioMayorA(precioMinimo)
}
```

### 5.3 Pruebas Unitarias con el paquete `testing`

```go
// internal/producto/producto_test.go
package producto_test  // convención: sufijo _test para tests de caja negra

import (
    "errors"
    "testing"

    "mimodulo/internal/producto"
)

// ─── Mock del Repositorio ─────────────────────────────────────────────────────

// repositorioMock implementa la interfaz Repositorio para tests.
type repositorioMock struct {
    guardarFn               func(p producto.Producto) (producto.Producto, error)
    buscarPorIDFn           func(id int) (producto.Producto, error)
    buscarPorPrecioMayorAFn func(min float64) ([]producto.Producto, error)
    guardarLlamado          bool
}

func (m *repositorioMock) Guardar(p producto.Producto) (producto.Producto, error) {
    m.guardarLlamado = true
    if m.guardarFn != nil {
        return m.guardarFn(p)
    }
    return p, nil
}

func (m *repositorioMock) BuscarPorID(id int) (producto.Producto, error) {
    if m.buscarPorIDFn != nil {
        return m.buscarPorIDFn(id)
    }
    return producto.Producto{}, errors.New("no encontrado")
}

func (m *repositorioMock) BuscarPorPrecioMayorA(min float64) ([]producto.Producto, error) {
    if m.buscarPorPrecioMayorAFn != nil {
        return m.buscarPorPrecioMayorAFn(min)
    }
    return nil, nil
}

// ─── Tests de CrearProducto ───────────────────────────────────────────────────

func TestCrearProducto_DatosValidos(t *testing.T) {
    // Arrange
    mock := &repositorioMock{
        guardarFn: func(p producto.Producto) (producto.Producto, error) {
            p.ID = 1
            return p, nil
        },
    }
    svc := producto.NuevoServicio(mock)

    // Act
    resultado, err := svc.CrearProducto("Laptop", 1200.0)

    // Assert
    if err != nil {
        t.Fatalf("no se esperaba error, pero se obtuvo: %v", err)
    }
    if resultado.Nombre != "Laptop" {
        t.Errorf("nombre esperado 'Laptop', obtenido '%s'", resultado.Nombre)
    }
    if resultado.Precio != 1200.0 {
        t.Errorf("precio esperado 1200.0, obtenido %f", resultado.Precio)
    }
    if !mock.guardarLlamado {
        t.Error("se esperaba que Guardar fuera llamado")
    }
}

func TestCrearProducto_NombreVacio_DevuelveError(t *testing.T) {
    mock := &repositorioMock{}
    svc := producto.NuevoServicio(mock)

    _, err := svc.CrearProducto("", 100.0)

    if err == nil {
        t.Fatal("se esperaba un error pero no se obtuvo ninguno")
    }
    if mock.guardarLlamado {
        t.Error("Guardar no debería haberse llamado cuando el nombre es inválido")
    }
}

func TestCrearProducto_PrecioNegativo_DevuelveError(t *testing.T) {
    svc := producto.NuevoServicio(&repositorioMock{})

    _, err := svc.CrearProducto("Laptop", -50.0)

    if err == nil {
        t.Fatal("se esperaba un error por precio negativo")
    }
}
```

### 5.4 Tests con Subtests (`t.Run`)

Los subtests permiten agrupar casos relacionados y ejecutarlos individualmente:

```go
func TestCrearProducto(t *testing.T) {
    svc := producto.NuevoServicio(&repositorioMock{
        guardarFn: func(p producto.Producto) (producto.Producto, error) {
            p.ID = 42
            return p, nil
        },
    })

    t.Run("datos válidos devuelve producto guardado", func(t *testing.T) {
        p, err := svc.CrearProducto("Teclado", 45.99)

        if err != nil {
            t.Fatalf("error inesperado: %v", err)
        }
        if p.ID != 42 {
            t.Errorf("ID esperado 42, obtenido %d", p.ID)
        }
    })

    t.Run("nombre vacío devuelve error", func(t *testing.T) {
        _, err := svc.CrearProducto("", 45.99)
        if err == nil {
            t.Error("se esperaba error con nombre vacío")
        }
    })

    t.Run("precio cero devuelve error", func(t *testing.T) {
        _, err := svc.CrearProducto("Teclado", 0)
        if err == nil {
            t.Error("se esperaba error con precio cero")
        }
    })
}

// Ejecutar sólo un subtest:
// go test -run TestCrearProducto/nombre_vacío_devuelve_error
```

### 5.5 Tests Basados en Tabla (Table-Driven Tests)

Este patrón es idiomático en Go para probar múltiples casos con el mismo código:

```go
func TestCrearProducto_Validaciones(t *testing.T) {
    casos := []struct {
        nombre        string
        inputNombre   string
        inputPrecio   float64
        esperaError   bool
    }{
        {
            nombre:      "datos válidos",
            inputNombre: "Laptop",
            inputPrecio: 1200.0,
            esperaError: false,
        },
        {
            nombre:      "nombre vacío",
            inputNombre: "",
            inputPrecio: 100.0,
            esperaError: true,
        },
        {
            nombre:      "precio negativo",
            inputNombre: "Laptop",
            inputPrecio: -10.0,
            esperaError: true,
        },
        {
            nombre:      "precio cero",
            inputNombre: "Laptop",
            inputPrecio: 0,
            esperaError: true,
        },
    }

    for _, tc := range casos {
        tc := tc // captura para closures seguras (Go < 1.22)
        t.Run(tc.nombre, func(t *testing.T) {
            t.Parallel() // ejecutar subtests en paralelo
            svc := producto.NuevoServicio(&repositorioMock{})

            _, err := svc.CrearProducto(tc.inputNombre, tc.inputPrecio)

            if tc.esperaError && err == nil {
                t.Errorf("se esperaba error pero no se obtuvo ninguno")
            }
            if !tc.esperaError && err != nil {
                t.Errorf("no se esperaba error pero se obtuvo: %v", err)
            }
        })
    }
}
```

### 5.6 Testing con `testify` (librería popular)

`testify` proporciona aserciones más expresivas y un paquete de mocking:

```bash
go get github.com/stretchr/testify
```

```go
// internal/producto/producto_testify_test.go
package producto_test

import (
    "testing"

    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"
    "github.com/stretchr/testify/mock"

    "mimodulo/internal/producto"
)

// ─── Mock con testify/mock ────────────────────────────────────────────────────

type RepositorioMock struct {
    mock.Mock
}

func (m *RepositorioMock) Guardar(p producto.Producto) (producto.Producto, error) {
    args := m.Called(p)
    return args.Get(0).(producto.Producto), args.Error(1)
}

func (m *RepositorioMock) BuscarPorID(id int) (producto.Producto, error) {
    args := m.Called(id)
    return args.Get(0).(producto.Producto), args.Error(1)
}

func (m *RepositorioMock) BuscarPorPrecioMayorA(min float64) ([]producto.Producto, error) {
    args := m.Called(min)
    return args.Get(0).([]producto.Producto), args.Error(1)
}

// ─── Tests ────────────────────────────────────────────────────────────────────

func TestCrearProducto_ConTestify(t *testing.T) {
    // Arrange
    repoMock := new(RepositorioMock)
    productoEsperado := producto.Producto{ID: 1, Nombre: "Laptop", Precio: 1200.0}
    repoMock.On("Guardar", mock.AnythingOfType("producto.Producto")).
        Return(productoEsperado, nil)

    svc := producto.NuevoServicio(repoMock)

    // Act
    resultado, err := svc.CrearProducto("Laptop", 1200.0)

    // Assert
    require.NoError(t, err)                            // falla inmediatamente si hay error
    assert.Equal(t, productoEsperado.ID, resultado.ID)
    assert.Equal(t, "Laptop", resultado.Nombre)
    assert.Equal(t, 1200.0, resultado.Precio)
    repoMock.AssertExpectations(t)                     // verifica que todos los On() fueron llamados
}

func TestObtenerProductosCaros_ConTestify(t *testing.T) {
    repoMock := new(RepositorioMock)
    productosEsperados := []producto.Producto{
        {ID: 1, Nombre: "Laptop", Precio: 1200.0},
        {ID: 3, Nombre: "Monitor", Precio: 350.0},
    }
    repoMock.On("BuscarPorPrecioMayorA", 100.0).Return(productosEsperados, nil)

    svc := producto.NuevoServicio(repoMock)
    resultado, err := svc.ObtenerProductosCaros(100.0)

    require.NoError(t, err)
    assert.Len(t, resultado, 2)
    assert.Equal(t, "Laptop", resultado[0].Nombre)
    repoMock.AssertExpectations(t)
}
```

### 5.7 Tests de Handlers HTTP

```go
// internal/api/handler.go
package api

import (
    "encoding/json"
    "net/http"
    "strconv"

    "mimodulo/internal/producto"
)

type Handler struct {
    svc *producto.Servicio
}

func NuevoHandler(svc *producto.Servicio) *Handler {
    return &Handler{svc: svc}
}

func (h *Handler) CrearProducto(w http.ResponseWriter, r *http.Request) {
    var req struct {
        Nombre string  `json:"nombre"`
        Precio float64 `json:"precio"`
    }
    if err := json.NewDecoder(r.Body).Decode(&req); err != nil {
        http.Error(w, "petición inválida", http.StatusBadRequest)
        return
    }

    p, err := h.svc.CrearProducto(req.Nombre, req.Precio)
    if err != nil {
        http.Error(w, err.Error(), http.StatusBadRequest)
        return
    }

    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusCreated)
    json.NewEncoder(w).Encode(p)
}
```

```go
// internal/api/handler_test.go
package api_test

import (
    "bytes"
    "encoding/json"
    "net/http"
    "net/http/httptest"
    "testing"

    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"

    "mimodulo/internal/api"
    "mimodulo/internal/producto"
)

func TestCrearProductoHandler_DatosValidos(t *testing.T) {
    // Arrange: mock del servicio usando la interfaz
    repoMock := new(RepositorioMock) // reutilizamos el mock anterior
    productoCreado := producto.Producto{ID: 7, Nombre: "Auriculares", Precio: 89.99}
    repoMock.On("Guardar", mock.Anything).Return(productoCreado, nil)

    svc := producto.NuevoServicio(repoMock)
    handler := api.NuevoHandler(svc)

    body, _ := json.Marshal(map[string]interface{}{
        "nombre": "Auriculares",
        "precio": 89.99,
    })
    req := httptest.NewRequest(http.MethodPost, "/productos", bytes.NewReader(body))
    req.Header.Set("Content-Type", "application/json")
    w := httptest.NewRecorder()

    // Act
    handler.CrearProducto(w, req)

    // Assert
    resp := w.Result()
    assert.Equal(t, http.StatusCreated, resp.StatusCode)

    var respBody producto.Producto
    require.NoError(t, json.NewDecoder(resp.Body).Decode(&respBody))
    assert.Equal(t, 7, respBody.ID)
    assert.Equal(t, "Auriculares", respBody.Nombre)
}

func TestCrearProductoHandler_CuerpoInvalido_Devuelve400(t *testing.T) {
    svc := producto.NuevoServicio(&repositorioMock{})
    handler := api.NuevoHandler(svc)

    req := httptest.NewRequest(http.MethodPost, "/productos",
        bytes.NewBufferString("esto no es JSON"))
    w := httptest.NewRecorder()

    handler.CrearProducto(w, req)

    assert.Equal(t, http.StatusBadRequest, w.Code)
}
```

### 5.8 Tests de Integración con Base de Datos

```go
// internal/producto/repositorio_postgres_test.go
//go:build integration

package producto_test

import (
    "testing"
    "os"

    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"

    "mimodulo/internal/producto"
)

// Para ejecutar: go test -tags=integration ./...

func TestRepositorioPostgres_GuardarYBuscar(t *testing.T) {
    dsn := os.Getenv("TEST_DATABASE_URL")
    if dsn == "" {
        t.Skip("TEST_DATABASE_URL no configurada; omitiendo test de integración")
    }

    repo, err := producto.NuevoRepositorioPostgres(dsn)
    require.NoError(t, err)
    defer repo.Limpiar() // limpia datos de test

    // Guardar
    guardado, err := repo.Guardar(producto.Producto{Nombre: "Laptop", Precio: 1200.0})
    require.NoError(t, err)
    assert.Greater(t, guardado.ID, 0)

    // Buscar
    encontrado, err := repo.BuscarPorID(guardado.ID)
    require.NoError(t, err)
    assert.Equal(t, "Laptop", encontrado.Nombre)
}
```

### 5.9 Benchmarks en Go

```go
// internal/producto/benchmark_test.go
package producto_test

import (
    "fmt"
    "testing"
)

func BenchmarkCrearProducto(b *testing.B) {
    mock := &repositorioMock{
        guardarFn: func(p producto.Producto) (producto.Producto, error) {
            return p, nil
        },
    }
    svc := producto.NuevoServicio(mock)

    b.ResetTimer()
    for i := 0; i < b.N; i++ {
        svc.CrearProducto(fmt.Sprintf("Producto-%d", i), float64(i+1))
    }
}

// Ejecutar: go test -bench=. -benchmem ./...
```

### 5.10 Fuzz Testing en Go (desde Go 1.18)

```go
// internal/producto/fuzz_test.go
package producto_test

import "testing"

func FuzzCrearProducto(f *testing.F) {
    // Corpus inicial: casos conocidos
    f.Add("Laptop", 1200.0)
    f.Add("", 0.0)
    f.Add("X", -1.0)

    f.Fuzz(func(t *testing.T, nombre string, precio float64) {
        svc := producto.NuevoServicio(&repositorioMock{})

        // El test no debe hacer panic en ningún caso
        _, _ = svc.CrearProducto(nombre, precio)
    })
}

// Ejecutar: go test -fuzz=FuzzCrearProducto -fuzztime=30s ./...
```

---

## 6. Cobertura de Código

La cobertura indica qué porcentaje del código está cubierto por los tests. No es un objetivo en sí misma, pero es un indicador útil.

### Spring Boot

```bash
# Con Maven + JaCoCo (añadir plugin en pom.xml)
mvn test jacoco:report
# Reporte en: target/site/jacoco/index.html
```

```xml
<!-- pom.xml -->
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.11</version>
    <executions>
        <execution>
            <goals><goal>prepare-agent</goal></goals>
        </execution>
        <execution>
            <id>report</id>
            <phase>test</phase>
            <goals><goal>report</goal></goals>
        </execution>
    </executions>
</plugin>
```

### Python

```bash
pytest --cov=src --cov-report=term-missing --cov-report=html
# Reporte HTML en: htmlcov/index.html
```

### Go

```bash
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out -o coverage.html
# Ver cobertura por función
go tool cover -func=coverage.out
```

---

## 7. Testing en CI/CD

### GitHub Actions (ejemplo multi-lenguaje)

```yaml
# .github/workflows/tests.yml
name: Tests

on:
  push:
    branches: [main, develop]
  pull_request:

jobs:
  test-java:
    name: Tests Spring Boot
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
      - name: Ejecutar tests con Maven
        run: mvn -B test
      - name: Subir reporte de cobertura
        uses: actions/upload-artifact@v4
        with:
          name: jacoco-report
          path: target/site/jacoco/

  test-python:
    name: Tests Python
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - name: Instalar dependencias
        run: pip install -r requirements-test.txt
      - name: Ejecutar pytest con cobertura
        run: pytest --cov=src --cov-report=xml
      - name: Subir cobertura a Codecov
        uses: codecov/codecov-action@v4

  test-go:
    name: Tests Go
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with:
          go-version: '1.23'
      - name: Ejecutar tests
        run: go test -v -race -coverprofile=coverage.out ./...
      - name: Mostrar cobertura
        run: go tool cover -func=coverage.out
```

---

## 8. Buenas Prácticas

### ✅ Lo que debes hacer

1. **Nombra los tests descriptivamente**: el nombre debe explicar qué se prueba y qué se espera.
   - ✅ `test_crear_producto_precio_negativo_lanza_excepcion`
   - ❌ `test1`

2. **Un test, una aserción principal**: cada test verifica una sola cosa para facilitar el diagnóstico.

3. **Tests independientes**: los tests no deben depender del orden de ejecución ni compartir estado mutable.

4. **Usa el patrón AAA** (Arrange, Act, Assert) para que los tests sean legibles.

5. **Testea el comportamiento, no la implementación**: el test no debe romperse si refactorizas el interior sin cambiar el contrato.

6. **Los tests deben ser rápidos**: los tests unitarios deben correr en milisegundos. Mueve las pruebas lentas a la suite de integración.

7. **Mantén los tests junto al código**: facilita el mantenimiento y la legibilidad.

8. **Ejecuta tests en el pipeline de CI**: ningún PR debe mergearse sin pasar los tests.

### ❌ Anti-patrones a evitar

| Anti-patrón | Descripción | Solución |
|-------------|-------------|----------|
| **Test frágil** | El test falla con cualquier refactorización | Testea la interfaz pública, no los detalles internos |
| **Test lento** | Un test tarda segundos | Usa mocks para dependencias externas |
| **Test que no falla** | Siempre pasa aunque el código esté roto | Verifica que el test realmente falla antes de arreglarlo (TDD) |
| **Fixture gigante** | `setUp` enorme compartido entre tests no relacionados | Factoriza fixtures específicos para cada test |
| **Orden de ejecución** | Los tests dependen del orden en que se ejecutan | Usa `@BeforeEach` / fixtures por test |
| **Número mágico** | Valores sin explicación en las aserciones | Usa constantes con nombre significativo |
| **Mockear todo** | Se mockea incluso el SUT | Solo mockea las dependencias externas, nunca el SUT |

### TDD (Test-Driven Development)

TDD es una práctica en la que escribes el test **antes** que el código:

```
1. 🔴 RED:    Escribe un test que falla
2. 🟢 GREEN:  Escribe el código mínimo para que pase
3. 🔵 REFACTOR: Mejora el código sin romper el test
```

Beneficios:
- Diseño guiado por los tests (código más desacoplado)
- Cobertura del 100% de lo que implementas
- Feedback inmediato

---

## 9. Recursos Adicionales

### Documentación Oficial

- **JUnit 5**: [https://junit.org/junit5/docs/current/user-guide/](https://junit.org/junit5/docs/current/user-guide/)
- **Mockito**: [https://javadoc.io/doc/org.mockito/mockito-core/latest/](https://javadoc.io/doc/org.mockito/mockito-core/latest/)
- **Spring Boot Testing**: [https://docs.spring.io/spring-boot/reference/testing/](https://docs.spring.io/spring-boot/reference/testing/)
- **pytest**: [https://docs.pytest.org/en/stable/](https://docs.pytest.org/en/stable/)
- **unittest**: [https://docs.python.org/3/library/unittest.html](https://docs.python.org/3/library/unittest.html)
- **Go testing**: [https://pkg.go.dev/testing](https://pkg.go.dev/testing)
- **testify**: [https://github.com/stretchr/testify](https://github.com/stretchr/testify)

### Libros Recomendados

- *Test-Driven Development* – Kent Beck
- *Growing Object-Oriented Software, Guided by Tests* – Freeman & Pryce
- *The Art of Unit Testing* – Roy Osherove
- *Clean Code* – Robert C. Martin (capítulos sobre tests)

### Cursos y Recursos Online

- [Testing Spring Boot Applications](https://www.baeldung.com/spring-boot-testing)
- [Real Python – Testing Your Code](https://realpython.com/python-testing/)
- [Go Testing Guide](https://go.dev/doc/tutorial/add-a-test)
- [Test Doubles – Martin Fowler](https://martinfowler.com/bliki/TestDouble.html)

---

> 💡 **Recuerda**: Un buen conjunto de tests es un activo tan valioso como el propio código de producción. Invertir tiempo en escribir buenos tests es invertir en la calidad y la sostenibilidad del proyecto.
