# 📘 Swagger y OpenAPI - Tutorial Completo

## Introducción

**Swagger** (actualmente conocido como **OpenAPI**) es el estándar más utilizado en la industria para documentar, diseñar y consumir APIs REST. Permite describir la estructura completa de una API de forma legible tanto por humanos como por máquinas.

Con Swagger/OpenAPI puedes:
- **Documentar** tu API de forma interactiva y actualizada
- **Diseñar** la API antes de implementarla (_design-first_)
- **Generar** código cliente y servidor automáticamente
- **Probar** endpoints directamente desde el navegador
- **Validar** contratos entre equipos de frontend y backend

Este tutorial te llevará desde los conceptos más básicos hasta la integración avanzada con Spring Boot, con ejemplos reales y ejercicios prácticos.

---

## 📋 Tabla de Contenidos

1. [Conceptos Fundamentales](#1-conceptos-fundamentales)
2. [Estructura de un Documento OpenAPI](#2-estructura-de-un-documento-openapi)
3. [Tu Primer Documento Swagger](#3-tu-primer-documento-swagger)
4. [Paths y Operaciones HTTP](#4-paths-y-operaciones-http)
5. [Parámetros y Request Bodies](#5-parámetros-y-request-bodies)
6. [Schemas y Tipos de Datos](#6-schemas-y-tipos-de-datos)
7. [Respuestas y Códigos de Estado](#7-respuestas-y-códigos-de-estado)
8. [Seguridad y Autenticación](#8-seguridad-y-autenticación)
9. [Reutilización con Components](#9-reutilización-con-components)
10. [Integración con Spring Boot](#10-integración-con-spring-boot)
11. [Swagger UI y Swagger Editor](#11-swagger-ui-y-swagger-editor)
12. [Generación de Código con OpenAPI Generator](#12-generación-de-código-con-openapi-generator)
13. [OpenAPI Avanzado](#13-openapi-avanzado)
14. [Proyecto Completo: API de Gestión de Tareas](#14-proyecto-completo-api-de-gestión-de-tareas)
15. [Buenas Prácticas](#15-buenas-prácticas)
16. [Recursos Adicionales](#16-recursos-adicionales)

---

## 1. Conceptos Fundamentales

### ¿Qué es OpenAPI?

**OpenAPI Specification (OAS)** es un estándar de descripción de APIs REST, mantenido por la [OpenAPI Initiative](https://www.openapis.org/), una organización bajo la Linux Foundation.

- **Swagger** era el nombre original del proyecto creado por SmartBear Software
- En 2016 fue donado a la OpenAPI Initiative y renombrado a **OpenAPI**
- Actualmente **Swagger** se refiere a las herramientas (Swagger UI, Swagger Editor, Swagger Codegen), mientras que **OpenAPI** es la especificación

### Versiones

| Versión | Año | Características clave |
|---------|-----|-----------------------|
| **Swagger 1.x** | 2011 | Primera versión, JSON únicamente |
| **Swagger 2.0** | 2014 | YAML soportado, `definitions`, `paths` |
| **OpenAPI 3.0** | 2017 | `components`, `requestBody`, servidores múltiples |
| **OpenAPI 3.1** | 2021 | Compatibilidad total con JSON Schema, webhooks |

> 💡 **Recomendación**: Usa **OpenAPI 3.0** o superior para proyectos nuevos. OpenAPI 3.1 es la más reciente y completa.

### ¿Por qué usar Swagger/OpenAPI?

```
Sin Swagger:                    Con Swagger:
┌──────────────────────────┐   ┌──────────────────────────────────────┐
│ PDF/Word desactualizado  │   │ Documentación viva y ejecutable       │
│ Reuniones para alinear   │   │ Contrato claro entre equipos          │
│ Código cliente manual    │   │ Generación automática de clientes SDK │
│ Testing manual de APIs   │   │ Testing interactivo en Swagger UI     │
│ Inconsistencias frecuent.│   │ Validación automática de contratos    │
└──────────────────────────┘   └──────────────────────────────────────┘
```

### Herramientas del Ecosistema Swagger

| Herramienta | Descripción |
|-------------|-------------|
| **Swagger UI** | Interfaz web interactiva para explorar y probar APIs |
| **Swagger Editor** | Editor online para escribir especificaciones OpenAPI |
| **Swagger Codegen** | Generador de código cliente/servidor a partir de specs |
| **OpenAPI Generator** | Fork mejorado de Swagger Codegen (recomendado) |
| **Springdoc-openapi** | Integración automática con Spring Boot |
| **Springfox** | Antigua librería Spring (usar springdoc en su lugar) |

---

## 2. Estructura de un Documento OpenAPI

Un documento OpenAPI puede escribirse en **YAML** o **JSON**. YAML es más legible y recomendado.

### Estructura General (OpenAPI 3.0)

```yaml
openapi: 3.0.3              # Versión de la especificación

info:                       # Metadatos de la API
  title: Mi API
  version: 1.0.0

servers:                    # Servidores donde está desplegada la API
  - url: https://api.ejemplo.com/v1

paths:                      # Endpoints de la API
  /recurso:
    get:
      summary: Obtener recursos

components:                 # Componentes reutilizables
  schemas:
    MiModelo:
      type: object

security:                   # Seguridad global
  - bearerAuth: []

tags:                       # Agrupación de endpoints
  - name: recursos
```

### El Objeto `info`

```yaml
info:
  title: API de Gestión de Productos
  description: |
    API REST para gestionar el catálogo de productos.
    
    ## Características
    - CRUD completo de productos
    - Filtrado y paginación
    - Autenticación JWT
  version: 2.1.0
  contact:
    name: Equipo de Desarrollo
    email: dev@empresa.com
    url: https://empresa.com/soporte
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT
  termsOfService: https://empresa.com/terminos
```

### El Objeto `servers`

```yaml
servers:
  - url: https://api.empresa.com/v1
    description: Servidor de producción
  - url: https://staging-api.empresa.com/v1
    description: Servidor de staging
  - url: http://localhost:8080/api/v1
    description: Desarrollo local
  - url: https://{entorno}.api.empresa.com/v1
    description: URL dinámica por entorno
    variables:
      entorno:
        default: produccion
        enum:
          - produccion
          - staging
          - desarrollo
        description: Entorno de despliegue
```

---

## 3. Tu Primer Documento Swagger

Vamos a crear paso a paso la especificación de una API sencilla: una **API de libros**.

### Paso 1: Cabecera y metadatos

```yaml
openapi: 3.0.3

info:
  title: API de Libros
  description: API REST para gestionar un catálogo de libros
  version: 1.0.0

servers:
  - url: http://localhost:8080/api/v1
    description: Servidor de desarrollo
```

### Paso 2: Definir el modelo de datos

```yaml
components:
  schemas:
    Libro:
      type: object
      required:
        - titulo
        - autor
        - isbn
      properties:
        id:
          type: integer
          format: int64
          example: 1
          readOnly: true
        titulo:
          type: string
          example: "El Quijote"
          minLength: 1
          maxLength: 200
        autor:
          type: string
          example: "Miguel de Cervantes"
        isbn:
          type: string
          pattern: "^[0-9]{13}$"
          example: "9788491051145"
        precio:
          type: number
          format: double
          minimum: 0
          example: 19.99
        disponible:
          type: boolean
          default: true
```

### Paso 3: Definir los endpoints

```yaml
paths:
  /libros:
    get:
      summary: Listar todos los libros
      operationId: listarLibros
      tags:
        - Libros
      parameters:
        - name: disponible
          in: query
          description: Filtrar por disponibilidad
          required: false
          schema:
            type: boolean
      responses:
        '200':
          description: Lista de libros obtenida correctamente
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Libro'
    
    post:
      summary: Crear un nuevo libro
      operationId: crearLibro
      tags:
        - Libros
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Libro'
      responses:
        '201':
          description: Libro creado correctamente
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Libro'
        '400':
          description: Datos de entrada inválidos

  /libros/{id}:
    get:
      summary: Obtener un libro por ID
      operationId: obtenerLibro
      tags:
        - Libros
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
            format: int64
      responses:
        '200':
          description: Libro encontrado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Libro'
        '404':
          description: Libro no encontrado
    
    put:
      summary: Actualizar un libro
      operationId: actualizarLibro
      tags:
        - Libros
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
            format: int64
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Libro'
      responses:
        '200':
          description: Libro actualizado correctamente
        '404':
          description: Libro no encontrado
    
    delete:
      summary: Eliminar un libro
      operationId: eliminarLibro
      tags:
        - Libros
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: integer
            format: int64
      responses:
        '204':
          description: Libro eliminado correctamente
        '404':
          description: Libro no encontrado
```

### Documento completo

```yaml
openapi: 3.0.3

info:
  title: API de Libros
  description: API REST para gestionar un catálogo de libros
  version: 1.0.0

servers:
  - url: http://localhost:8080/api/v1

tags:
  - name: Libros
    description: Operaciones sobre libros

paths:
  /libros:
    get:
      summary: Listar todos los libros
      operationId: listarLibros
      tags: [Libros]
      responses:
        '200':
          description: Lista de libros
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Libro'
    post:
      summary: Crear un libro
      operationId: crearLibro
      tags: [Libros]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Libro'
      responses:
        '201':
          description: Libro creado

  /libros/{id}:
    parameters:
      - name: id
        in: path
        required: true
        schema:
          type: integer
    get:
      summary: Obtener libro por ID
      operationId: obtenerLibro
      tags: [Libros]
      responses:
        '200':
          description: Libro encontrado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Libro'
        '404':
          description: No encontrado
    put:
      summary: Actualizar libro
      operationId: actualizarLibro
      tags: [Libros]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Libro'
      responses:
        '200':
          description: Actualizado
    delete:
      summary: Eliminar libro
      operationId: eliminarLibro
      tags: [Libros]
      responses:
        '204':
          description: Eliminado

components:
  schemas:
    Libro:
      type: object
      required: [titulo, autor, isbn]
      properties:
        id:
          type: integer
          readOnly: true
        titulo:
          type: string
        autor:
          type: string
        isbn:
          type: string
        precio:
          type: number
        disponible:
          type: boolean
          default: true
```

---

## 4. Paths y Operaciones HTTP

### Métodos HTTP en OpenAPI

```yaml
paths:
  /recursos:
    get:      # Obtener lista o recurso
    post:     # Crear nuevo recurso
    put:      # Reemplazar recurso completo
    patch:    # Actualizar parcialmente
    delete:   # Eliminar recurso
    head:     # Como GET pero sin cuerpo
    options:  # Obtener métodos permitidos
```

### Estructura de una Operación

```yaml
paths:
  /productos:
    post:
      # Identificador único (útil para generación de código)
      operationId: crearProducto
      
      # Resumen corto (aparece en Swagger UI)
      summary: Crear un nuevo producto
      
      # Descripción larga (soporta Markdown)
      description: |
        Crea un nuevo producto en el catálogo.
        
        - El campo `sku` debe ser único
        - El `precio` debe ser mayor que 0
        - Se puede incluir hasta 10 imágenes
      
      # Agrupar en Swagger UI
      tags:
        - Productos
      
      # Parámetros
      parameters:
        - name: X-Request-ID
          in: header
          schema:
            type: string
      
      # Cuerpo de la petición
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ProductoInput'
      
      # Respuestas posibles
      responses:
        '201':
          description: Producto creado exitosamente
      
      # Deprecar un endpoint
      deprecated: false
      
      # Seguridad específica del endpoint
      security:
        - bearerAuth: []
```

### Parámetros de Ruta Compartidos

Puedes definir parámetros comunes a nivel de path para evitar repetición:

```yaml
paths:
  /usuarios/{usuarioId}/pedidos/{pedidoId}:
    # Parámetros compartidos por todas las operaciones del path
    parameters:
      - name: usuarioId
        in: path
        required: true
        schema:
          type: integer
      - name: pedidoId
        in: path
        required: true
        schema:
          type: integer
    
    get:
      summary: Obtener pedido específico de usuario
      # Los parámetros del path se heredan automáticamente
      responses:
        '200':
          description: OK
    
    delete:
      summary: Cancelar pedido
      responses:
        '204':
          description: Cancelado
```

---

## 5. Parámetros y Request Bodies

### Tipos de Parámetros

```yaml
parameters:
  # En la ruta URL
  - name: id
    in: path
    required: true           # Siempre true para path params
    description: ID del recurso
    schema:
      type: integer
      format: int64

  # En la query string (?clave=valor)
  - name: pagina
    in: query
    required: false
    schema:
      type: integer
      minimum: 1
      default: 1
  
  - name: tamanio
    in: query
    schema:
      type: integer
      minimum: 1
      maximum: 100
      default: 20

  # En las cabeceras HTTP
  - name: X-Api-Key
    in: header
    required: true
    schema:
      type: string

  # En las cookies
  - name: session_token
    in: cookie
    schema:
      type: string
```

### Parámetros Avanzados

```yaml
parameters:
  # Arrays en query string
  - name: etiquetas
    in: query
    description: Filtrar por etiquetas (se pueden pasar varias)
    schema:
      type: array
      items:
        type: string
    style: form          # ?etiquetas=a&etiquetas=b  (default)
    explode: true

  # Objeto como parámetro
  - name: filtro
    in: query
    schema:
      type: object
      properties:
        estado:
          type: string
        categoria:
          type: string
    style: deepObject    # ?filtro[estado]=activo&filtro[categoria]=ropa
    explode: true

  # Con ejemplos múltiples
  - name: formato
    in: query
    schema:
      type: string
      enum: [json, xml, csv]
    examples:
      json:
        value: json
        summary: Formato JSON
      xml:
        value: xml
        summary: Formato XML
```

### Request Body

```yaml
requestBody:
  description: Datos del producto a crear
  required: true
  content:
    # JSON
    application/json:
      schema:
        $ref: '#/components/schemas/ProductoInput'
      examples:
        electronico:
          summary: Producto electrónico
          value:
            nombre: "Laptop Pro 15"
            precio: 999.99
            categoria: "electronica"
        ropa:
          summary: Producto de ropa
          value:
            nombre: "Camiseta básica"
            precio: 19.99
            categoria: "ropa"
    
    # Formulario HTML
    application/x-www-form-urlencoded:
      schema:
        type: object
        properties:
          nombre:
            type: string
          precio:
            type: number
    
    # Subida de archivos
    multipart/form-data:
      schema:
        type: object
        properties:
          nombre:
            type: string
          imagen:
            type: string
            format: binary
          documentos:
            type: array
            items:
              type: string
              format: binary
    
    # Archivo binario directamente
    image/png:
      schema:
        type: string
        format: binary
```

---

## 6. Schemas y Tipos de Datos

### Tipos Primitivos

```yaml
schemas:
  Ejemplos:
    type: object
    properties:
      # Enteros
      edad:
        type: integer           # int32 por defecto
      id:
        type: integer
        format: int64           # Long en Java, BigInt en JS
      
      # Decimales
      precio:
        type: number            # float por defecto
      saldo:
        type: number
        format: double          # Doble precisión
      
      # Texto
      nombre:
        type: string
      email:
        type: string
        format: email
      web:
        type: string
        format: uri
      password:
        type: string
        format: password        # Oculto en Swagger UI
      uuid:
        type: string
        format: uuid
      
      # Fechas
      fechaNacimiento:
        type: string
        format: date            # 2024-01-15
      creadoEn:
        type: string
        format: date-time       # 2024-01-15T10:30:00Z
      
      # Booleanos
      activo:
        type: boolean
      
      # Binario
      avatar:
        type: string
        format: byte            # Base64 encoded
      archivo:
        type: string
        format: binary          # Octets stream
```

### Validaciones

```yaml
schemas:
  Usuario:
    type: object
    properties:
      nombre:
        type: string
        minLength: 2
        maxLength: 50
        pattern: "^[a-zA-ZáéíóúÁÉÍÓÚñÑ ]+$"   # Solo letras
      
      edad:
        type: integer
        minimum: 0
        maximum: 120
        exclusiveMinimum: true   # > 0, no >= 0 (OAS 3.0)
      
      nota:
        type: number
        minimum: 0.0
        maximum: 10.0
      
      email:
        type: string
        format: email
      
      rol:
        type: string
        enum:
          - admin
          - usuario
          - moderador
      
      etiquetas:
        type: array
        items:
          type: string
        minItems: 1
        maxItems: 10
        uniqueItems: true        # Sin duplicados
      
      metadata:
        type: object
        minProperties: 1
        maxProperties: 5
        additionalProperties:   # Propiedades extra permitidas
          type: string
```

### Herencia y Composición

#### `allOf` - Herencia / Combinar schemas

```yaml
schemas:
  Animal:
    type: object
    properties:
      nombre:
        type: string
      especie:
        type: string
  
  Mascota:
    allOf:
      - $ref: '#/components/schemas/Animal'
      - type: object
        properties:
          duenio:
            type: string
          vacunado:
            type: boolean
```

#### `oneOf` - Exactamente uno de varios schemas

```yaml
schemas:
  Pago:
    oneOf:
      - $ref: '#/components/schemas/PagoTarjeta'
      - $ref: '#/components/schemas/PagoTransferencia'
      - $ref: '#/components/schemas/PagoBizum'
    discriminator:
      propertyName: tipo    # Campo que indica qué schema usar
      mapping:
        tarjeta: '#/components/schemas/PagoTarjeta'
        transferencia: '#/components/schemas/PagoTransferencia'
        bizum: '#/components/schemas/PagoBizum'
  
  PagoTarjeta:
    type: object
    required: [tipo, numeroTarjeta, cvv]
    properties:
      tipo:
        type: string
        enum: [tarjeta]
      numeroTarjeta:
        type: string
      cvv:
        type: string
  
  PagoTransferencia:
    type: object
    required: [tipo, iban]
    properties:
      tipo:
        type: string
        enum: [transferencia]
      iban:
        type: string
```

#### `anyOf` - Uno o más schemas

```yaml
schemas:
  Contacto:
    anyOf:
      - required: [email]
        properties:
          email:
            type: string
            format: email
      - required: [telefono]
        properties:
          telefono:
            type: string
```

#### `not` - Negación

```yaml
schemas:
  ValorNoNulo:
    not:
      type: 'null'
```

### Arrays y Objetos Complejos

```yaml
schemas:
  Pedido:
    type: object
    properties:
      lineas:
        type: array
        items:
          $ref: '#/components/schemas/LineaPedido'
      
      metadatos:
        type: object
        additionalProperties:    # Mapa de string a string
          type: string
        example:
          origen: "web"
          campania: "black-friday"
      
      # Tuple (array con tipos específicos por posición)
      coordenadas:
        type: array
        prefixItems:
          - type: number         # latitud
          - type: number         # longitud
        maxItems: 2
        minItems: 2
```

---

## 7. Respuestas y Códigos de Estado

### Códigos de Estado Más Comunes

```yaml
responses:
  '200':
    description: OK - Operación exitosa (GET, PUT, PATCH)
  '201':
    description: Created - Recurso creado (POST)
  '202':
    description: Accepted - Procesamiento asíncrono aceptado
  '204':
    description: No Content - Éxito sin cuerpo de respuesta (DELETE)
  '400':
    description: Bad Request - Datos de entrada inválidos
  '401':
    description: Unauthorized - No autenticado
  '403':
    description: Forbidden - Autenticado pero sin permisos
  '404':
    description: Not Found - Recurso no encontrado
  '409':
    description: Conflict - Conflicto (ej. recurso ya existe)
  '422':
    description: Unprocessable Entity - Error de validación semántica
  '429':
    description: Too Many Requests - Rate limit excedido
  '500':
    description: Internal Server Error - Error del servidor
  '503':
    description: Service Unavailable - Servicio no disponible
```

### Estructura Detallada de Respuestas

```yaml
paths:
  /productos/{id}:
    get:
      responses:
        '200':
          description: Producto obtenido correctamente
          headers:
            X-Request-ID:
              description: ID único de la petición
              schema:
                type: string
                format: uuid
            Cache-Control:
              schema:
                type: string
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Producto'
              examples:
                electronico:
                  $ref: '#/components/examples/ProductoElectronico'
            application/xml:
              schema:
                $ref: '#/components/schemas/Producto'
        
        '404':
          description: Producto no encontrado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
              example:
                codigo: "PRODUCTO_NO_ENCONTRADO"
                mensaje: "No existe ningún producto con el ID proporcionado"
                timestamp: "2024-01-15T10:30:00Z"
```

### Definir Respuestas Reutilizables

```yaml
components:
  responses:
    NoAutenticado:
      description: El usuario no está autenticado
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          example:
            codigo: "NO_AUTENTICADO"
            mensaje: "Token de autenticación requerido"
    
    NoAutorizado:
      description: Sin permisos para esta operación
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
    
    ErrorServidor:
      description: Error interno del servidor
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

# Uso en endpoints:
paths:
  /admin/usuarios:
    get:
      responses:
        '200':
          description: Lista de usuarios
        '401':
          $ref: '#/components/responses/NoAutenticado'
        '403':
          $ref: '#/components/responses/NoAutorizado'
        '500':
          $ref: '#/components/responses/ErrorServidor'
```

### Schema de Error Estandarizado

```yaml
components:
  schemas:
    Error:
      type: object
      required: [codigo, mensaje]
      properties:
        codigo:
          type: string
          description: Código de error único
          example: "VALIDACION_FALLIDA"
        mensaje:
          type: string
          description: Descripción legible del error
          example: "El campo email no tiene un formato válido"
        detalles:
          type: array
          items:
            type: object
            properties:
              campo:
                type: string
              error:
                type: string
          example:
            - campo: "email"
              error: "Formato inválido"
            - campo: "edad"
              error: "Debe ser mayor de 18"
        timestamp:
          type: string
          format: date-time
        path:
          type: string
          example: "/api/v1/usuarios"
    
    RespuestaPaginada:
      type: object
      properties:
        contenido:
          type: array
          items: {}            # Se especifica con allOf o en el endpoint
        pagina:
          type: integer
        tamanio:
          type: integer
        totalElementos:
          type: integer
          format: int64
        totalPaginas:
          type: integer
        ultimo:
          type: boolean
        primero:
          type: boolean
```

---

## 8. Seguridad y Autenticación

### Tipos de Seguridad en OpenAPI

OpenAPI soporta varios esquemas de seguridad:

```yaml
components:
  securitySchemes:
    
    # API Key en header
    ApiKeyHeader:
      type: apiKey
      in: header
      name: X-API-Key
    
    # API Key en query string
    ApiKeyQuery:
      type: apiKey
      in: query
      name: api_key
    
    # API Key en cookie
    ApiKeyCookie:
      type: apiKey
      in: cookie
      name: api_session
    
    # HTTP Basic Auth
    BasicAuth:
      type: http
      scheme: basic
    
    # JWT Bearer Token
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT      # Solo informativo
    
    # OAuth2
    OAuth2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://auth.empresa.com/oauth/authorize
          tokenUrl: https://auth.empresa.com/oauth/token
          scopes:
            read:productos: Leer productos
            write:productos: Crear y editar productos
            admin: Acceso completo
        
        clientCredentials:
          tokenUrl: https://auth.empresa.com/oauth/token
          scopes:
            api:read: Acceso de lectura
            api:write: Acceso de escritura
        
        implicit:
          authorizationUrl: https://auth.empresa.com/oauth/authorize
          scopes:
            read: Acceso de lectura
        
        password:
          tokenUrl: https://auth.empresa.com/oauth/token
          scopes:
            read: Leer
            write: Escribir
    
    # OpenID Connect
    OpenID:
      type: openIdConnect
      openIdConnectUrl: https://auth.empresa.com/.well-known/openid-configuration
```

### Aplicar Seguridad Globalmente

```yaml
# Aplica a todos los endpoints por defecto
security:
  - BearerAuth: []

paths:
  /productos:
    get:
      summary: Listar productos (requiere autenticación)
      # Hereda la seguridad global
      responses:
        '200':
          description: OK
    
  /productos/publicos:
    get:
      summary: Listar productos públicos (sin autenticación)
      security: []           # Sobreescribe la seguridad global: sin auth
      responses:
        '200':
          description: OK

  /admin/productos:
    post:
      summary: Crear producto (requiere rol admin)
      security:
        - OAuth2:            # Sobreescribe con OAuth2
            - write:productos
            - admin
      responses:
        '201':
          description: Creado
```

### Seguridad Combinada (AND / OR)

```yaml
# OR: acepta cualquiera de los esquemas
security:
  - BearerAuth: []           # Esquema 1
  - ApiKeyHeader: []         # Esquema 2

# AND: requiere ambos esquemas simultáneamente
security:
  - BearerAuth: []
    ApiKeyHeader: []         # Mismo elemento del array = AND
```

---

## 9. Reutilización con Components

La sección `components` es el almacén de elementos reutilizables:

```yaml
components:
  schemas:          # Modelos de datos
  responses:        # Respuestas HTTP reutilizables
  parameters:       # Parámetros reutilizables
  examples:         # Ejemplos reutilizables
  requestBodies:    # Cuerpos de petición reutilizables
  headers:          # Cabeceras HTTP reutilizables
  securitySchemes:  # Esquemas de seguridad
  links:            # Links entre operaciones
  callbacks:        # Webhooks y callbacks
```

### Parámetros Reutilizables

```yaml
components:
  parameters:
    PaginaParam:
      name: pagina
      in: query
      description: Número de página (empieza en 1)
      required: false
      schema:
        type: integer
        minimum: 1
        default: 1
    
    TamanioParam:
      name: tamanio
      in: query
      description: Elementos por página
      required: false
      schema:
        type: integer
        minimum: 1
        maximum: 100
        default: 20
    
    IdPath:
      name: id
      in: path
      required: true
      schema:
        type: integer
        format: int64

# Uso:
paths:
  /productos:
    get:
      parameters:
        - $ref: '#/components/parameters/PaginaParam'
        - $ref: '#/components/parameters/TamanioParam'
  
  /productos/{id}:
    get:
      parameters:
        - $ref: '#/components/parameters/IdPath'
```

### Examples Reutilizables

```yaml
components:
  examples:
    ProductoEjemplo:
      summary: Ejemplo de producto electrónico
      value:
        id: 1
        nombre: "Laptop Pro 15"
        precio: 999.99
        categoria: "electronica"
        stock: 50
    
    ErrorValidacion:
      summary: Error de validación típico
      value:
        codigo: "VALIDACION_FALLIDA"
        mensaje: "Los datos proporcionados no son válidos"
        detalles:
          - campo: "precio"
            error: "Debe ser mayor que 0"
```

### Request Bodies Reutilizables

```yaml
components:
  requestBodies:
    CrearProducto:
      description: Datos necesarios para crear un producto
      required: true
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ProductoInput'
          examples:
            basico:
              $ref: '#/components/examples/ProductoEjemplo'

# Uso:
paths:
  /productos:
    post:
      requestBody:
        $ref: '#/components/requestBodies/CrearProducto'
```

### Links (Relaciones entre Operaciones)

```yaml
paths:
  /pedidos:
    post:
      operationId: crearPedido
      responses:
        '201':
          description: Pedido creado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Pedido'
          links:
            ObtenerPedido:
              operationId: obtenerPedido
              parameters:
                pedidoId: '$response.body#/id'
              description: Enlace para obtener el pedido recién creado

  /pedidos/{pedidoId}:
    get:
      operationId: obtenerPedido
      parameters:
        - name: pedidoId
          in: path
          required: true
          schema:
            type: integer
```

---

## 10. Integración con Spring Boot

### Dependencias Maven

```xml
<!-- pom.xml -->
<dependencies>
    <!-- Spring Boot Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- Springdoc OpenAPI (Swagger UI incluido) -->
    <dependency>
        <groupId>org.springdoc</groupId>
        <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
        <version>2.3.0</version>
    </dependency>
    
    <!-- Lombok (opcional, para reducir boilerplate) -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

### Dependencias Gradle

```groovy
// build.gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.3.0'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
}
```

### Configuración Básica

```java
// src/main/java/com/ejemplo/config/OpenApiConfig.java
package com.ejemplo.config;

import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Contact;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.License;
import io.swagger.v3.oas.models.servers.Server;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.List;

@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
            .info(new Info()
                .title("API de Gestión de Productos")
                .description("API REST completa para gestionar el catálogo de productos")
                .version("v1.0.0")
                .contact(new Contact()
                    .name("Equipo Backend")
                    .email("backend@empresa.com")
                    .url("https://empresa.com"))
                .license(new License()
                    .name("MIT")
                    .url("https://opensource.org/licenses/MIT")))
            .servers(List.of(
                new Server().url("http://localhost:8080").description("Desarrollo"),
                new Server().url("https://api.empresa.com").description("Producción")
            ));
    }
}
```

### Configuración en `application.properties`

```properties
# URL de Swagger UI: http://localhost:8080/swagger-ui.html
springdoc.swagger-ui.path=/swagger-ui.html

# URL del JSON/YAML de la spec: http://localhost:8080/v3/api-docs
springdoc.api-docs.path=/v3/api-docs

# Mostrar la URL del YAML: http://localhost:8080/v3/api-docs.yaml
springdoc.api-docs.enabled=true

# Ordenar endpoints alfabéticamente
springdoc.swagger-ui.operationsSorter=alpha

# Expandir los tags por defecto
springdoc.swagger-ui.tagsSorter=alpha

# Deshabilitar en producción
springdoc.swagger-ui.enabled=true
springdoc.api-docs.enabled=true
```

### Modelo / DTO con Anotaciones

```java
// src/main/java/com/ejemplo/dto/ProductoDTO.java
package com.ejemplo.dto;

import io.swagger.v3.oas.annotations.media.Schema;
import jakarta.validation.constraints.*;
import lombok.Data;

@Data
@Schema(description = "Datos de un producto del catálogo")
public class ProductoDTO {

    @Schema(description = "Identificador único del producto", example = "1", accessMode = Schema.AccessMode.READ_ONLY)
    private Long id;

    @NotBlank(message = "El nombre es obligatorio")
    @Size(min = 2, max = 100)
    @Schema(description = "Nombre del producto", example = "Laptop Pro 15", requiredMode = Schema.RequiredMode.REQUIRED)
    private String nombre;

    @NotNull
    @DecimalMin(value = "0.01", message = "El precio debe ser mayor que 0")
    @Schema(description = "Precio en euros", example = "999.99", requiredMode = Schema.RequiredMode.REQUIRED)
    private Double precio;

    @Schema(description = "Descripción detallada del producto", example = "Portátil de alto rendimiento con procesador i7")
    private String descripcion;

    @NotBlank
    @Schema(description = "Categoría del producto", example = "electronica",
            allowableValues = {"electronica", "ropa", "hogar", "deportes"})
    private String categoria;

    @Min(0)
    @Schema(description = "Unidades disponibles en stock", example = "50", defaultValue = "0")
    private Integer stock = 0;

    @Schema(description = "Indica si el producto está disponible para compra", example = "true", defaultValue = "true")
    private Boolean activo = true;
}
```

### Controlador con Anotaciones

```java
// src/main/java/com/ejemplo/controller/ProductoController.java
package com.ejemplo.controller;

import com.ejemplo.dto.ProductoDTO;
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.media.ArraySchema;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponse;
import io.swagger.v3.oas.annotations.responses.ApiResponses;
import io.swagger.v3.oas.annotations.security.SecurityRequirement;
import io.swagger.v3.oas.annotations.tags.Tag;
import jakarta.validation.Valid;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/v1/productos")
@Tag(name = "Productos", description = "Operaciones para gestionar el catálogo de productos")
@SecurityRequirement(name = "bearerAuth")
public class ProductoController {

    @GetMapping
    @Operation(
        summary = "Listar productos",
        description = "Obtiene la lista completa de productos con soporte de filtrado y paginación"
    )
    @ApiResponses({
        @ApiResponse(
            responseCode = "200",
            description = "Lista de productos obtenida correctamente",
            content = @Content(
                mediaType = "application/json",
                array = @ArraySchema(schema = @Schema(implementation = ProductoDTO.class))
            )
        ),
        @ApiResponse(responseCode = "401", description = "No autenticado",
            content = @Content(schema = @Schema(hidden = true)))
    })
    public ResponseEntity<List<ProductoDTO>> listarProductos(
        @Parameter(description = "Número de página (empieza en 1)", example = "1")
        @RequestParam(defaultValue = "1") int pagina,
        
        @Parameter(description = "Elementos por página", example = "20")
        @RequestParam(defaultValue = "20") int tamanio,
        
        @Parameter(description = "Filtrar por categoría")
        @RequestParam(required = false) String categoria
    ) {
        // Implementación...
        return ResponseEntity.ok(List.of());
    }

    @PostMapping
    @Operation(summary = "Crear producto", description = "Crea un nuevo producto en el catálogo")
    @ApiResponses({
        @ApiResponse(responseCode = "201", description = "Producto creado correctamente",
            content = @Content(schema = @Schema(implementation = ProductoDTO.class))),
        @ApiResponse(responseCode = "400", description = "Datos de entrada inválidos"),
        @ApiResponse(responseCode = "409", description = "El SKU ya existe")
    })
    public ResponseEntity<ProductoDTO> crearProducto(
        @io.swagger.v3.oas.annotations.parameters.RequestBody(
            description = "Datos del producto a crear",
            required = true,
            content = @Content(schema = @Schema(implementation = ProductoDTO.class))
        )
        @Valid @RequestBody ProductoDTO producto
    ) {
        // Implementación...
        return ResponseEntity.status(HttpStatus.CREATED).body(producto);
    }

    @GetMapping("/{id}")
    @Operation(summary = "Obtener producto por ID")
    @ApiResponses({
        @ApiResponse(responseCode = "200", description = "Producto encontrado",
            content = @Content(schema = @Schema(implementation = ProductoDTO.class))),
        @ApiResponse(responseCode = "404", description = "Producto no encontrado")
    })
    public ResponseEntity<ProductoDTO> obtenerProducto(
        @Parameter(description = "ID del producto", required = true, example = "1")
        @PathVariable Long id
    ) {
        // Implementación...
        return ResponseEntity.notFound().build();
    }

    @PutMapping("/{id}")
    @Operation(summary = "Actualizar producto completo")
    @ApiResponse(responseCode = "200", description = "Producto actualizado")
    @ApiResponse(responseCode = "404", description = "Producto no encontrado")
    public ResponseEntity<ProductoDTO> actualizarProducto(
        @PathVariable Long id,
        @Valid @RequestBody ProductoDTO producto
    ) {
        // Implementación...
        return ResponseEntity.ok(producto);
    }

    @PatchMapping("/{id}")
    @Operation(summary = "Actualizar producto parcialmente")
    @ApiResponse(responseCode = "200", description = "Producto actualizado")
    public ResponseEntity<ProductoDTO> actualizarParcial(
        @PathVariable Long id,
        @RequestBody java.util.Map<String, Object> cambios
    ) {
        // Implementación...
        return ResponseEntity.ok(new ProductoDTO());
    }

    @DeleteMapping("/{id}")
    @Operation(summary = "Eliminar producto")
    @ApiResponse(responseCode = "204", description = "Producto eliminado correctamente")
    @ApiResponse(responseCode = "404", description = "Producto no encontrado")
    public ResponseEntity<Void> eliminarProducto(@PathVariable Long id) {
        // Implementación...
        return ResponseEntity.noContent().build();
    }
}
```

### Configurar Seguridad JWT en Spring Boot

```java
// src/main/java/com/ejemplo/config/OpenApiConfig.java
@Configuration
public class OpenApiConfig {

    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
            .info(new Info().title("Mi API").version("v1"))
            .addSecurityItem(new SecurityRequirement().addList("bearerAuth"))
            .components(new Components()
                .addSecuritySchemes("bearerAuth",
                    new SecurityScheme()
                        .name("bearerAuth")
                        .type(SecurityScheme.Type.HTTP)
                        .scheme("bearer")
                        .bearerFormat("JWT")
                        .description("Introduce el token JWT obtenido del endpoint /auth/login")
                )
                .addSecuritySchemes("apiKey",
                    new SecurityScheme()
                        .name("X-API-Key")
                        .type(SecurityScheme.Type.APIKEY)
                        .in(SecurityScheme.In.HEADER)
                )
            );
    }
}
```

### Agrupar Endpoints con Tags

```java
// Definir tags globalmente en la configuración
@Bean
public OpenAPI customOpenAPI() {
    return new OpenAPI()
        .info(...)
        .tags(List.of(
            new Tag().name("Productos").description("Gestión del catálogo de productos"),
            new Tag().name("Usuarios").description("Gestión de usuarios y autenticación"),
            new Tag().name("Pedidos").description("Gestión de pedidos y seguimiento"),
            new Tag().name("Admin").description("Operaciones administrativas")
        ));
}
```

---

## 11. Swagger UI y Swagger Editor

### Swagger UI

**Swagger UI** es la interfaz web que permite explorar y probar tu API de forma interactiva.

#### Acceso

Con springdoc-openapi en Spring Boot:
- **Swagger UI**: `http://localhost:8080/swagger-ui.html`
- **OpenAPI JSON**: `http://localhost:8080/v3/api-docs`
- **OpenAPI YAML**: `http://localhost:8080/v3/api-docs.yaml`

#### Personalizar Swagger UI

```java
@Bean
public SwaggerUiConfigParameters swaggerUiConfigParameters() {
    SwaggerUiConfigParameters config = new SwaggerUiConfigParameters();
    config.setDocExpansion("list");          // list | full | none
    config.setDefaultModelsExpandDepth(-1); // Ocultar schemas por defecto
    config.setDisplayRequestDuration(true); // Mostrar tiempo de respuesta
    config.setFilter(true);                 // Habilitar barra de búsqueda
    config.setSupportedSubmitMethods(List.of("get", "post", "put", "delete", "patch"));
    return config;
}
```

```properties
# application.properties - Personalización adicional
springdoc.swagger-ui.doc-expansion=list
springdoc.swagger-ui.default-models-expand-depth=-1
springdoc.swagger-ui.display-request-duration=true
springdoc.swagger-ui.filter=true
springdoc.swagger-ui.try-it-out-enabled=true
springdoc.swagger-ui.persist-authorization=true

# Personalizar el título del navegador
springdoc.swagger-ui.layout=BaseLayout
springdoc.swagger-ui.display-operation-id=false
```

### Swagger Editor

**Swagger Editor** es un editor online en tiempo real disponible en:
- 🌐 **Online**: https://editor.swagger.io
- 🐳 **Docker**: `docker run -p 8080:8080 swaggerapi/swagger-editor`

#### Características

- Validación en tiempo real de la especificación OpenAPI
- Autocompletado y sugerencias
- Vista previa de Swagger UI integrada
- Soporte para YAML y JSON
- Importar/exportar especificaciones

#### Usar Swagger Editor con Docker

```bash
# Swagger Editor
docker run -d -p 8081:8080 swaggerapi/swagger-editor

# Swagger UI standalone
docker run -d -p 8082:8080 \
  -e SWAGGER_JSON_URL=https://petstore3.swagger.io/api/v3/openapi.json \
  swaggerapi/swagger-ui

# Swagger UI con tu propia spec
docker run -d -p 8082:8080 \
  -v /ruta/a/tu/spec.yaml:/usr/share/nginx/html/spec.yaml \
  -e SWAGGER_JSON=/usr/share/nginx/html/spec.yaml \
  swaggerapi/swagger-ui
```

---

## 12. Generación de Código con OpenAPI Generator

**OpenAPI Generator** puede generar código cliente y servidor en más de 50 lenguajes a partir de una especificación OpenAPI.

### Instalación

```bash
# Con npm
npm install @openapitools/openapi-generator-cli -g

# Con Homebrew (macOS)
brew install openapi-generator

# Con Docker (sin instalación)
docker run --rm openapitools/openapi-generator-cli generate ...

# Descargar JAR directamente
wget https://repo1.maven.org/maven2/org/openapitools/openapi-generator-cli/7.2.0/openapi-generator-cli-7.2.0.jar
```

### Generadores Disponibles

```bash
# Ver todos los generadores disponibles
openapi-generator-cli list

# Generadores de cliente más usados:
# java, typescript-axios, typescript-fetch, python, javascript,
# csharp, go, kotlin, swift5, dart, php, ruby

# Generadores de servidor:
# spring, nodejs-express-server, python-flask, aspnetcore,
# go-server, kotlin-spring, php-slim4
```

### Generar Cliente Java

```bash
openapi-generator-cli generate \
  -i https://petstore3.swagger.io/api/v3/openapi.json \
  -g java \
  -o ./cliente-java \
  --additional-properties=artifactId=mi-api-cliente,groupId=com.ejemplo,apiPackage=com.ejemplo.api,modelPackage=com.ejemplo.model
```

### Generar Cliente TypeScript/Axios

```bash
openapi-generator-cli generate \
  -i ./api-spec.yaml \
  -g typescript-axios \
  -o ./cliente-typescript \
  --additional-properties=npmName=mi-api-client,supportsES6=true
```

### Generar Servidor Spring Boot

```bash
openapi-generator-cli generate \
  -i ./api-spec.yaml \
  -g spring \
  -o ./servidor-spring \
  --additional-properties=artifactId=mi-api,groupId=com.ejemplo,basePackage=com.ejemplo,configPackage=com.ejemplo.config,apiPackage=com.ejemplo.api,modelPackage=com.ejemplo.model,interfaceOnly=true,useSpringBoot3=true
```

### Con Maven Plugin

```xml
<!-- pom.xml -->
<plugin>
    <groupId>org.openapitools</groupId>
    <artifactId>openapi-generator-maven-plugin</artifactId>
    <version>7.2.0</version>
    <executions>
        <execution>
            <goals>
                <goal>generate</goal>
            </goals>
            <configuration>
                <inputSpec>${project.basedir}/src/main/resources/api-spec.yaml</inputSpec>
                <generatorName>spring</generatorName>
                <apiPackage>com.ejemplo.api</apiPackage>
                <modelPackage>com.ejemplo.model</modelPackage>
                <configOptions>
                    <interfaceOnly>true</interfaceOnly>
                    <useSpringBoot3>true</useSpringBoot3>
                    <useTags>true</useTags>
                    <delegatePattern>true</delegatePattern>
                </configOptions>
            </configuration>
        </execution>
    </executions>
</plugin>
```

### Archivo de Configuración

```yaml
# openapi-generator-config.yaml
generatorName: spring
inputSpec: ./api-spec.yaml
outputDir: ./generated
apiPackage: com.ejemplo.api
modelPackage: com.ejemplo.model
additionalProperties:
  interfaceOnly: "true"
  useSpringBoot3: "true"
  useTags: "true"
  dateLibrary: java8
  java8: "true"
  openApiNullable: "false"
```

```bash
# Usar el archivo de configuración
openapi-generator-cli generate -c openapi-generator-config.yaml
```

---

## 13. OpenAPI Avanzado

### Callbacks (Webhooks)

Los callbacks describen operaciones que tu API llamará a URLs externas (webhooks):

```yaml
paths:
  /suscripciones:
    post:
      summary: Crear suscripción con webhook
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                eventoUrl:
                  type: string
                  format: uri
                  description: URL que recibirá los eventos
      responses:
        '201':
          description: Suscripción creada
      
      # Define qué llamadas hará tu API al cliente
      callbacks:
        nuevoEvento:
          '{$request.body#/eventoUrl}':    # URL dinámica del request
            post:
              summary: Notificación de nuevo evento
              requestBody:
                required: true
                content:
                  application/json:
                    schema:
                      type: object
                      properties:
                        tipo:
                          type: string
                        datos:
                          type: object
              responses:
                '200':
                  description: Evento procesado
```

### Webhooks (OpenAPI 3.1)

```yaml
openapi: 3.1.0

webhooks:
  nuevoUsuario:
    post:
      summary: Notificación de nuevo usuario
      description: Llamado cuando se registra un nuevo usuario
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Usuario'
      responses:
        '200':
          description: Webhook procesado correctamente
```

### Extensiones Personalizadas

Las extensiones OpenAPI usan el prefijo `x-`:

```yaml
info:
  title: Mi API
  x-logo:                    # Logo personalizado para la doc
    url: https://empresa.com/logo.png
  x-internal: false          # Campo personalizado

paths:
  /productos:
    get:
      x-rate-limit: 100       # Límite de peticiones
      x-cache-ttl: 300        # TTL del caché en segundos
      x-roles-required:       # Roles necesarios
        - admin
        - gestor
```

### Paginación Estandarizada

```yaml
components:
  parameters:
    PaginaParam:
      name: page
      in: query
      schema:
        type: integer
        minimum: 0
        default: 0
    TamanioParam:
      name: size
      in: query
      schema:
        type: integer
        minimum: 1
        maximum: 100
        default: 20
    OrdenParam:
      name: sort
      in: query
      description: "Campo y dirección (ej: nombre,asc o precio,desc)"
      schema:
        type: string
        example: "nombre,asc"
  
  schemas:
    PaginaRespuesta:
      type: object
      properties:
        content:
          type: array
          items: {}
        page:
          type: integer
        size:
          type: integer
        totalElements:
          type: integer
          format: int64
        totalPages:
          type: integer
        first:
          type: boolean
        last:
          type: boolean
        numberOfElements:
          type: integer
```

### Versionado de la API

```yaml
# Opción 1: Versión en la URL
servers:
  - url: https://api.empresa.com/v1
  - url: https://api.empresa.com/v2

# Opción 2: Versión en el header
components:
  parameters:
    ApiVersion:
      name: Accept-Version
      in: header
      schema:
        type: string
        enum: [v1, v2]
        default: v1

# Opción 3: Múltiples specs separadas
# api-v1.yaml y api-v2.yaml
```

### Documentar Errores de Forma Completa

```yaml
components:
  schemas:
    ProblemDetails:
      # Basado en RFC 7807
      type: object
      properties:
        type:
          type: string
          format: uri
          description: URI que identifica el tipo de problema
          example: "https://api.empresa.com/errores/validacion"
        title:
          type: string
          description: Título corto del problema
          example: "Error de validación"
        status:
          type: integer
          description: Código de estado HTTP
          example: 400
        detail:
          type: string
          description: Explicación legible del problema
          example: "El campo 'email' no tiene un formato válido"
        instance:
          type: string
          format: uri
          description: URI que identifica la instancia específica del problema
          example: "/pedidos/1234"
        errores:
          type: array
          items:
            type: object
            properties:
              campo:
                type: string
              mensaje:
                type: string
```

---

## 14. Proyecto Completo: API de Gestión de Tareas

Vamos a construir la especificación completa de una **API de Gestión de Tareas** con todos los conceptos aprendidos.

### Spec Completa: `tareas-api.yaml`

```yaml
openapi: 3.0.3

info:
  title: API de Gestión de Tareas
  description: |
    API REST completa para gestionar tareas y proyectos.
    
    ## Autenticación
    Esta API utiliza JWT Bearer Token. Para obtener un token:
    1. Haz POST a `/auth/login` con tus credenciales
    2. Usa el token devuelto en la cabecera `Authorization: Bearer <token>`
    
    ## Rate Limiting
    - Usuarios autenticados: 1000 req/hora
    - Sin autenticar: 100 req/hora
  version: 1.0.0
  contact:
    name: Soporte API
    email: api@tareas.com
  license:
    name: MIT

servers:
  - url: http://localhost:8080/api/v1
    description: Desarrollo
  - url: https://api.tareas.com/v1
    description: Producción

security:
  - bearerAuth: []

tags:
  - name: Autenticación
    description: Login, logout y gestión de tokens
  - name: Proyectos
    description: Gestión de proyectos
  - name: Tareas
    description: Gestión de tareas dentro de proyectos
  - name: Usuarios
    description: Gestión de usuarios (solo admin)

paths:
  /auth/login:
    post:
      tags: [Autenticación]
      summary: Iniciar sesión
      operationId: login
      security: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/LoginRequest'
      responses:
        '200':
          description: Login exitoso
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TokenResponse'
        '401':
          description: Credenciales inválidas
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'

  /auth/refresh:
    post:
      tags: [Autenticación]
      summary: Renovar token
      operationId: refreshToken
      security: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required: [refreshToken]
              properties:
                refreshToken:
                  type: string
      responses:
        '200':
          description: Token renovado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TokenResponse'
        '401':
          description: Refresh token inválido o expirado

  /proyectos:
    get:
      tags: [Proyectos]
      summary: Listar proyectos del usuario
      operationId: listarProyectos
      parameters:
        - $ref: '#/components/parameters/PaginaParam'
        - $ref: '#/components/parameters/TamanioParam'
        - name: estado
          in: query
          schema:
            $ref: '#/components/schemas/EstadoProyecto'
      responses:
        '200':
          description: Lista de proyectos
          content:
            application/json:
              schema:
                allOf:
                  - $ref: '#/components/schemas/PaginaRespuesta'
                  - type: object
                    properties:
                      content:
                        type: array
                        items:
                          $ref: '#/components/schemas/Proyecto'
        '401':
          $ref: '#/components/responses/NoAutenticado'
    
    post:
      tags: [Proyectos]
      summary: Crear proyecto
      operationId: crearProyecto
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ProyectoInput'
      responses:
        '201':
          description: Proyecto creado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Proyecto'
          links:
            ListarTareas:
              operationId: listarTareas
              parameters:
                proyectoId: '$response.body#/id'
        '400':
          $ref: '#/components/responses/ErrorValidacion'
        '401':
          $ref: '#/components/responses/NoAutenticado'

  /proyectos/{proyectoId}:
    parameters:
      - $ref: '#/components/parameters/ProyectoIdParam'
    
    get:
      tags: [Proyectos]
      summary: Obtener proyecto
      operationId: obtenerProyecto
      responses:
        '200':
          description: Proyecto encontrado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Proyecto'
        '404':
          $ref: '#/components/responses/NoEncontrado'
    
    put:
      tags: [Proyectos]
      summary: Actualizar proyecto
      operationId: actualizarProyecto
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ProyectoInput'
      responses:
        '200':
          description: Proyecto actualizado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Proyecto'
        '404':
          $ref: '#/components/responses/NoEncontrado'
    
    delete:
      tags: [Proyectos]
      summary: Eliminar proyecto
      operationId: eliminarProyecto
      responses:
        '204':
          description: Proyecto eliminado
        '404':
          $ref: '#/components/responses/NoEncontrado'

  /proyectos/{proyectoId}/tareas:
    parameters:
      - $ref: '#/components/parameters/ProyectoIdParam'
    
    get:
      tags: [Tareas]
      summary: Listar tareas del proyecto
      operationId: listarTareas
      parameters:
        - $ref: '#/components/parameters/PaginaParam'
        - $ref: '#/components/parameters/TamanioParam'
        - name: estado
          in: query
          schema:
            $ref: '#/components/schemas/EstadoTarea'
        - name: asignadoA
          in: query
          description: Filtrar por ID de usuario asignado
          schema:
            type: integer
        - name: prioridad
          in: query
          schema:
            $ref: '#/components/schemas/Prioridad'
      responses:
        '200':
          description: Lista de tareas
          content:
            application/json:
              schema:
                allOf:
                  - $ref: '#/components/schemas/PaginaRespuesta'
                  - type: object
                    properties:
                      content:
                        type: array
                        items:
                          $ref: '#/components/schemas/Tarea'
        '404':
          $ref: '#/components/responses/NoEncontrado'
    
    post:
      tags: [Tareas]
      summary: Crear tarea
      operationId: crearTarea
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TareaInput'
            examples:
              tareaSimple:
                summary: Tarea simple
                value:
                  titulo: "Implementar login"
                  prioridad: "ALTA"
              tareaCompleta:
                summary: Tarea con todos los campos
                value:
                  titulo: "Diseñar base de datos"
                  descripcion: "Crear el diagrama ER y los scripts SQL"
                  prioridad: "ALTA"
                  asignadoA: 5
                  fechaVencimiento: "2024-12-31"
                  etiquetas: ["backend", "base-datos"]
      responses:
        '201':
          description: Tarea creada
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Tarea'
        '400':
          $ref: '#/components/responses/ErrorValidacion'
        '404':
          $ref: '#/components/responses/NoEncontrado'

  /proyectos/{proyectoId}/tareas/{tareaId}:
    parameters:
      - $ref: '#/components/parameters/ProyectoIdParam'
      - $ref: '#/components/parameters/TareaIdParam'
    
    get:
      tags: [Tareas]
      summary: Obtener tarea
      operationId: obtenerTarea
      responses:
        '200':
          description: Tarea encontrada
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Tarea'
        '404':
          $ref: '#/components/responses/NoEncontrado'
    
    patch:
      tags: [Tareas]
      summary: Actualizar tarea parcialmente
      operationId: actualizarTarea
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TareaUpdate'
      responses:
        '200':
          description: Tarea actualizada
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Tarea'
    
    delete:
      tags: [Tareas]
      summary: Eliminar tarea
      operationId: eliminarTarea
      responses:
        '204':
          description: Tarea eliminada

  /proyectos/{proyectoId}/tareas/{tareaId}/estado:
    parameters:
      - $ref: '#/components/parameters/ProyectoIdParam'
      - $ref: '#/components/parameters/TareaIdParam'
    
    patch:
      tags: [Tareas]
      summary: Cambiar estado de tarea
      operationId: cambiarEstadoTarea
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required: [estado]
              properties:
                estado:
                  $ref: '#/components/schemas/EstadoTarea'
      responses:
        '200':
          description: Estado actualizado
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Tarea'

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  parameters:
    PaginaParam:
      name: page
      in: query
      description: Número de página (empieza en 0)
      schema:
        type: integer
        minimum: 0
        default: 0
    
    TamanioParam:
      name: size
      in: query
      description: Tamaño de la página
      schema:
        type: integer
        minimum: 1
        maximum: 100
        default: 20
    
    ProyectoIdParam:
      name: proyectoId
      in: path
      required: true
      description: ID del proyecto
      schema:
        type: integer
        format: int64
    
    TareaIdParam:
      name: tareaId
      in: path
      required: true
      description: ID de la tarea
      schema:
        type: integer
        format: int64

  responses:
    NoAutenticado:
      description: No autenticado - Token requerido o inválido
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          example:
            codigo: "NO_AUTENTICADO"
            mensaje: "Token de autenticación requerido"
    
    NoEncontrado:
      description: Recurso no encontrado
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          example:
            codigo: "NO_ENCONTRADO"
            mensaje: "El recurso solicitado no existe"
    
    ErrorValidacion:
      description: Error de validación de datos
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'

  schemas:
    # Enumeraciones
    EstadoProyecto:
      type: string
      enum: [ACTIVO, PAUSADO, COMPLETADO, ARCHIVADO]
      example: ACTIVO
    
    EstadoTarea:
      type: string
      enum: [PENDIENTE, EN_PROGRESO, EN_REVISION, COMPLETADA, CANCELADA]
      example: PENDIENTE
    
    Prioridad:
      type: string
      enum: [BAJA, MEDIA, ALTA, CRITICA]
      example: ALTA

    # Autenticación
    LoginRequest:
      type: object
      required: [email, password]
      properties:
        email:
          type: string
          format: email
          example: "usuario@ejemplo.com"
        password:
          type: string
          format: password
          example: "miContraseñaSegura123"
    
    TokenResponse:
      type: object
      properties:
        accessToken:
          type: string
          description: Token JWT de acceso (expira en 1 hora)
        refreshToken:
          type: string
          description: Token de renovación (expira en 7 días)
        tokenType:
          type: string
          example: "Bearer"
        expiresIn:
          type: integer
          description: Segundos hasta expiración
          example: 3600

    # Proyectos
    ProyectoInput:
      type: object
      required: [nombre]
      properties:
        nombre:
          type: string
          minLength: 1
          maxLength: 100
          example: "Rediseño del sitio web"
        descripcion:
          type: string
          maxLength: 500
        fechaInicio:
          type: string
          format: date
        fechaFin:
          type: string
          format: date
    
    Proyecto:
      allOf:
        - $ref: '#/components/schemas/ProyectoInput'
        - type: object
          properties:
            id:
              type: integer
              format: int64
              readOnly: true
            estado:
              $ref: '#/components/schemas/EstadoProyecto'
            creadoPor:
              type: integer
              readOnly: true
            creadoEn:
              type: string
              format: date-time
              readOnly: true
            totalTareas:
              type: integer
              readOnly: true
            tareasCompletadas:
              type: integer
              readOnly: true

    # Tareas
    TareaInput:
      type: object
      required: [titulo]
      properties:
        titulo:
          type: string
          minLength: 1
          maxLength: 200
          example: "Implementar autenticación JWT"
        descripcion:
          type: string
          maxLength: 2000
        prioridad:
          $ref: '#/components/schemas/Prioridad'
        asignadoA:
          type: integer
          format: int64
          description: ID del usuario asignado
        fechaVencimiento:
          type: string
          format: date
        etiquetas:
          type: array
          items:
            type: string
          maxItems: 10
          uniqueItems: true
          example: ["backend", "seguridad"]
    
    TareaUpdate:
      type: object
      description: Todos los campos son opcionales para actualización parcial
      properties:
        titulo:
          type: string
          minLength: 1
          maxLength: 200
        descripcion:
          type: string
        prioridad:
          $ref: '#/components/schemas/Prioridad'
        estado:
          $ref: '#/components/schemas/EstadoTarea'
        asignadoA:
          type: integer
        fechaVencimiento:
          type: string
          format: date
        etiquetas:
          type: array
          items:
            type: string
    
    Tarea:
      allOf:
        - $ref: '#/components/schemas/TareaInput'
        - type: object
          properties:
            id:
              type: integer
              format: int64
              readOnly: true
            estado:
              $ref: '#/components/schemas/EstadoTarea'
            proyectoId:
              type: integer
              readOnly: true
            creadoPor:
              type: integer
              readOnly: true
            creadoEn:
              type: string
              format: date-time
              readOnly: true
            actualizadoEn:
              type: string
              format: date-time
              readOnly: true

    # Paginación
    PaginaRespuesta:
      type: object
      properties:
        content:
          type: array
          items: {}
        page:
          type: integer
        size:
          type: integer
        totalElements:
          type: integer
          format: int64
        totalPages:
          type: integer
        first:
          type: boolean
        last:
          type: boolean

    # Error
    Error:
      type: object
      required: [codigo, mensaje]
      properties:
        codigo:
          type: string
          example: "ERROR_VALIDACION"
        mensaje:
          type: string
          example: "Los datos proporcionados no son válidos"
        detalles:
          type: array
          items:
            type: object
            properties:
              campo:
                type: string
              error:
                type: string
        timestamp:
          type: string
          format: date-time
        path:
          type: string
```

### Implementación Spring Boot del Proyecto

```java
// Estructura del proyecto
src/
├── main/
│   ├── java/com/ejemplo/tareas/
│   │   ├── TareasApplication.java
│   │   ├── config/
│   │   │   └── OpenApiConfig.java
│   │   ├── controller/
│   │   │   ├── AuthController.java
│   │   │   ├── ProyectoController.java
│   │   │   └── TareaController.java
│   │   ├── dto/
│   │   │   ├── LoginRequest.java
│   │   │   ├── TokenResponse.java
│   │   │   ├── ProyectoDTO.java
│   │   │   └── TareaDTO.java
│   │   ├── model/
│   │   │   ├── Proyecto.java
│   │   │   └── Tarea.java
│   │   └── service/
│   │       ├── ProyectoService.java
│   │       └── TareaService.java
│   └── resources/
│       ├── application.properties
│       └── api-spec.yaml
```

```java
// TareasApplication.java
@SpringBootApplication
public class TareasApplication {
    public static void main(String[] args) {
        SpringApplication.run(TareasApplication.class, args);
    }
}
```

```properties
# application.properties
spring.application.name=tareas-api
server.port=8080

# Swagger UI disponible en http://localhost:8080/swagger-ui.html
springdoc.swagger-ui.path=/swagger-ui.html
springdoc.api-docs.path=/v3/api-docs
springdoc.swagger-ui.try-it-out-enabled=true
springdoc.swagger-ui.persist-authorization=true
springdoc.swagger-ui.display-request-duration=true
```

---

## 15. Buenas Prácticas

### ✅ Diseño de la API

```yaml
# ✅ BIEN: Nombres de recursos en plural y en sustantivos
/usuarios
/proyectos
/proyectos/{id}/tareas

# ❌ MAL: Verbos en las rutas
/getUsuarios
/crearProyecto
/eliminarTarea
```

```yaml
# ✅ BIEN: Usar los métodos HTTP correctamente
GET    /tareas          # Leer lista
POST   /tareas          # Crear
GET    /tareas/{id}     # Leer uno
PUT    /tareas/{id}     # Reemplazar completo
PATCH  /tareas/{id}     # Actualizar parcialmente
DELETE /tareas/{id}     # Eliminar

# ✅ BIEN: operationId descriptivo en camelCase
operationId: listarTareas
operationId: crearTarea
operationId: obtenerTareaPorId
```

### ✅ Documentación

```yaml
# ✅ BIEN: Descripciones útiles con Markdown
description: |
  Obtiene la lista paginada de tareas.
  
  **Filtros disponibles:**
  - `estado`: Filtra por estado de la tarea
  - `prioridad`: Filtra por nivel de prioridad
  
  **Ordenación por defecto:** Fecha de creación descendente

# ❌ MAL: Sin descripción o descripción inútil
description: Gets tasks
```

```yaml
# ✅ BIEN: Ejemplos reales y representativos
example:
  id: 1
  titulo: "Implementar autenticación JWT"
  estado: "EN_PROGRESO"
  prioridad: "ALTA"

# ❌ MAL: Ejemplos genéricos
example:
  id: 0
  titulo: "string"
  estado: "string"
```

### ✅ Schemas

```yaml
# ✅ BIEN: Separar schema de entrada y salida
TareaInput:           # Para crear/actualizar (sin id, sin timestamps)
  type: object
  required: [titulo]
  properties:
    titulo:
      type: string

Tarea:               # Respuesta completa
  allOf:
    - $ref: '#/components/schemas/TareaInput'
    - type: object
      properties:
        id:
          readOnly: true
        creadoEn:
          readOnly: true

# ❌ MAL: Mismo schema para todo (id en el body de creación)
Tarea:
  properties:
    id:
      type: integer
    titulo:
      type: string
```

```yaml
# ✅ BIEN: Marcar campos requeridos explícitamente
type: object
required:
  - titulo
  - email
properties:
  titulo:
    type: string
  email:
    type: string
  descripcion:      # No en 'required' = opcional
    type: string

# ✅ BIEN: Usar $ref para evitar repetición
$ref: '#/components/schemas/Error'

# ❌ MAL: Copiar y pegar schemas
```

### ✅ Versionado y Mantenimiento

```yaml
# ✅ BIEN: Versionar la API en la URL
servers:
  - url: https://api.empresa.com/v1
  - url: https://api.empresa.com/v2

# ✅ BIEN: Marcar endpoints deprecados
paths:
  /usuarios/buscar:   # Será eliminado en v3
    get:
      deprecated: true
      description: "⚠️ Deprecado. Usar GET /usuarios?nombre=xxx en su lugar"
```

### ✅ Seguridad

```yaml
# ✅ BIEN: Documentar todos los posibles errores de autenticación
responses:
  '401':
    description: Token no proporcionado o inválido
  '403':
    description: Sin permisos para esta operación

# ✅ BIEN: No incluir tokens reales en los ejemplos
example:
  Authorization: "Bearer eyJhbGciOiJIUzI1NiJ9..."  # ✅ Token de ejemplo
  # NO poner tokens reales aquí
```

### ✅ Herramientas Recomendadas

| Propósito | Herramienta | URL |
|-----------|-------------|-----|
| Editar specs | Swagger Editor | https://editor.swagger.io |
| Editar con IDE | Stoplight Studio | https://stoplight.io/studio |
| Mock server | Prism | https://stoplight.io/open-source/prism |
| Testing | Dredd | https://dredd.org |
| Linting | Spectral | https://stoplight.io/open-source/spectral |
| Diff de specs | OpenAPI Diff | https://github.com/OpenAPITools/openapi-diff |
| Generación de código | OpenAPI Generator | https://openapi-generator.tech |

### ✅ Checklist de Calidad

```
□ Todos los endpoints tienen summary y description
□ Todos los parámetros tienen description y example
□ Todos los schemas tienen description en propiedades clave
□ Los ejemplos son representativos y realistas
□ Se documentan todos los posibles códigos de respuesta (incluyendo errores)
□ Se usan $ref para evitar repetición de schemas
□ Los campos requeridos están marcados en required[]
□ Se usa readOnly en campos que no se envían en el request
□ La seguridad está documentada y configurada
□ Los endpoints están agrupados con tags lógicos
□ operationId único en cada endpoint
□ La spec ha sido validada con Swagger Editor o Spectral
```

---

## 16. Recursos Adicionales

### Documentación Oficial

| Recurso | URL |
|---------|-----|
| Especificación OpenAPI 3.0 | https://swagger.io/specification/ |
| Especificación OpenAPI 3.1 | https://spec.openapis.org/oas/v3.1.0 |
| Springdoc OpenAPI (Spring Boot) | https://springdoc.org |
| OpenAPI Generator | https://openapi-generator.tech |
| Swagger Tools | https://swagger.io/tools/ |

### Herramientas Online

| Herramienta | URL | Descripción |
|-------------|-----|-------------|
| Swagger Editor | https://editor.swagger.io | Editor online oficial |
| Swagger UI Demo | https://petstore3.swagger.io | Demo con Petstore API |
| Stoplight Studio | https://stoplight.io | Editor visual avanzado |
| Apicurio Studio | https://studio.apicur.io | Editor OpenAPI visual |
| Redoc | https://redocly.github.io/redoc | Documentación elegante alternativa |

### Librerías por Lenguaje/Framework

| Framework | Librería | Descripción |
|-----------|----------|-------------|
| Spring Boot | springdoc-openapi | Integración automática |
| Node.js/Express | swagger-ui-express | Swagger UI para Express |
| FastAPI (Python) | integrado | Swagger UI automático |
| Django REST | drf-spectacular | OpenAPI para Django |
| NestJS | @nestjs/swagger | Decoradores para NestJS |
| .NET | Swashbuckle.AspNetCore | Swagger para ASP.NET Core |
| Go | swaggo/swag | Anotaciones para Go |

### Ejemplos y Plantillas

```bash
# Clonar especificación de ejemplo (Petstore)
curl -O https://petstore3.swagger.io/api/v3/openapi.json

# Ver especificación en Swagger UI local con Docker
docker run -p 8080:8080 \
  -e SWAGGER_JSON_URL=https://petstore3.swagger.io/api/v3/openapi.json \
  swaggerapi/swagger-ui
```

### Aprender Más

- 📖 [OpenAPI 3.0 Tutorial](https://support.smartbear.com/swaggerhub/docs/tutorials/openapi-3-tutorial.html) - SmartBear
- 📖 [Aprende OpenAPI en 10 minutos](https://learnxinyminutes.com/docs/yaml/) - Learn X in Y Minutes
- 🎥 [API Design 101](https://www.youtube.com/watch?v=eMgDpPX3XCE) - YouTube
- 📚 [Designing Web APIs](https://www.oreilly.com/library/view/designing-web-apis/9781492082507/) - O'Reilly

---

> 💡 **Tip final**: La mejor documentación de API es la que está **siempre actualizada** y es **fácil de entender** para quien la consume. Empieza con lo básico y mejora iterativamente. Una spec imperfecta y actualizada vale más que una spec perfecta y desactualizada.

---

*📝 Tutorial creado para el repositorio de Tutoriales para Estudiantes de Informática*
