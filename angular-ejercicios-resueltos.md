# Ejercicios resueltos de Angular (Temas 1–15)

Este documento contiene ejercicios resueltos correspondientes a los primeros 15 temas del tutorial "Tutorial completo de Angular (Principiante a Intermedio)". Cada sección incluye: enunciado, solución y una breve explicación, con enlaces a la sección del tutorial.

Índice
- [1. Prerrequisitos](#1-prerrequisitos)
- [2. Introducción a Angular](#2-introducción-a-angular)
- [3. Preparación del entorno](#3-preparación-del-entorno)
- [4. Angular CLI y estructura](#4-angular-cli-y-estructura)
- [5. TypeScript esencial](#5-typescript-esencial)
- [6. Componentes y plantillas](#6-componentes-y-plantillas)
- [7. Data binding y eventos](#7-data-binding-y-eventos)
- [8. Directivas](#8-directivas)
- [9. Pipes](#9-pipes)
- [10. Servicios e inyección de dependencias](#10-servicios-e-inyección-de-dependencias)
- [11. HTTPClient y APIs](#11-httpclient-y-apis)
- [12. Routing básico](#12-routing-básico)
- [13. Formularios](#13-formularios)
- [14. RxJS y patrones reactivos](#14-rxjs-y-patrones-reactivos)
- [15. Interceptores, Guards y Resolvers](#15-interceptores-guards-y-resolvers)

---

> Referencia: consulta el tutorial principal para contexto detallado: [Tutorial completo de Angular](./angular-tutorial.md).

## 1. Prerrequisitos
Enunciado: Verifica tu entorno de Node y Angular CLI.
Solución:
```bash
node -v
npm -v
ng version
```
Explicación: Asegura versiones compatibles antes de iniciar.

## 2. Introducción a Angular
Enunciado: Lista 3 casos donde Angular es adecuado.
Solución: Apps empresariales, dashboards complejos, formularios avanzados.
Explicación: Angular aporta estructura y herramientas integradas.

## 3. Preparación del entorno
Enunciado: Instala Angular CLI.
Solución:
```bash
npm install -g @angular/cli
```
Explicación: La CLI acelera la creación y gestión de proyectos.

## 4. Angular CLI y estructura
Enunciado: Crea un proyecto con routing y SCSS.
Solución:
```bash
ng new mi-app --routing --style=scss
cd mi-app && ng serve -o
```
Explicación: La CLI genera el esqueleto y el servidor de dev.

## 5. TypeScript esencial
Enunciado: Define una interfaz `Usuario` y úsala en una función.
Solución:
```typescript
interface Usuario { id: number; nombre: string; activo?: boolean; }
function imprimirUsuario(u: Usuario) { console.log(`${u.id} - ${u.nombre}`); }
```
Explicación: Tipar datos evita errores de tiempo de ejecución.

## 6. Componentes y plantillas
Enunciado: Crea un componente contador con dos botones.
Solución (resumen): ver ejemplo del tutorial.
Explicación: El componente maneja estado local y eventos.

## 7. Data binding y eventos
Enunciado: Implementa input enlazado y muestra el texto.
Solución:
```html
<input [value]="nombre" (input)="nombre = $event.target.value">
<p>{{ nombre }}</p>
```
Explicación: Combina property y event binding.

## 8. Directivas
Enunciado: Lista con *ngFor y mensaje cuando esté vacía.
Solución:
```html
<div *ngIf="items.length === 0">No hay elementos</div>
<ul>
  <li *ngFor="let item of items; index as i">{{ i }} - {{ item }}</li>
</ul>
```
Explicación: `*ngIf` y `*ngFor` controlan estructura.

## 9. Pipes
Enunciado: Muestra una fecha corta y un monto en EUR.
Solución:
```html
<p>{{ fecha | date:'short' }}</p>
<p>{{ monto | currency:'EUR' }}</p>
```
Explicación: Pipes transforman valores en la vista.

## 10. Servicios e inyección de dependencias
Enunciado: Crea `UsuariosService` con listar/agregar.
Solución (resumen): ver servicio en el tutorial.
Explicación: Separar lógica de negocio del componente.

## 11. HTTPClient y APIs
Enunciado: Crea un servicio CRUD contra `jsonplaceholder`.
Solución (resumen): ver `TodoApi` en el tutorial.
Explicación: `HttpClient` maneja solicitudes y respuestas tipadas.

## 12. Routing básico
Enunciado: Define rutas para Home y Detalle con parámetro `id`.
Solución:
```typescript
export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'detalle/:id', component: DetalleComponent },
  { path: '**', redirectTo: '' }
];
```
Explicación: Rutas y parámetros navegan entre vistas.

## 13. Formularios
Enunciado: Formulario reactivo con validación de nombre y email.
Solución (resumen): ver ejemplo de Reactive Forms.
Explicación: Validaciones declarativas y estado del formulario.

## 14. RxJS y patrones reactivos
Enunciado: Búsqueda con `Subject` y `debounceTime`.
Solución:
```typescript
searchTerm = new Subject<string>();
results$ = this.searchTerm.pipe(
  debounceTime(300),
  switchMap(term => this.api.buscar(term))
);
```
Explicación: Evita solicitudes excesivas y compone flujos.

## 15. Interceptores, Guards y Resolvers
Enunciado: Interceptor que agrega Authorization.
Solución:
```typescript
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    const authReq = req.clone({ setHeaders: { Authorization: 'Bearer token' } });
    return next.handle(authReq);
  }
}
```
Explicación: Interceptores modifican solicitudes globalmente.

---

Consejo: Consulta las secciones correspondientes del [tutorial](./angular-tutorial.md) para ampliar cada ejercicio.
