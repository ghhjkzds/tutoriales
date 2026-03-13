# 🐹 Go (Golang) — Guía Completa de Nivel Básico a Experto

Esta guía cubre Go desde cero hasta técnicas avanzadas. Cada sección incluye explicaciones claras, ejemplos de código listos para ejecutar y ejercicios prácticos con soluciones.

---

## 📋 Tabla de Contenidos

1. [¿Qué es Go y por qué aprenderlo?](#1-qué-es-go-y-por-qué-aprenderlo)
2. [Instalación y entorno de trabajo](#2-instalación-y-entorno-de-trabajo)
3. [Nivel Básico — Fundamentos del lenguaje](#3-nivel-básico--fundamentos-del-lenguaje)
4. [Nivel Básico — Funciones y paquetes](#4-nivel-básico--funciones-y-paquetes)
5. [Nivel Básico — Arrays, Slices y Maps](#5-nivel-básico--arrays-slices-y-maps)
6. [Nivel Intermedio — Structs y métodos](#6-nivel-intermedio--structs-y-métodos)
7. [Nivel Intermedio — Interfaces](#7-nivel-intermedio--interfaces)
8. [Nivel Intermedio — Gestión de errores](#8-nivel-intermedio--gestión-de-errores)
9. [Nivel Intermedio — Punteros](#9-nivel-intermedio--punteros)
10. [Nivel Intermedio — Goroutines y concurrencia](#10-nivel-intermedio--goroutines-y-concurrencia)
11. [Nivel Avanzado — Channels](#11-nivel-avanzado--channels)
12. [Nivel Avanzado — Context y cancelación](#12-nivel-avanzado--context-y-cancelación)
13. [Nivel Avanzado — Generics](#13-nivel-avanzado--generics)
14. [Nivel Avanzado — Testing en Go](#14-nivel-avanzado--testing-en-go)
15. [Nivel Avanzado — HTTP y APIs REST](#15-nivel-avanzado--http-y-apis-rest)
16. [Nivel Experto — Patrones de diseño en Go](#16-nivel-experto--patrones-de-diseño-en-go)
17. [Nivel Experto — Optimización y perfilado](#17-nivel-experto--optimización-y-perfilado)
18. [Nivel Experto — CLI, módulos y tooling](#18-nivel-experto--cli-módulos-y-tooling)
19. [Proyecto completo: API REST con Go](#19-proyecto-completo-api-rest-con-go)
20. [Ejercicios prácticos resueltos](#20-ejercicios-prácticos-resueltos)
21. [Recursos y próximos pasos](#21-recursos-y-próximos-pasos)

---

## 1. ¿Qué es Go y por qué aprenderlo?

**Go** (también llamado *Golang*) es un lenguaje de programación compilado, de tipado estático, diseñado en Google por Robert Griesemer, Rob Pike y Ken Thompson. Fue lanzado públicamente en 2009.

### Características principales

| Característica | Descripción |
|---|---|
| **Compilado** | Genera binarios nativos muy rápidos |
| **Tipado estático** | Los errores de tipo se detectan en compilación |
| **Recolección de basura** | Gestión automática de memoria |
| **Concurrencia nativa** | Goroutines y channels integrados en el lenguaje |
| **Sintaxis simple** | Pocas palabras clave, fácil de aprender |
| **Multiplataforma** | Compila para Linux, macOS, Windows, ARM, etc. |
| **Estándar único** | `gofmt` formatea el código de manera uniforme |

### ¿Cuándo usar Go?

- Microservicios y APIs REST de alto rendimiento
- Herramientas de línea de comandos (CLI)
- Servicios con alta concurrencia (servidores, proxies)
- Aplicaciones cloud-native (Kubernetes, Docker están escritos en Go)
- Scripts de infraestructura y DevOps

---

## 2. Instalación y entorno de trabajo

### Instalación

```bash
# Linux/macOS — descarga desde https://go.dev/dl/
wget https://go.dev/dl/go1.22.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.22.0.linux-amd64.tar.gz

# Agregar al PATH en ~/.bashrc o ~/.zshrc
export PATH=$PATH:/usr/local/go/bin

# Verificar instalación
go version
# go version go1.22.0 linux/amd64
```

```bash
# macOS con Homebrew
brew install go

# Windows — descargar el instalador .msi desde https://go.dev/dl/
```

### Primer proyecto con módulos

```bash
# Crear directorio del proyecto
mkdir hola-mundo && cd hola-mundo

# Inicializar módulo Go
go mod init github.com/tuusuario/hola-mundo

# Esto crea go.mod — el archivo de dependencias del proyecto
```

```go
// main.go
package main

import "fmt"

func main() {
    fmt.Println("¡Hola, Mundo!")
}
```

```bash
# Ejecutar directamente
go run main.go

# Compilar a binario
go build -o hola-mundo
./hola-mundo
```

### Editor recomendado

- **VS Code** con extensión `golang.go` — autocompletado, linting, debugging
- **GoLand** (JetBrains) — IDE completo para Go

---

## 3. Nivel Básico — Fundamentos del lenguaje

### Variables y tipos de datos

```go
package main

import "fmt"

func main() {
    // Declaración con var
    var nombre string = "Alice"
    var edad int = 30
    var altura float64 = 1.75
    var activo bool = true

    // Inferencia de tipo con :=
    ciudad := "Madrid"
    temperatura := 22.5
    esDia := true

    fmt.Println(nombre, edad, altura, activo)
    fmt.Println(ciudad, temperatura, esDia)

    // Declaración múltiple
    var (
        x int     = 10
        y float32 = 3.14
        z string  = "Go"
    )
    fmt.Println(x, y, z)

    // Constantes
    const Pi = 3.14159
    const Mensaje = "Constante de texto"
    fmt.Println(Pi, Mensaje)
}
```

### Tipos de datos disponibles

```go
package main

import "fmt"

func main() {
    // Enteros
    var a int = 42         // tamaño depende del sistema (32 o 64 bits)
    var b int8 = 127       // -128 a 127
    var c int16 = 32767    // -32768 a 32767
    var d int32 = 2147483647
    var e int64 = 9223372036854775807
    var f uint = 100       // entero sin signo
    var g byte = 255       // alias de uint8

    // Flotantes
    var h float32 = 3.14
    var i float64 = 3.141592653589793

    // Complejos
    var j complex64 = 1 + 2i
    var k complex128 = 3 + 4i

    // Cadenas y runas
    var texto string = "Hola Go"
    var caracter rune = 'A'    // alias de int32

    // Booleano
    var ok bool = true

    fmt.Println(a, b, c, d, e, f, g)
    fmt.Println(h, i, j, k)
    fmt.Println(texto, caracter, ok)
}
```

### Operadores

```go
package main

import "fmt"

func main() {
    // Aritméticos
    a, b := 10, 3
    fmt.Println(a+b, a-b, a*b, a/b, a%b) // 13 7 30 3 1

    // Relacionales
    fmt.Println(a > b, a < b, a == b, a != b, a >= b, a <= b)

    // Lógicos
    t, f := true, false
    fmt.Println(t && f, t || f, !t) // false true false

    // Asignación compuesta
    x := 5
    x += 3  // x = 8
    x -= 1  // x = 7
    x *= 2  // x = 14
    x /= 2  // x = 7
    x %=3   // x = 1
    fmt.Println(x)

    // Incremento / decremento (solo como sentencias, no expresiones)
    x++
    x--
    fmt.Println(x) // 1
}
```

### Estructuras de control

```go
package main

import "fmt"

func main() {
    // if / else if / else
    nota := 85
    if nota >= 90 {
        fmt.Println("Sobresaliente")
    } else if nota >= 70 {
        fmt.Println("Notable")
    } else if nota >= 50 {
        fmt.Println("Aprobado")
    } else {
        fmt.Println("Suspenso")
    }

    // if con inicialización
    if valor := calcular(); valor > 0 {
        fmt.Println("Positivo:", valor)
    }

    // switch
    dia := "lunes"
    switch dia {
    case "lunes", "martes", "miércoles", "jueves", "viernes":
        fmt.Println("Día laborable")
    case "sábado", "domingo":
        fmt.Println("Fin de semana")
    default:
        fmt.Println("Día desconocido")
    }

    // switch sin condición (equivale a if-else)
    x := 42
    switch {
    case x < 0:
        fmt.Println("Negativo")
    case x == 0:
        fmt.Println("Cero")
    default:
        fmt.Println("Positivo")
    }

    // for clásico
    for i := 0; i < 5; i++ {
        fmt.Print(i, " ")
    }
    fmt.Println()

    // for como while
    n := 1
    for n < 100 {
        n *= 2
    }
    fmt.Println(n) // 128

    // for infinito con break
    contador := 0
    for {
        contador++
        if contador == 3 {
            break
        }
    }
    fmt.Println(contador)

    // continue
    for i := 0; i < 10; i++ {
        if i%2 == 0 {
            continue
        }
        fmt.Print(i, " ") // 1 3 5 7 9
    }
    fmt.Println()
}

func calcular() int { return 42 }
```

### 📝 Ejercicio 1 — Tabla de multiplicar

Escribe un programa que imprima la tabla de multiplicar del 1 al 10 para un número dado.

```go
package main

import "fmt"

func main() {
    numero := 7
    fmt.Printf("Tabla del %d:\n", numero)
    for i := 1; i <= 10; i++ {
        fmt.Printf("%d x %d = %d\n", numero, i, numero*i)
    }
}
```

---

## 4. Nivel Básico — Funciones y paquetes

### Funciones

```go
package main

import (
    "fmt"
    "math"
)

// Función básica
func saludar(nombre string) {
    fmt.Printf("¡Hola, %s!\n", nombre)
}

// Función con valor de retorno
func suma(a, b int) int {
    return a + b
}

// Múltiples valores de retorno
func dividir(a, b float64) (float64, error) {
    if b == 0 {
        return 0, fmt.Errorf("no se puede dividir por cero")
    }
    return a / b, nil
}

// Retornos con nombre
func circulo(radio float64) (area, perimetro float64) {
    area = math.Pi * radio * radio
    perimetro = 2 * math.Pi * radio
    return // retorno desnudo
}

// Función variádica
func sumaVariadica(nums ...int) int {
    total := 0
    for _, n := range nums {
        total += n
    }
    return total
}

// Función como valor (first-class function)
func aplicar(f func(int) int, x int) int {
    return f(x)
}

// Closure
func contador() func() int {
    n := 0
    return func() int {
        n++
        return n
    }
}

func main() {
    saludar("Go")

    fmt.Println(suma(3, 4)) // 7

    resultado, err := dividir(10, 3)
    if err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Printf("%.2f\n", resultado)
    }

    a, p := circulo(5)
    fmt.Printf("Área: %.2f, Perímetro: %.2f\n", a, p)

    fmt.Println(sumaVariadica(1, 2, 3, 4, 5)) // 15

    doble := func(x int) int { return x * 2 }
    fmt.Println(aplicar(doble, 7)) // 14

    c := contador()
    fmt.Println(c(), c(), c()) // 1 2 3
}
```

### defer, panic y recover

```go
package main

import "fmt"

// defer — se ejecuta cuando la función termina (LIFO)
func ejemploDefer() {
    fmt.Println("inicio")
    defer fmt.Println("primero en entrar, último en salir") // se ejecuta al final
    defer fmt.Println("segundo defer")
    fmt.Println("fin del cuerpo")
}

// panic y recover
func division(a, b int) (resultado int) {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recuperado del pánico:", r)
            resultado = -1
        }
    }()
    if b == 0 {
        panic("división por cero")
    }
    return a / b
}

func main() {
    ejemploDefer()
    // Salida:
    // inicio
    // fin del cuerpo
    // segundo defer
    // primero en entrar, último en salir

    fmt.Println(division(10, 2))  // 5
    fmt.Println(division(10, 0))  // Recuperado del pánico: división por cero -> -1
}
```

### 📝 Ejercicio 2 — Calculadora funcional

Implementa una calculadora que recibe dos números y un operador, y usa funciones.

```go
package main

import (
    "fmt"
    "errors"
)

func calcular(a, b float64, op string) (float64, error) {
    switch op {
    case "+":
        return a + b, nil
    case "-":
        return a - b, nil
    case "*":
        return a * b, nil
    case "/":
        if b == 0 {
            return 0, errors.New("división por cero")
        }
        return a / b, nil
    default:
        return 0, fmt.Errorf("operador desconocido: %s", op)
    }
}

func main() {
    operaciones := []struct {
        a, b float64
        op   string
    }{
        {10, 5, "+"},
        {10, 5, "-"},
        {10, 5, "*"},
        {10, 5, "/"},
        {10, 0, "/"},
        {10, 5, "^"},
    }

    for _, o := range operaciones {
        res, err := calcular(o.a, o.b, o.op)
        if err != nil {
            fmt.Printf("%.0f %s %.0f = Error: %v\n", o.a, o.op, o.b, err)
        } else {
            fmt.Printf("%.0f %s %.0f = %.2f\n", o.a, o.op, o.b, res)
        }
    }
}
```

---

## 5. Nivel Básico — Arrays, Slices y Maps

### Arrays

```go
package main

import "fmt"

func main() {
    // Array de tamaño fijo
    var numeros [5]int
    numeros[0] = 10
    numeros[1] = 20
    fmt.Println(numeros) // [10 20 0 0 0]

    // Array inicializado
    colores := [3]string{"rojo", "verde", "azul"}
    fmt.Println(colores[1]) // verde

    // Tamaño inferido
    primos := [...]int{2, 3, 5, 7, 11}
    fmt.Println(len(primos)) // 5

    // Recorrer array
    for i, v := range primos {
        fmt.Printf("primos[%d] = %d\n", i, v)
    }

    // Arrays son valor (se copian)
    a := [3]int{1, 2, 3}
    b := a
    b[0] = 99
    fmt.Println(a, b) // [1 2 3] [99 2 3]
}
```

### Slices

```go
package main

import "fmt"

func main() {
    // Slice — vista dinámica sobre un array
    s := []int{1, 2, 3, 4, 5}
    fmt.Println(s, len(s), cap(s))

    // Slice de un array
    arr := [6]int{10, 20, 30, 40, 50, 60}
    slice := arr[1:4] // [20 30 40]
    fmt.Println(slice)

    // make — crea slice con longitud y capacidad
    s2 := make([]string, 3, 5)
    s2[0] = "a"
    s2[1] = "b"
    s2[2] = "c"
    fmt.Println(s2, len(s2), cap(s2))

    // append
    s3 := []int{1, 2, 3}
    s3 = append(s3, 4, 5)
    fmt.Println(s3) // [1 2 3 4 5]

    // append de otro slice
    s4 := []int{6, 7}
    s3 = append(s3, s4...)
    fmt.Println(s3) // [1 2 3 4 5 6 7]

    // copy
    origen := []int{1, 2, 3}
    destino := make([]int, len(origen))
    n := copy(destino, origen)
    fmt.Println(destino, n) // [1 2 3] 3

    // Slices son referencia (no copian datos)
    original := []int{1, 2, 3}
    referencia := original
    referencia[0] = 99
    fmt.Println(original) // [99 2 3] — ¡original también cambió!

    // Slice 2D
    matriz := [][]int{
        {1, 2, 3},
        {4, 5, 6},
        {7, 8, 9},
    }
    fmt.Println(matriz[1][2]) // 6
}
```

### Maps

```go
package main

import "fmt"

func main() {
    // Creación
    m := map[string]int{
        "alice": 30,
        "bob":   25,
        "carol": 35,
    }

    // Acceder
    fmt.Println(m["alice"]) // 30

    // Verificar existencia
    valor, existe := m["david"]
    if existe {
        fmt.Println("david tiene", valor)
    } else {
        fmt.Println("david no existe")
    }

    // Agregar / modificar
    m["david"] = 28
    m["alice"] = 31

    // Eliminar
    delete(m, "bob")

    // Recorrer (orden no garantizado)
    for nombre, edad := range m {
        fmt.Printf("%s: %d años\n", nombre, edad)
    }

    // make
    contador := make(map[string]int)
    palabras := []string{"go", "es", "genial", "go", "es", "rápido", "go"}
    for _, p := range palabras {
        contador[p]++
    }
    fmt.Println(contador) // map[es:2 genial:1 go:3 rápido:1]
}
```

### 📝 Ejercicio 3 — Frecuencia de palabras

```go
package main

import (
    "fmt"
    "strings"
    "sort"
)

func frecuencia(texto string) map[string]int {
    palabras := strings.Fields(strings.ToLower(texto))
    conteo := make(map[string]int)
    for _, p := range palabras {
        // Eliminar puntuación básica
        p = strings.Trim(p, ".,!?;:")
        conteo[p]++
    }
    return conteo
}

func main() {
    texto := "Go es rápido. Go es simple. Go es eficiente y Go es concurrente."
    conteo := frecuencia(texto)

    // Ordenar por clave para salida reproducible
    claves := make([]string, 0, len(conteo))
    for k := range conteo {
        claves = append(claves, k)
    }
    sort.Strings(claves)

    for _, k := range claves {
        fmt.Printf("%-15s: %d\n", k, conteo[k])
    }
}
```

---

## 6. Nivel Intermedio — Structs y métodos

### Structs

```go
package main

import "fmt"

// Definición de struct
type Persona struct {
    Nombre   string
    Apellido string
    Edad     int
    Email    string
}

// Struct anidado
type Empresa struct {
    Nombre    string
    Direccion Direccion
    Empleados []Persona
}

type Direccion struct {
    Calle  string
    Ciudad string
    Pais   string
}

// Método sobre struct (valor)
func (p Persona) NombreCompleto() string {
    return p.Nombre + " " + p.Apellido
}

// Método con puntero (puede modificar el struct)
func (p *Persona) Cumpleanos() {
    p.Edad++
}

func (p Persona) String() string {
    return fmt.Sprintf("Persona{%s, %d años, %s}", p.NombreCompleto(), p.Edad, p.Email)
}

func main() {
    // Crear struct
    p1 := Persona{
        Nombre:   "Alice",
        Apellido: "Gómez",
        Edad:     29,
        Email:    "alice@ejemplo.com",
    }

    // Acceder a campos
    fmt.Println(p1.Nombre)
    fmt.Println(p1.NombreCompleto())
    fmt.Println(p1) // usa método String()

    // Modificar mediante puntero
    p1.Cumpleanos()
    fmt.Println(p1.Edad) // 30

    // Puntero a struct
    p2 := &Persona{Nombre: "Bob", Apellido: "López", Edad: 35}
    p2.Edad = 36 // Go auto-derreferencia
    fmt.Println(p2.NombreCompleto())

    // Struct anónimo
    producto := struct {
        Nombre string
        Precio float64
    }{
        Nombre: "Laptop",
        Precio: 999.99,
    }
    fmt.Println(producto)

    // Empresa con struct anidado
    empresa := Empresa{
        Nombre: "GoTech SL",
        Direccion: Direccion{
            Calle:  "Calle Mayor 1",
            Ciudad: "Madrid",
            Pais:   "España",
        },
        Empleados: []Persona{p1, *p2},
    }
    fmt.Println(empresa.Nombre, "-", empresa.Direccion.Ciudad)
    for _, e := range empresa.Empleados {
        fmt.Println(" -", e.NombreCompleto())
    }
}
```

### Composición (embedding)

```go
package main

import "fmt"

type Animal struct {
    Nombre string
    Peso   float64
}

func (a Animal) Descripcion() string {
    return fmt.Sprintf("%s (%.1f kg)", a.Nombre, a.Peso)
}

// Perro embebe Animal — hereda sus campos y métodos
type Perro struct {
    Animal
    Raza string
}

func (p Perro) Ladrar() string {
    return p.Nombre + " dice: ¡Guau!"
}

type Gato struct {
    Animal
    Indoor bool
}

func (g Gato) Maullar() string {
    return g.Nombre + " dice: ¡Miau!"
}

func main() {
    p := Perro{
        Animal: Animal{Nombre: "Rex", Peso: 28.5},
        Raza:   "Pastor Alemán",
    }

    fmt.Println(p.Descripcion()) // método heredado de Animal
    fmt.Println(p.Ladrar())
    fmt.Println(p.Nombre)   // campo accesible directamente
    fmt.Println(p.Raza)
}
```

---

## 7. Nivel Intermedio — Interfaces

Las interfaces en Go son **implícitas**: un tipo implementa una interfaz si tiene todos los métodos que ésta define.

```go
package main

import (
    "fmt"
    "math"
)

// Definición de interfaz
type Forma interface {
    Area() float64
    Perimetro() float64
}

type Rectangulo struct {
    Ancho, Alto float64
}

func (r Rectangulo) Area() float64 {
    return r.Ancho * r.Alto
}

func (r Rectangulo) Perimetro() float64 {
    return 2 * (r.Ancho + r.Alto)
}

type Circulo struct {
    Radio float64
}

func (c Circulo) Area() float64 {
    return math.Pi * c.Radio * c.Radio
}

func (c Circulo) Perimetro() float64 {
    return 2 * math.Pi * c.Radio
}

type Triangulo struct {
    Base, Altura, A, B, C float64
}

func (t Triangulo) Area() float64 {
    return 0.5 * t.Base * t.Altura
}

func (t Triangulo) Perimetro() float64 {
    return t.A + t.B + t.C
}

// Función que acepta cualquier Forma
func imprimirForma(f Forma) {
    fmt.Printf("Área: %.2f | Perímetro: %.2f\n", f.Area(), f.Perimetro())
}

// Interface vacía — acepta cualquier tipo
func describir(i interface{}) {
    fmt.Printf("Tipo: %T | Valor: %v\n", i, i)
}

// Type assertion
func procesar(i interface{}) {
    switch v := i.(type) {
    case int:
        fmt.Printf("Es un entero: %d\n", v)
    case string:
        fmt.Printf("Es una cadena: %q\n", v)
    case Forma:
        fmt.Printf("Es una forma con área %.2f\n", v.Area())
    default:
        fmt.Printf("Tipo desconocido: %T\n", v)
    }
}

func main() {
    formas := []Forma{
        Rectangulo{Ancho: 5, Alto: 3},
        Circulo{Radio: 4},
        Triangulo{Base: 6, Altura: 4, A: 5, B: 5, C: 6},
    }

    for _, f := range formas {
        imprimirForma(f)
    }

    describir(42)
    describir("hola")
    describir(3.14)
    describir(Rectangulo{2, 3})

    procesar(100)
    procesar("Go")
    procesar(Circulo{Radio: 2})
}
```

### Interface Stringer y error

```go
package main

import "fmt"

// Stringer — fmt usa este método al imprimir
type Temperatura struct {
    Celsius float64
}

func (t Temperatura) String() string {
    return fmt.Sprintf("%.1f°C (%.1f°F)", t.Celsius, t.Celsius*9/5+32)
}

// Implementar la interfaz error
type ErrorValidacion struct {
    Campo   string
    Mensaje string
}

func (e ErrorValidacion) Error() string {
    return fmt.Sprintf("error en campo '%s': %s", e.Campo, e.Mensaje)
}

func validarEdad(edad int) error {
    if edad < 0 {
        return ErrorValidacion{Campo: "edad", Mensaje: "no puede ser negativa"}
    }
    if edad > 150 {
        return ErrorValidacion{Campo: "edad", Mensaje: "valor irreal"}
    }
    return nil
}

func main() {
    t := Temperatura{Celsius: 100}
    fmt.Println(t) // usa String() automáticamente

    if err := validarEdad(-5); err != nil {
        fmt.Println(err)
    }

    if err := validarEdad(200); err != nil {
        // Type assertion para acceder al tipo concreto
        if ve, ok := err.(ErrorValidacion); ok {
            fmt.Printf("Campo problemático: %s\n", ve.Campo)
        }
    }
}
```

---

## 8. Nivel Intermedio — Gestión de errores

Go usa errores como valores, no excepciones. Este es uno de los patrones más importantes del lenguaje.

```go
package main

import (
    "errors"
    "fmt"
    "strconv"
)

// Errores centinela
var (
    ErrNoEncontrado  = errors.New("elemento no encontrado")
    ErrPermisoDenado = errors.New("permiso denegado")
)

// Error con contexto usando fmt.Errorf y %w (wrapping)
func buscarUsuario(id int) (string, error) {
    usuarios := map[int]string{1: "Alice", 2: "Bob"}
    u, ok := usuarios[id]
    if !ok {
        return "", fmt.Errorf("buscarUsuario(%d): %w", id, ErrNoEncontrado)
    }
    return u, nil
}

// Tipo de error personalizado con datos adicionales
type ErrorHTTP struct {
    Codigo  int
    Mensaje string
}

func (e *ErrorHTTP) Error() string {
    return fmt.Sprintf("HTTP %d: %s", e.Codigo, e.Mensaje)
}

func hacerPeticion(url string) error {
    if url == "" {
        return &ErrorHTTP{Codigo: 400, Mensaje: "URL vacía"}
    }
    return nil
}

// Encadenamiento de errores
func procesarEntrada(s string) (int, error) {
    n, err := strconv.Atoi(s)
    if err != nil {
        return 0, fmt.Errorf("procesarEntrada: no es un número válido %q: %w", s, err)
    }
    if n < 0 {
        return 0, fmt.Errorf("procesarEntrada: %w", errors.New("el número debe ser positivo"))
    }
    return n * 2, nil
}

func main() {
    // Comprobación básica
    usuario, err := buscarUsuario(1)
    if err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Println("Usuario:", usuario)
    }

    // errors.Is — compara con error centinela (incluso envuelto)
    _, err = buscarUsuario(99)
    if errors.Is(err, ErrNoEncontrado) {
        fmt.Println("El usuario no existe") // ← se ejecuta esto
    }
    fmt.Println(err)

    // errors.As — extrae el tipo de error concreto
    err2 := hacerPeticion("")
    var httpErr *ErrorHTTP
    if errors.As(err2, &httpErr) {
        fmt.Printf("Código de estado: %d\n", httpErr.Codigo)
    }

    // Encadenado
    v, err := procesarEntrada("abc")
    if err != nil {
        fmt.Println("Error:", err)
    } else {
        fmt.Println("Resultado:", v)
    }

    v, err = procesarEntrada("21")
    if err == nil {
        fmt.Println("Resultado:", v) // 42
    }
}
```

---

## 9. Nivel Intermedio — Punteros

```go
package main

import "fmt"

// Los punteros permiten modificar valores y evitar copias costosas
func duplicar(n *int) {
    *n = *n * 2
}

func incrementar(n *int) {
    (*n)++
}

type Grande struct {
    Datos [1000]int
}

// Pasar por puntero evita copiar 8000 bytes
func procesarGrande(g *Grande) {
    g.Datos[0] = 42
}

// new — asigna memoria y devuelve puntero
func crearEntero(valor int) *int {
    p := new(int)
    *p = valor
    return p
}

func main() {
    x := 10
    fmt.Println("Antes:", x)  // 10
    duplicar(&x)
    fmt.Println("Después:", x) // 20

    y := 5
    incrementar(&y)
    fmt.Println(y) // 6

    // nil pointer
    var p *int
    fmt.Println(p)        // <nil>
    fmt.Println(p == nil) // true

    // & y *
    a := 42
    ptr := &a
    fmt.Println(ptr)   // dirección de memoria: 0xc0000...
    fmt.Println(*ptr)  // 42
    *ptr = 100
    fmt.Println(a)     // 100

    // new
    numPtr := crearEntero(77)
    fmt.Println(*numPtr) // 77

    // Puntero a struct
    type Punto struct{ X, Y int }
    p2 := &Punto{X: 1, Y: 2}
    p2.X = 10 // equivale a (*p2).X = 10
    fmt.Println(*p2)
}
```

---

## 10. Nivel Intermedio — Goroutines y concurrencia

Las goroutines son funciones que se ejecutan de forma concurrente. Son muy ligeras (comienzan con solo ~2 KB de stack).

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

// Goroutine básica
func tarea(id int, wg *sync.WaitGroup) {
    defer wg.Done()
    fmt.Printf("Tarea %d: inicio\n", id)
    time.Sleep(time.Duration(id*100) * time.Millisecond)
    fmt.Printf("Tarea %d: fin\n", id)
}

// Race condition sin protección — ¡MAL!
func sumaInsegura() {
    contador := 0
    var wg sync.WaitGroup
    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            contador++ // ¡condición de carrera!
        }()
    }
    wg.Wait()
    fmt.Println("Inseguro:", contador) // resultado impredecible
}

// Con Mutex — protege el acceso al recurso compartido
func sumaSegura() {
    contador := 0
    var wg sync.WaitGroup
    var mu sync.Mutex

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            mu.Lock()
            contador++
            mu.Unlock()
        }()
    }
    wg.Wait()
    fmt.Println("Seguro:", contador) // siempre 1000
}

// Con atomic — aún más eficiente para operaciones simples
import "sync/atomic"

func sumaAtomica() {
    var contador int64
    var wg sync.WaitGroup

    for i := 0; i < 1000; i++ {
        wg.Add(1)
        go func() {
            defer wg.Done()
            atomic.AddInt64(&contador, 1)
        }()
    }
    wg.Wait()
    fmt.Println("Atómica:", contador) // siempre 1000
}

func main() {
    var wg sync.WaitGroup
    for i := 1; i <= 5; i++ {
        wg.Add(1)
        go tarea(i, &wg)
    }
    wg.Wait()
    fmt.Println("Todas las tareas completadas")

    sumaSegura()
    sumaAtomica()
}
```

> ⚠️ **Detectar condiciones de carrera**: ejecuta con `go run -race main.go` para que Go detecte automáticamente las race conditions.

---

## 11. Nivel Avanzado — Channels

Los channels son la forma idiomática de comunicar goroutines en Go ("no comuniques compartiendo memoria, comparte memoria comunicando").

```go
package main

import (
    "fmt"
    "time"
)

// Channel sin buffer — sincrónico
func pingPong() {
    ch := make(chan string)

    go func() {
        msg := <-ch
        fmt.Println("Recibido:", msg)
        ch <- "pong"
    }()

    ch <- "ping"
    respuesta := <-ch
    fmt.Println("Respuesta:", respuesta)
}

// Channel con buffer — asincrónico
func bufferizado() {
    ch := make(chan int, 3)
    ch <- 1
    ch <- 2
    ch <- 3
    // No bloquea porque hay capacidad

    fmt.Println(<-ch) // 1
    fmt.Println(<-ch) // 2
    fmt.Println(<-ch) // 3
}

// Productor-consumidor
func generador(n int) <-chan int {
    ch := make(chan int)
    go func() {
        defer close(ch)
        for i := 0; i < n; i++ {
            ch <- i * i
        }
    }()
    return ch
}

// Fan-out — un productor, múltiples consumidores
func worker(id int, jobs <-chan int, resultados chan<- int) {
    for j := range jobs {
        fmt.Printf("Worker %d procesando trabajo %d\n", id, j)
        time.Sleep(50 * time.Millisecond)
        resultados <- j * 2
    }
}

// select — esperar en múltiples channels
func seleccionar() {
    ch1 := make(chan string)
    ch2 := make(chan string)

    go func() {
        time.Sleep(100 * time.Millisecond)
        ch1 <- "canal 1"
    }()

    go func() {
        time.Sleep(200 * time.Millisecond)
        ch2 <- "canal 2"
    }()

    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-ch1:
            fmt.Println("Recibido de", msg1)
        case msg2 := <-ch2:
            fmt.Println("Recibido de", msg2)
        }
    }
}

// Timeout con select
func conTimeout(ch <-chan string) {
    select {
    case msg := <-ch:
        fmt.Println("Mensaje recibido:", msg)
    case <-time.After(500 * time.Millisecond):
        fmt.Println("Tiempo de espera agotado")
    }
}

func main() {
    pingPong()
    bufferizado()

    // Productor-consumidor
    for v := range generador(5) {
        fmt.Print(v, " ")
    }
    fmt.Println()

    // Fan-out con pool de workers
    trabajos := make(chan int, 10)
    resultados := make(chan int, 10)

    // Lanzar 3 workers
    for w := 1; w <= 3; w++ {
        go worker(w, trabajos, resultados)
    }

    // Enviar 9 trabajos
    for j := 1; j <= 9; j++ {
        trabajos <- j
    }
    close(trabajos)

    // Recoger resultados
    for r := 1; r <= 9; r++ {
        fmt.Printf("Resultado: %d\n", <-resultados)
    }

    seleccionar()
}
```

---

## 12. Nivel Avanzado — Context y cancelación

```go
package main

import (
    "context"
    "fmt"
    "time"
)

// Context con timeout
func operacionLenta(ctx context.Context, id int) error {
    select {
    case <-time.After(2 * time.Second): // simula trabajo
        fmt.Printf("Operación %d completada\n", id)
        return nil
    case <-ctx.Done():
        fmt.Printf("Operación %d cancelada: %v\n", id, ctx.Err())
        return ctx.Err()
    }
}

// Context con cancelación manual
func servicioConCancelacion() {
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()

    go func() {
        time.Sleep(100 * time.Millisecond)
        fmt.Println("Cancelando desde goroutine externa...")
        cancel()
    }()

    if err := operacionLenta(ctx, 99); err != nil {
        fmt.Println("Error:", err)
    }
}

// Pasar valores en el context (usar con moderación)
type claveTipo string

const claveUsuario claveTipo = "usuario"

func handler(ctx context.Context) {
    if usuario, ok := ctx.Value(claveUsuario).(string); ok {
        fmt.Println("Usuario en contexto:", usuario)
    }
}

func main() {
    // Timeout de 500ms — la operación tarda 2s → se cancela
    ctx, cancel := context.WithTimeout(context.Background(), 500*time.Millisecond)
    defer cancel()

    if err := operacionLenta(ctx, 1); err != nil {
        fmt.Println("Resultado:", err)
    }

    // Deadline absoluto
    deadline := time.Now().Add(1 * time.Second)
    ctx2, cancel2 := context.WithDeadline(context.Background(), deadline)
    defer cancel2()
    operacionLenta(ctx2, 2)

    // Cancelación manual
    servicioConCancelacion()

    // Valores en context
    ctx3 := context.WithValue(context.Background(), claveUsuario, "alice")
    handler(ctx3)
}
```

---

## 13. Nivel Avanzado — Generics

Introducidos en Go 1.18, los genéricos permiten escribir funciones y tipos parametrizados por tipo.

```go
package main

import (
    "fmt"
    "golang.org/x/exp/constraints"
)

// Función genérica básica
func Min[T constraints.Ordered](a, b T) T {
    if a < b {
        return a
    }
    return b
}

func Max[T constraints.Ordered](a, b T) T {
    if a > b {
        return a
    }
    return b
}

// Mapa funcional genérico
func Mapa[T, U any](lista []T, f func(T) U) []U {
    resultado := make([]U, len(lista))
    for i, v := range lista {
        resultado[i] = f(v)
    }
    return resultado
}

// Filtrar genérico
func Filtrar[T any](lista []T, pred func(T) bool) []T {
    var resultado []T
    for _, v := range lista {
        if pred(v) {
            resultado = append(resultado, v)
        }
    }
    return resultado
}

// Reducir genérico
func Reducir[T, U any](lista []T, inicial U, f func(U, T) U) U {
    acc := inicial
    for _, v := range lista {
        acc = f(acc, v)
    }
    return acc
}

// Struct genérico — pila (stack)
type Pila[T any] struct {
    elementos []T
}

func (p *Pila[T]) Push(v T) {
    p.elementos = append(p.elementos, v)
}

func (p *Pila[T]) Pop() (T, bool) {
    var cero T
    if len(p.elementos) == 0 {
        return cero, false
    }
    ultimo := len(p.elementos) - 1
    v := p.elementos[ultimo]
    p.elementos = p.elementos[:ultimo]
    return v, true
}

func (p *Pila[T]) Len() int { return len(p.elementos) }

// Restricción personalizada
type Numero interface {
    ~int | ~int32 | ~int64 | ~float32 | ~float64
}

func Suma[T Numero](nums []T) T {
    var total T
    for _, n := range nums {
        total += n
    }
    return total
}

func main() {
    fmt.Println(Min(3, 5))           // 3
    fmt.Println(Min("abc", "xyz"))   // abc
    fmt.Println(Max(3.14, 2.71))     // 3.14

    nums := []int{1, 2, 3, 4, 5}
    dobles := Mapa(nums, func(n int) int { return n * 2 })
    fmt.Println(dobles) // [2 4 6 8 10]

    pares := Filtrar(nums, func(n int) bool { return n%2 == 0 })
    fmt.Println(pares) // [2 4]

    suma := Reducir(nums, 0, func(acc, n int) int { return acc + n })
    fmt.Println(suma) // 15

    // Pila genérica de strings
    var pilaStr Pila[string]
    pilaStr.Push("a")
    pilaStr.Push("b")
    pilaStr.Push("c")
    v, _ := pilaStr.Pop()
    fmt.Println(v, pilaStr.Len()) // c 2

    // Suma genérica
    fmt.Println(Suma([]float64{1.1, 2.2, 3.3})) // 6.6
}
```

---

## 14. Nivel Avanzado — Testing en Go

```go
// archivo: calculadora/calculadora.go
package calculadora

import "errors"

func Suma(a, b int) int       { return a + b }
func Resta(a, b int) int      { return a - b }
func Multiplica(a, b int) int { return a * b }

func Divide(a, b float64) (float64, error) {
    if b == 0 {
        return 0, errors.New("división por cero")
    }
    return a / b, nil
}
```

```go
// archivo: calculadora/calculadora_test.go
package calculadora

import (
    "testing"
    "math"
)

// Tests unitarios básicos
func TestSuma(t *testing.T) {
    resultado := Suma(2, 3)
    esperado := 5
    if resultado != esperado {
        t.Errorf("Suma(2, 3) = %d; quería %d", resultado, esperado)
    }
}

// Tests basados en tabla (table-driven tests) — patrón idiomático de Go
func TestDivide(t *testing.T) {
    casos := []struct {
        nombre   string
        a, b     float64
        esperado float64
        error    bool
    }{
        {"positivos", 10, 2, 5, false},
        {"negativos", -10, 2, -5, false},
        {"resultado decimal", 7, 2, 3.5, false},
        {"división por cero", 10, 0, 0, true},
        {"cero entre número", 0, 5, 0, false},
    }

    for _, tc := range casos {
        t.Run(tc.nombre, func(t *testing.T) {
            resultado, err := Divide(tc.a, tc.b)
            if tc.error {
                if err == nil {
                    t.Error("se esperaba un error pero no hubo ninguno")
                }
            } else {
                if err != nil {
                    t.Errorf("error inesperado: %v", err)
                }
                if math.Abs(resultado-tc.esperado) > 1e-9 {
                    t.Errorf("Divide(%.1f, %.1f) = %.2f; quería %.2f",
                        tc.a, tc.b, resultado, tc.esperado)
                }
            }
        })
    }
}

// Benchmark
func BenchmarkSuma(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Suma(100, 200)
    }
}

func BenchmarkDivide(b *testing.B) {
    for i := 0; i < b.N; i++ {
        Divide(100, 7)
    }
}
```

```bash
# Ejecutar tests
go test ./...

# Con cobertura
go test -cover ./...

# Informe de cobertura en HTML
go test -coverprofile=coverage.out ./...
go tool cover -html=coverage.out

# Benchmarks
go test -bench=. ./...

# Tests específicos
go test -run TestDivide ./calculadora/
```

### Mocks con interfaces

```go
// archivo: servicio/repositorio.go
package servicio

type Usuario struct {
    ID     int
    Nombre string
}

type RepositorioUsuarios interface {
    ObtenerPorID(id int) (*Usuario, error)
    Guardar(u *Usuario) error
}

type ServicioUsuarios struct {
    repo RepositorioUsuarios
}

func NuevoServicio(repo RepositorioUsuarios) *ServicioUsuarios {
    return &ServicioUsuarios{repo: repo}
}

func (s *ServicioUsuarios) ObtenerUsuario(id int) (*Usuario, error) {
    return s.repo.ObtenerPorID(id)
}
```

```go
// archivo: servicio/servicio_test.go
package servicio

import (
    "errors"
    "testing"
)

// Mock manual
type repoMock struct {
    usuarios map[int]*Usuario
}

func (m *repoMock) ObtenerPorID(id int) (*Usuario, error) {
    u, ok := m.usuarios[id]
    if !ok {
        return nil, errors.New("usuario no encontrado")
    }
    return u, nil
}

func (m *repoMock) Guardar(u *Usuario) error {
    m.usuarios[u.ID] = u
    return nil
}

func TestObtenerUsuario(t *testing.T) {
    repo := &repoMock{
        usuarios: map[int]*Usuario{
            1: {ID: 1, Nombre: "Alice"},
        },
    }
    svc := NuevoServicio(repo)

    u, err := svc.ObtenerUsuario(1)
    if err != nil {
        t.Fatal("error inesperado:", err)
    }
    if u.Nombre != "Alice" {
        t.Errorf("nombre = %q; quería %q", u.Nombre, "Alice")
    }

    _, err = svc.ObtenerUsuario(99)
    if err == nil {
        t.Error("se esperaba error para usuario inexistente")
    }
}
```

---

## 15. Nivel Avanzado — HTTP y APIs REST

```go
// archivo: main.go
package main

import (
    "encoding/json"
    "errors"
    "fmt"
    "log"
    "net/http"
    "strconv"
    "strings"
    "sync"
)

// --- Modelos ---

type Tarea struct {
    ID        int    `json:"id"`
    Titulo    string `json:"titulo"`
    Completada bool  `json:"completada"`
}

// --- Repositorio en memoria ---

type Repositorio struct {
    mu      sync.RWMutex
    tareas  map[int]Tarea
    siguiente int
}

func NuevoRepositorio() *Repositorio {
    return &Repositorio{
        tareas:    make(map[int]Tarea),
        siguiente: 1,
    }
}

var ErrNoEncontrado = errors.New("tarea no encontrada")

func (r *Repositorio) ObtenerTodas() []Tarea {
    r.mu.RLock()
    defer r.mu.RUnlock()
    lista := make([]Tarea, 0, len(r.tareas))
    for _, t := range r.tareas {
        lista = append(lista, t)
    }
    return lista
}

func (r *Repositorio) ObtenerPorID(id int) (Tarea, error) {
    r.mu.RLock()
    defer r.mu.RUnlock()
    t, ok := r.tareas[id]
    if !ok {
        return Tarea{}, ErrNoEncontrado
    }
    return t, nil
}

func (r *Repositorio) Crear(t Tarea) Tarea {
    r.mu.Lock()
    defer r.mu.Unlock()
    t.ID = r.siguiente
    r.siguiente++
    r.tareas[t.ID] = t
    return t
}

func (r *Repositorio) Actualizar(t Tarea) (Tarea, error) {
    r.mu.Lock()
    defer r.mu.Unlock()
    if _, ok := r.tareas[t.ID]; !ok {
        return Tarea{}, ErrNoEncontrado
    }
    r.tareas[t.ID] = t
    return t, nil
}

func (r *Repositorio) Eliminar(id int) error {
    r.mu.Lock()
    defer r.mu.Unlock()
    if _, ok := r.tareas[id]; !ok {
        return ErrNoEncontrado
    }
    delete(r.tareas, id)
    return nil
}

// --- Handlers ---

type Handler struct{ repo *Repositorio }

func responder(w http.ResponseWriter, codigo int, datos interface{}) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(codigo)
    json.NewEncoder(w).Encode(datos)
}

func (h *Handler) ListarTareas(w http.ResponseWriter, r *http.Request) {
    responder(w, http.StatusOK, h.repo.ObtenerTodas())
}

func (h *Handler) CrearTarea(w http.ResponseWriter, r *http.Request) {
    var t Tarea
    if err := json.NewDecoder(r.Body).Decode(&t); err != nil {
        responder(w, http.StatusBadRequest, map[string]string{"error": "cuerpo inválido"})
        return
    }
    if strings.TrimSpace(t.Titulo) == "" {
        responder(w, http.StatusBadRequest, map[string]string{"error": "el título es obligatorio"})
        return
    }
    creada := h.repo.Crear(t)
    responder(w, http.StatusCreated, creada)
}

func (h *Handler) ObtenerTarea(w http.ResponseWriter, r *http.Request) {
    id, err := idDeURL(r.URL.Path)
    if err != nil {
        responder(w, http.StatusBadRequest, map[string]string{"error": "ID inválido"})
        return
    }
    t, err := h.repo.ObtenerPorID(id)
    if errors.Is(err, ErrNoEncontrado) {
        responder(w, http.StatusNotFound, map[string]string{"error": "tarea no encontrada"})
        return
    }
    responder(w, http.StatusOK, t)
}

func (h *Handler) EliminarTarea(w http.ResponseWriter, r *http.Request) {
    id, err := idDeURL(r.URL.Path)
    if err != nil {
        responder(w, http.StatusBadRequest, map[string]string{"error": "ID inválido"})
        return
    }
    if err := h.repo.Eliminar(id); errors.Is(err, ErrNoEncontrado) {
        responder(w, http.StatusNotFound, map[string]string{"error": "tarea no encontrada"})
        return
    }
    w.WriteHeader(http.StatusNoContent)
}

// Extrae ID de URLs como /tareas/42
func idDeURL(path string) (int, error) {
    partes := strings.Split(strings.Trim(path, "/"), "/")
    if len(partes) < 2 {
        return 0, fmt.Errorf("sin ID")
    }
    return strconv.Atoi(partes[len(partes)-1])
}

// --- Middleware ---

func middlewareLog(siguiente http.HandlerFunc) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        log.Printf("%s %s", r.Method, r.URL.Path)
        siguiente(w, r)
    }
}

// --- Router ---

func (h *Handler) registrarRutas(mux *http.ServeMux) {
    mux.HandleFunc("/tareas", middlewareLog(func(w http.ResponseWriter, r *http.Request) {
        switch r.Method {
        case http.MethodGet:
            h.ListarTareas(w, r)
        case http.MethodPost:
            h.CrearTarea(w, r)
        default:
            responder(w, http.StatusMethodNotAllowed, map[string]string{"error": "método no permitido"})
        }
    }))

    mux.HandleFunc("/tareas/", middlewareLog(func(w http.ResponseWriter, r *http.Request) {
        switch r.Method {
        case http.MethodGet:
            h.ObtenerTarea(w, r)
        case http.MethodDelete:
            h.EliminarTarea(w, r)
        default:
            responder(w, http.StatusMethodNotAllowed, map[string]string{"error": "método no permitido"})
        }
    }))
}

func main() {
    repo := NuevoRepositorio()
    handler := &Handler{repo: repo}

    mux := http.NewServeMux()
    handler.registrarRutas(mux)

    log.Println("Servidor escuchando en :8080")
    log.Fatal(http.ListenAndServe(":8080", mux))
}
```

```bash
# Probar la API
curl -X POST http://localhost:8080/tareas \
  -H "Content-Type: application/json" \
  -d '{"titulo":"Aprender Go","completada":false}'

curl http://localhost:8080/tareas
curl http://localhost:8080/tareas/1
curl -X DELETE http://localhost:8080/tareas/1
```

---

## 16. Nivel Experto — Patrones de diseño en Go

### Patrón Options (Functional Options)

```go
package main

import (
    "fmt"
    "time"
)

type ServidorHTTP struct {
    host    string
    puerto  int
    timeout time.Duration
    maxConn int
    tls     bool
}

type Opcion func(*ServidorHTTP)

func ConHost(host string) Opcion {
    return func(s *ServidorHTTP) { s.host = host }
}

func ConPuerto(puerto int) Opcion {
    return func(s *ServidorHTTP) { s.puerto = puerto }
}

func ConTimeout(t time.Duration) Opcion {
    return func(s *ServidorHTTP) { s.timeout = t }
}

func ConMaxConexiones(n int) Opcion {
    return func(s *ServidorHTTP) { s.maxConn = n }
}

func ConTLS() Opcion {
    return func(s *ServidorHTTP) { s.tls = true }
}

func NuevoServidor(opciones ...Opcion) *ServidorHTTP {
    // Valores por defecto
    s := &ServidorHTTP{
        host:    "localhost",
        puerto:  8080,
        timeout: 30 * time.Second,
        maxConn: 100,
        tls:     false,
    }
    for _, o := range opciones {
        o(s)
    }
    return s
}

func main() {
    s := NuevoServidor(
        ConPuerto(9090),
        ConTimeout(60*time.Second),
        ConTLS(),
    )
    fmt.Printf("Servidor: %s:%d TLS=%v\n", s.host, s.puerto, s.tls)
}
```

### Patrón Worker Pool

```go
package main

import (
    "fmt"
    "sync"
    "time"
)

type Trabajo struct {
    ID int
}

type Resultado struct {
    TrabajoID int
    Valor     int
}

func poolDeWorkers(numWorkers int, trabajos <-chan Trabajo) <-chan Resultado {
    resultados := make(chan Resultado)
    var wg sync.WaitGroup

    for i := 0; i < numWorkers; i++ {
        wg.Add(1)
        go func(id int) {
            defer wg.Done()
            for t := range trabajos {
                // Simular procesamiento
                time.Sleep(10 * time.Millisecond)
                resultados <- Resultado{
                    TrabajoID: t.ID,
                    Valor:     t.ID * t.ID,
                }
            }
        }(i)
    }

    go func() {
        wg.Wait()
        close(resultados)
    }()

    return resultados
}

func main() {
    trabajos := make(chan Trabajo, 100)
    resultados := poolDeWorkers(5, trabajos)

    // Enviar trabajos
    for i := 1; i <= 20; i++ {
        trabajos <- Trabajo{ID: i}
    }
    close(trabajos)

    // Recoger resultados
    for r := range resultados {
        fmt.Printf("Trabajo %d → %d\n", r.TrabajoID, r.Valor)
    }
}
```

### Patrón Pipeline

```go
package main

import "fmt"

func generar(nums ...int) <-chan int {
    out := make(chan int)
    go func() {
        defer close(out)
        for _, n := range nums {
            out <- n
        }
    }()
    return out
}

func cuadrado(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        defer close(out)
        for n := range in {
            out <- n * n
        }
    }()
    return out
}

func filtrarPares(in <-chan int) <-chan int {
    out := make(chan int)
    go func() {
        defer close(out)
        for n := range in {
            if n%2 == 0 {
                out <- n
            }
        }
    }()
    return out
}

func main() {
    // Pipeline: generar → cuadrado → filtrar pares
    nums := generar(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    cuadrados := cuadrado(nums)
    pares := filtrarPares(cuadrados)

    for v := range pares {
        fmt.Print(v, " ") // 4 16 36 64 100
    }
    fmt.Println()
}
```

### Patrón Singleton

```go
package config

import (
    "sync"
)

type Configuracion struct {
    BBDD     string
    Puerto   int
    Debug    bool
}

var (
    instancia *Configuracion
    once      sync.Once
)

func ObtenerConfig() *Configuracion {
    once.Do(func() {
        instancia = &Configuracion{
            BBDD:   "localhost:5432",
            Puerto: 8080,
            Debug:  false,
        }
    })
    return instancia
}
```

---

## 17. Nivel Experto — Optimización y perfilado

### Buenas prácticas de rendimiento

```go
package main

import (
    "bytes"
    "fmt"
    "strings"
)

// ❌ MAL: concatenación en bucle crea muchos strings
func concatenarMal(palabras []string) string {
    resultado := ""
    for _, p := range palabras {
        resultado += p + " "
    }
    return resultado
}

// ✅ BIEN: strings.Builder evita allocations innecesarias
func concatenarBien(palabras []string) string {
    var sb strings.Builder
    for _, p := range palabras {
        sb.WriteString(p)
        sb.WriteByte(' ')
    }
    return sb.String()
}

// ❌ MAL: slice sin capacidad inicial — re-allocaciones frecuentes
func sliceMal(n int) []int {
    var s []int
    for i := 0; i < n; i++ {
        s = append(s, i)
    }
    return s
}

// ✅ BIEN: pre-asignar con capacidad conocida
func sliceBien(n int) []int {
    s := make([]int, 0, n)
    for i := 0; i < n; i++ {
        s = append(s, i)
    }
    return s
}

// Reutilizar buffers con sync.Pool
import "sync"

var bufPool = sync.Pool{
    New: func() interface{} {
        return new(bytes.Buffer)
    },
}

func procesarConPool(datos string) string {
    buf := bufPool.Get().(*bytes.Buffer)
    defer func() {
        buf.Reset()
        bufPool.Put(buf)
    }()

    buf.WriteString("procesado: ")
    buf.WriteString(datos)
    return buf.String()
}

func main() {
    palabras := []string{"Go", "es", "rápido", "y", "eficiente"}
    fmt.Println(concatenarBien(palabras))
    fmt.Println(procesarConPool("datos importantes"))
}
```

### Perfilado (profiling)

```go
// Agregar al main para perfilado de CPU
package main

import (
    "os"
    "runtime/pprof"
    "log"
)

func main() {
    // CPU profiling
    f, err := os.Create("cpu.prof")
    if err != nil {
        log.Fatal(err)
    }
    defer f.Close()
    pprof.StartCPUProfile(f)
    defer pprof.StopCPUProfile()

    // ... tu código aquí ...

    // Memory profiling
    mf, err := os.Create("mem.prof")
    if err != nil {
        log.Fatal(err)
    }
    defer mf.Close()
    pprof.WriteHeapProfile(mf)
}
```

```bash
# Analizar perfil de CPU
go tool pprof cpu.prof
# Comandos en pprof:
# top        — funciones con más tiempo de CPU
# list func  — ver código de una función
# web        — visualización en navegador (requiere graphviz)

# HTTP profiling en aplicaciones en ejecución
import _ "net/http/pprof"
# Luego: go tool pprof http://localhost:8080/debug/pprof/heap
```

---

## 18. Nivel Experto — CLI, módulos y tooling

### Crear una CLI robusta

```go
// cmd/root.go
package main

import (
    "flag"
    "fmt"
    "os"
)

type Config struct {
    Verbose bool
    Output  string
    Puerto  int
}

func main() {
    cfg := Config{}

    // Subcomandos
    servirCmd := flag.NewFlagSet("servir", flag.ExitOnError)
    servirPuerto := servirCmd.Int("puerto", 8080, "Puerto del servidor")
    servirVerbose := servirCmd.Bool("v", false, "Modo verbose")

    calcCmd := flag.NewFlagSet("calc", flag.ExitOnError)
    calcOp := calcCmd.String("op", "suma", "Operación: suma, resta, mult, div")

    if len(os.Args) < 2 {
        fmt.Println("Uso: programa <comando> [opciones]")
        fmt.Println("  servir  — inicia el servidor")
        fmt.Println("  calc    — realiza cálculos")
        os.Exit(1)
    }

    switch os.Args[1] {
    case "servir":
        servirCmd.Parse(os.Args[2:])
        cfg.Puerto = *servirPuerto
        cfg.Verbose = *servirVerbose
        fmt.Printf("Iniciando servidor en :%d (verbose=%v)\n", cfg.Puerto, cfg.Verbose)

    case "calc":
        calcCmd.Parse(os.Args[2:])
        args := calcCmd.Args()
        if len(args) < 2 {
            fmt.Fprintln(os.Stderr, "Se necesitan dos números")
            os.Exit(1)
        }
        fmt.Printf("Operación %s sobre %v\n", *calcOp, args)

    default:
        fmt.Fprintf(os.Stderr, "Comando desconocido: %q\n", os.Args[1])
        os.Exit(1)
    }
}
```

### Gestión de módulos Go

```bash
# Inicializar módulo
go mod init github.com/usuario/proyecto

# Agregar dependencia
go get github.com/gin-gonic/gin@latest

# Actualizar dependencias
go get -u ./...

# Limpiar dependencias no usadas
go mod tidy

# Ver árbol de dependencias
go mod graph

# Vendor — copiar dependencias localmente
go mod vendor

# Verificar integridad
go mod verify
```

### Herramientas esenciales del ecosistema Go

```bash
# Formateo de código (¡siempre antes de commit!)
gofmt -w .
goimports -w .   # también ordena imports

# Linting
go vet ./...                    # detector de errores del compilador
golangci-lint run               # linter completo (requiere instalación)

# Seguridad
govulncheck ./...               # busca vulnerabilidades conocidas

# Generación de código
go generate ./...               # ejecuta directivas //go:generate

# Documentación
go doc fmt.Println              # documentación de una función
godoc -http=:6060               # servidor de documentación local

# Build para múltiples plataformas
GOOS=linux GOARCH=amd64 go build -o app-linux
GOOS=windows GOARCH=amd64 go build -o app.exe
GOOS=darwin GOARCH=arm64 go build -o app-mac-m1
```

---

## 19. Proyecto completo: API REST con Go

Una API de gestión de tareas con autenticación JWT, base de datos SQLite y estructura en capas.

### Estructura del proyecto

```
tareas-api/
├── cmd/
│   └── main.go
├── internal/
│   ├── config/
│   │   └── config.go
│   ├── domain/
│   │   └── tarea.go
│   ├── repository/
│   │   └── sqlite_repo.go
│   ├── service/
│   │   └── tarea_service.go
│   └── handler/
│       ├── tarea_handler.go
│       └── middleware.go
├── go.mod
└── go.sum
```

```go
// internal/domain/tarea.go
package domain

import (
    "errors"
    "time"
)

type Prioridad string

const (
    Baja  Prioridad = "baja"
    Media Prioridad = "media"
    Alta  Prioridad = "alta"
)

type Tarea struct {
    ID         int       `json:"id"`
    Titulo     string    `json:"titulo"`
    Descripcion string   `json:"descripcion,omitempty"`
    Completada bool      `json:"completada"`
    Prioridad  Prioridad `json:"prioridad"`
    CreadaEn   time.Time `json:"creada_en"`
    ActualEn   time.Time `json:"actualizada_en"`
}

var (
    ErrTareaNoEncontrada   = errors.New("tarea no encontrada")
    ErrTituloObligatorio   = errors.New("el título es obligatorio")
    ErrPrioridadInvalida   = errors.New("prioridad inválida: use baja, media o alta")
)

func (t *Tarea) Validar() error {
    if t.Titulo == "" {
        return ErrTituloObligatorio
    }
    switch t.Prioridad {
    case Baja, Media, Alta:
    case "":
        t.Prioridad = Media // valor por defecto
    default:
        return ErrPrioridadInvalida
    }
    return nil
}

type Repositorio interface {
    ObtenerTodas() ([]Tarea, error)
    ObtenerPorID(id int) (Tarea, error)
    Crear(t Tarea) (Tarea, error)
    Actualizar(t Tarea) (Tarea, error)
    Eliminar(id int) error
}
```

```go
// internal/service/tarea_service.go
package service

import (
    "fmt"
    "time"
    "github.com/usuario/tareas-api/internal/domain"
)

type TareaService struct {
    repo domain.Repositorio
}

func NuevoTareaService(repo domain.Repositorio) *TareaService {
    return &TareaService{repo: repo}
}

func (s *TareaService) ListarTareas() ([]domain.Tarea, error) {
    return s.repo.ObtenerTodas()
}

func (s *TareaService) ObtenerTarea(id int) (domain.Tarea, error) {
    return s.repo.ObtenerPorID(id)
}

func (s *TareaService) CrearTarea(t domain.Tarea) (domain.Tarea, error) {
    if err := t.Validar(); err != nil {
        return domain.Tarea{}, fmt.Errorf("validación: %w", err)
    }
    t.CreadaEn = time.Now()
    t.ActualEn = t.CreadaEn
    return s.repo.Crear(t)
}

func (s *TareaService) ActualizarTarea(t domain.Tarea) (domain.Tarea, error) {
    if err := t.Validar(); err != nil {
        return domain.Tarea{}, fmt.Errorf("validación: %w", err)
    }
    t.ActualEn = time.Now()
    return s.repo.Actualizar(t)
}

func (s *TareaService) EliminarTarea(id int) error {
    return s.repo.Eliminar(id)
}
```

```go
// internal/handler/middleware.go
package handler

import (
    "log"
    "net/http"
    "time"
)

func MiddlewareLog(siguiente http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        inicio := time.Now()
        siguiente.ServeHTTP(w, r)
        log.Printf("%s %s — %v", r.Method, r.URL.Path, time.Since(inicio))
    })
}

func MiddlewareCORS(siguiente http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        w.Header().Set("Access-Control-Allow-Origin", "*")
        w.Header().Set("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS")
        w.Header().Set("Access-Control-Allow-Headers", "Content-Type, Authorization")
        if r.Method == http.MethodOptions {
            w.WriteHeader(http.StatusNoContent)
            return
        }
        siguiente.ServeHTTP(w, r)
    })
}
```

```go
// cmd/main.go
package main

import (
    "log"
    "net/http"
)

func main() {
    mux := http.NewServeMux()
    // Registrar rutas, middleware, etc.

    handler := MiddlewareLog(MiddlewareCORS(mux))

    servidor := &http.Server{
        Addr:    ":8080",
        Handler: handler,
    }

    log.Println("API de Tareas escuchando en :8080")
    if err := servidor.ListenAndServe(); err != nil {
        log.Fatal(err)
    }
}
```

```bash
# Inicializar y ejecutar
go mod init github.com/usuario/tareas-api
go mod tidy
go run cmd/main.go

# Probar endpoints
curl -X POST http://localhost:8080/api/v1/tareas \
  -H "Content-Type: application/json" \
  -d '{"titulo":"Estudiar Go","prioridad":"alta"}'

curl http://localhost:8080/api/v1/tareas
curl http://localhost:8080/api/v1/tareas/1

curl -X PUT http://localhost:8080/api/v1/tareas/1 \
  -H "Content-Type: application/json" \
  -d '{"titulo":"Estudiar Go","completada":true,"prioridad":"alta"}'

curl -X DELETE http://localhost:8080/api/v1/tareas/1
```

---

## 20. Ejercicios prácticos resueltos

### Ejercicio 4 — FizzBuzz

```go
package main

import "fmt"

func fizzBuzz(n int) string {
    switch {
    case n%15 == 0:
        return "FizzBuzz"
    case n%3 == 0:
        return "Fizz"
    case n%5 == 0:
        return "Buzz"
    default:
        return fmt.Sprintf("%d", n)
    }
}

func main() {
    for i := 1; i <= 30; i++ {
        fmt.Println(fizzBuzz(i))
    }
}
```

### Ejercicio 5 — Fibonacci con memoización

```go
package main

import "fmt"

func fibMemo() func(int) int {
    cache := map[int]int{0: 0, 1: 1}
    var fib func(int) int
    fib = func(n int) int {
        if v, ok := cache[n]; ok {
            return v
        }
        cache[n] = fib(n-1) + fib(n-2)
        return cache[n]
    }
    return fib
}

func main() {
    fib := fibMemo()
    for i := 0; i <= 15; i++ {
        fmt.Printf("fib(%d) = %d\n", i, fib(i))
    }
}
```

### Ejercicio 6 — Invertir cadena Unicode

```go
package main

import "fmt"

func invertir(s string) string {
    runas := []rune(s) // Convertir a runes para manejar Unicode correctamente
    for i, j := 0, len(runas)-1; i < j; i, j = i+1, j-1 {
        runas[i], runas[j] = runas[j], runas[i]
    }
    return string(runas)
}

func main() {
    pruebas := []string{"Hola", "Go 🐹", "résumé", "日本語"}
    for _, s := range pruebas {
        fmt.Printf("%-15q → %q\n", s, invertir(s))
    }
}
```

### Ejercicio 7 — Árbol binario de búsqueda

```go
package main

import "fmt"

type Nodo struct {
    Valor       int
    Izquierdo   *Nodo
    Derecho     *Nodo
}

func (n *Nodo) Insertar(valor int) *Nodo {
    if n == nil {
        return &Nodo{Valor: valor}
    }
    if valor < n.Valor {
        n.Izquierdo = n.Izquierdo.Insertar(valor)
    } else if valor > n.Valor {
        n.Derecho = n.Derecho.Insertar(valor)
    }
    return n
}

func (n *Nodo) Buscar(valor int) bool {
    if n == nil {
        return false
    }
    if valor == n.Valor {
        return true
    }
    if valor < n.Valor {
        return n.Izquierdo.Buscar(valor)
    }
    return n.Derecho.Buscar(valor)
}

// InOrden — devuelve los valores en orden ascendente
func (n *Nodo) InOrden() []int {
    if n == nil {
        return nil
    }
    var resultado []int
    resultado = append(resultado, n.Izquierdo.InOrden()...)
    resultado = append(resultado, n.Valor)
    resultado = append(resultado, n.Derecho.InOrden()...)
    return resultado
}

func main() {
    var raiz *Nodo
    for _, v := range []int{5, 3, 7, 1, 4, 6, 8} {
        raiz = raiz.Insertar(v)
    }

    fmt.Println("InOrden:", raiz.InOrden()) // [1 3 4 5 6 7 8]
    fmt.Println("Buscar 4:", raiz.Buscar(4)) // true
    fmt.Println("Buscar 9:", raiz.Buscar(9)) // false
}
```

### Ejercicio 8 — Servidor de chat con goroutines y channels

```go
package main

import (
    "bufio"
    "fmt"
    "net"
    "strings"
)

type Cliente struct {
    conn   net.Conn
    nombre string
    msgs   chan string
}

type Chat struct {
    clientes  map[*Cliente]bool
    broadcast chan string
    unirse    chan *Cliente
    salir     chan *Cliente
}

func NuevoChat() *Chat {
    return &Chat{
        clientes:  make(map[*Cliente]bool),
        broadcast: make(chan string, 100),
        unirse:    make(chan *Cliente),
        salir:     make(chan *Cliente),
    }
}

func (c *Chat) Ejecutar() {
    for {
        select {
        case cliente := <-c.unirse:
            c.clientes[cliente] = true
            c.broadcast <- fmt.Sprintf("→ %s se unió al chat", cliente.nombre)

        case cliente := <-c.salir:
            if _, ok := c.clientes[cliente]; ok {
                delete(c.clientes, cliente)
                close(cliente.msgs)
                c.broadcast <- fmt.Sprintf("← %s abandonó el chat", cliente.nombre)
            }

        case msg := <-c.broadcast:
            for cliente := range c.clientes {
                select {
                case cliente.msgs <- msg:
                default:
                    // Si el canal está lleno, ignorar este mensaje para este cliente
                }
            }
        }
    }
}

func (c *Chat) ManejarCliente(conn net.Conn) {
    scanner := bufio.NewScanner(conn)
    fmt.Fprint(conn, "Tu nombre: ")
    scanner.Scan()
    nombre := strings.TrimSpace(scanner.Text())

    cliente := &Cliente{
        conn:   conn,
        nombre: nombre,
        msgs:   make(chan string, 10),
    }
    c.unirse <- cliente

    // Goroutine para escribir mensajes al cliente
    go func() {
        for msg := range cliente.msgs {
            fmt.Fprintln(conn, msg)
        }
    }()

    // Leer mensajes del cliente
    for scanner.Scan() {
        texto := strings.TrimSpace(scanner.Text())
        if texto == "/salir" {
            break
        }
        c.broadcast <- fmt.Sprintf("[%s]: %s", nombre, texto)
    }

    c.salir <- cliente
    conn.Close()
}

func main() {
    chat := NuevoChat()
    go chat.Ejecutar()

    ln, err := net.Listen("tcp", ":9000")
    if err != nil {
        fmt.Println("Error:", err)
        return
    }
    defer ln.Close()
    fmt.Println("Servidor de chat en :9000 — conecta con: telnet localhost 9000")

    for {
        conn, err := ln.Accept()
        if err != nil {
            continue
        }
        go chat.ManejarCliente(conn)
    }
}
```

```bash
# Probar el servidor de chat (en terminales separadas)
telnet localhost 9000
```

### Ejercicio 9 — Lector de CSV concurrente

```go
package main

import (
    "encoding/csv"
    "fmt"
    "os"
    "strconv"
    "sync"
)

type Venta struct {
    Producto string
    Cantidad int
    Precio   float64
}

func leerCSV(ruta string) ([]Venta, error) {
    f, err := os.Open(ruta)
    if err != nil {
        return nil, fmt.Errorf("abrir %s: %w", ruta, err)
    }
    defer f.Close()

    r := csv.NewReader(f)
    registros, err := r.ReadAll()
    if err != nil {
        return nil, err
    }

    ventas := make([]Venta, 0, len(registros)-1)
    for _, reg := range registros[1:] { // saltar cabecera
        cant, _ := strconv.Atoi(reg[1])
        precio, _ := strconv.ParseFloat(reg[2], 64)
        ventas = append(ventas, Venta{
            Producto: reg[0],
            Cantidad: cant,
            Precio:   precio,
        })
    }
    return ventas, nil
}

func procesarVentas(archivos []string) map[string]float64 {
    var mu sync.Mutex
    totales := make(map[string]float64)
    var wg sync.WaitGroup

    for _, archivo := range archivos {
        wg.Add(1)
        go func(ruta string) {
            defer wg.Done()
            ventas, err := leerCSV(ruta)
            if err != nil {
                fmt.Println("Error:", err)
                return
            }
            for _, v := range ventas {
                mu.Lock()
                totales[v.Producto] += float64(v.Cantidad) * v.Precio
                mu.Unlock()
            }
        }(archivo)
    }

    wg.Wait()
    return totales
}

func main() {
    // Crear archivos CSV de prueba
    datos := []struct{ nombre, contenido string }{
        {"ventas1.csv", "producto,cantidad,precio\nManzana,10,0.5\nPera,5,0.8\n"},
        {"ventas2.csv", "producto,cantidad,precio\nManzana,20,0.5\nNaranja,15,0.6\n"},
    }
    for _, d := range datos {
        os.WriteFile("/tmp/"+d.nombre, []byte(d.contenido), 0644)
    }

    archivos := []string{"/tmp/ventas1.csv", "/tmp/ventas2.csv"}
    totales := procesarVentas(archivos)

    for producto, total := range totales {
        fmt.Printf("%-10s: %.2f€\n", producto, total)
    }
}
```

---

## 21. Recursos y próximos pasos

### Documentación oficial

- 🌐 [go.dev](https://go.dev/) — sitio oficial de Go
- 📖 [Tour de Go](https://go.dev/tour/) — tutorial interactivo oficial
- 📚 [Especificación del lenguaje](https://go.dev/ref/spec)
- 📦 [pkg.go.dev](https://pkg.go.dev/) — documentación de paquetes

### Libros recomendados

| Título | Nivel | Descripción |
|---|---|---|
| *The Go Programming Language* | Básico-Intermedio | La biblia de Go (Donovan & Kernighan) |
| *Go in Action* | Intermedio | Enfoque práctico con proyectos reales |
| *Concurrency in Go* | Avanzado | Patrones de concurrencia en profundidad |
| *100 Go Mistakes* | Experto | Los 100 errores más comunes y cómo evitarlos |

### Frameworks y librerías populares

| Categoría | Librería | Descripción |
|---|---|---|
| **Web** | [Gin](https://gin-gonic.com/) | Framework HTTP rápido y minimalista |
| **Web** | [Echo](https://echo.labstack.com/) | Framework HTTP de alto rendimiento |
| **Web** | [Fiber](https://gofiber.io/) | Inspirado en Express.js |
| **ORM** | [GORM](https://gorm.io/) | ORM completo para Go |
| **CLI** | [Cobra](https://github.com/spf13/cobra) | Framework para CLIs (usado por kubectl) |
| **Config** | [Viper](https://github.com/spf13/viper) | Gestión de configuración |
| **Logging** | [Zap](https://github.com/uber-go/zap) | Logger de alto rendimiento (Uber) |
| **Testing** | [Testify](https://github.com/stretchr/testify) | Aserciones y mocks para tests |
| **gRPC** | [grpc-go](https://grpc.io/docs/languages/go/) | RPC de alto rendimiento |

### Ruta de aprendizaje sugerida

```
Básico (1-2 semanas)
  ├── Variables, tipos, operadores
  ├── Control de flujo
  ├── Funciones y paquetes
  └── Arrays, Slices, Maps

Intermedio (2-4 semanas)
  ├── Structs y métodos
  ├── Interfaces
  ├── Gestión de errores
  ├── Punteros
  └── Goroutines básicas

Avanzado (1-2 meses)
  ├── Channels y patrones de concurrencia
  ├── Context
  ├── Generics
  ├── Testing avanzado
  └── HTTP y APIs REST

Experto (continuo)
  ├── Patrones de diseño
  ├── Optimización y profiling
  ├── Arquitectura de microservicios
  └── Proyectos de producción
```

### Práctica adicional

- 🏋️ [Exercism — Go Track](https://exercism.org/tracks/go) — ejercicios con mentores
- 🔰 [Go by Example](https://gobyexample.com/) — ejemplos de referencia rápida
- 🏆 [LeetCode](https://leetcode.com/) — algoritmos en Go
- 🚀 [Awesome Go](https://awesome-go.com/) — catálogo de librerías y recursos

---

> 💡 **Consejo final**: La mejor manera de aprender Go es escribiendo código. Empieza con un proyecto pequeño que resuelva un problema real tuyo: una CLI, un scraper, una API sencilla. El ecosistema de Go es excelente y la comunidad es muy activa. ¡Mucho ánimo! 🐹
