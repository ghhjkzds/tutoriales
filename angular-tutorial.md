# Tutorial completo de Angular (Principiante a Intermedio)

A continuación encontrarás un tutorial completo de Angular, pensado para personas de nivel principiante a intermedio. Está organizado en módulos progresivos con explicaciones, ejemplos, ejercicios y buenas prácticas. Puedes seguirlo desde cero y avanzar por secciones. Al final se incluye un mini-proyecto guiado y recomendaciones para testing, rendimiento y despliegue.

## Índice
- Prerrequisitos
- Introducción a Angular
- Preparación del entorno
- Angular CLI y estructura de un proyecto
- TypeScript para Angular (lo esencial)
- Componentes y plantillas
- Data binding y eventos
- Directivas estructurales y de atributo
- Pipes
- Servicios, inyección de dependencias y separación de responsabilidades
- HTTPClient y consumo de APIs
- Routing: navegación, parámetros y lazy loading
- Formularios: Template-driven y Reactive Forms
- RxJS y patrones reactivos
- Interceptores, Guards y resolvers
- Módulos vs Standalone Components (Angular moderno)
- Estado y comunicación entre componentes
- Testing básico (unitario y de integración)
- Estilos, theming y librerías UI
- Performance y buenas prácticas
- Internacionalización (i18n) básica
- Despliegue (Vercel, Netlify, Firebase, GitHub Pages)
- Mini-proyecto: “Lista de Tareas con API”
- Recursos y próximos pasos

---

## Prerrequisitos
- Conocimientos básicos de HTML y CSS.
- Conocimientos básicos de JavaScript.
- Familiaridad mínima con el terminal/console.
- Opcional pero recomendado: fundamentos de TypeScript (tipos, interfaces, clases).

## Introducción a Angular
- ¿Qué es Angular?: Un framework frontend de Google para crear aplicaciones web escalables basadas en componentes y arquitectura reactiva.
- Filosofía: opinionado, con herramientas integradas (routing, formularios, HTTP, i18n, testing), fuerte soporte a TypeScript.
- Cuándo usarlo: apps empresariales, dashboards complejos, formularios avanzados, proyectos que requieren convenciones sólidas.

## Preparación del entorno
1) Instalar Node.js (LTS): desde nodejs.org.
2) Instalar Angular CLI:
   ```bash
   npm install -g @angular/cli
   ```
3) Verificar:
   ```bash
   node -v
   ng version
   ```

## Angular CLI y estructura de un proyecto
- Crear un proyecto:
  ```bash
  ng new mi-app --routing --style=scss
  ```
  Opciones:
  - `--routing`: añade el módulo de routing.
  - `--style=scss`: usa SCSS (puedes elegir css/sass/less).
- Ejecutar servidor de desarrollo:
  ```bash
  cd mi-app
  ng serve -o
  ```
- Estructura típica:
  - `src/`
    - `app/`
      - `app.component.ts|html|scss`
      - `app.routes.ts` o `app-routing.module.ts` (según standalone o módulos)
      - `features/` (tus funcionalidades)
      - `shared/` (componentes/pipes/servicios reutilizables)
    - `assets/` (imágenes, i18n, etc.)
    - `environments/` (variables por entorno)
  - `angular.json` (configuración del build)
  - `tsconfig.json` (TypeScript)
  - `package.json` (dependencias)

## TypeScript para Angular (lo esencial)
- Tipos: `string`, `number`, `boolean`, `any`, `unknown`.
- Interfaces:
  ```typescript
  interface Usuario { id: number; nombre: string; activo?: boolean; }
  ```
- Clases y decoradores (Angular usa decoradores como `@Component`).
- Generics y utilidades comunes.
- Tipos estrictos: habilítalos para detectar errores temprano.

## Componentes y plantillas
- Crear componente:
  ```bash
  ng generate component shared/header
  ```
- Estructura:
  - `.ts`: clase y metadatos `@Component`
  - `.html`: plantilla
  - `.scss`: estilos del componente
- Ejemplo:
  ```typescript
  import { Component } from '@angular/core';

  @Component({
    selector: 'app-contador',
    templateUrl: './contador.component.html',
    styleUrls: ['./contador.component.scss']
  })
  export class ContadorComponent {
    valor = 0;
    incrementar() { this.valor++; }
    decrementar() { this.valor--; }
  }
  ```
  ```html
  <div class="contador">
    <h2>{{ valor }}</h2>
    <button (click)="decrementar()">-</button>
    <button (click)="incrementar()">+</button>
  </div>
  ```

## Data binding y eventos
- Interpolación: `{{ variable }}`
- Property binding: `[prop]="valor"`
- Event binding: `(event)="handler($event)"`
- Two-way binding: `[(ngModel)]="modelo"` (requiere `FormsModule` en template-driven)
- Ejemplo:
  ```html
  <input [value]="nombre" (input)="nombre = $event.target.value">
  <p>Hola, {{ nombre }}</p>
  ```

## Directivas estructurales y de atributo
- Estructurales: `*ngIf`, `*ngFor`, `*ngSwitch`
  ```html
  <div *ngIf="items.length === 0">No hay elementos</div>
  <ul>
    <li *ngFor="let item of items; index as i">{{ i }} - {{ item }}</li>
  </ul>
  ```
- Atributo: `[ngClass]`, `[ngStyle]`
  ```html
  <div [ngClass]="{ activo: isActive, error: hasError }">Estado</div>
  ```
- Directiva personalizada (nivel intermedio):
  ```typescript
  import { Directive, ElementRef, Input } from '@angular/core';

  @Directive({ selector: '[appResaltar]' })
  export class ResaltarDirective {
    @Input('appResaltar') color = 'yellow';
    constructor(private el: ElementRef) {}
    ngOnChanges() {
      this.el.nativeElement.style.backgroundColor = this.color;
    }
  }
  ```

## Pipes
- Transforman valores en plantillas.
- Built-in: `date`, `currency`, `uppercase`, `lowercase`, `json`, `async`.
  ```html
  <p>{{ fecha | date:'short' }}</p>
  <p>{{ monto | currency:'EUR' }}</p>
  ```
- Pipe personalizado:
  ```typescript
  import { Pipe, PipeTransform } from '@angular/core';

  @Pipe({ name: 'titulo' })
  export class TituloPipe implements PipeTransform {
    transform(valor: string): string {
      return valor?.trim().toUpperCase() ?? '';
    }
  }
  ```

## Servicios, inyección de dependencias y separación de responsabilidades
- Crear servicio:
  ```bash
  ng generate service core/usuarios
  ```
- Proveer servicios: `providedIn: 'root'` (singleton global) o en módulos/standalone.
- Uso:
  ```typescript
  import { Injectable } from '@angular/core';

  @Injectable({ providedIn: 'root' })
  export class UsuariosService {
    private usuarios = [{ id:1, nombre:'Ana' }];

    listar() { return this.usuarios; }
    agregar(usuario: {id:number; nombre:string}) { this.usuarios.push(usuario); }
  }
  ```
  ```typescript
  export class ListaUsuariosComponent {
    usuarios = this.usuariosService.listar();
    constructor(private usuariosService: UsuariosService) {}
  }
  ```

## HTTPClient y consumo de APIs
- Importar `HttpClientModule` en `app.module.ts` o en `main.ts` si usas standalone.
- Servicio de API:
  ```typescript
  import { HttpClient } from '@angular/common/http';
  import { Injectable } from '@angular/core';
  import { Observable } from 'rxjs';

  export interface Todo { id: number; title: string; completed: boolean; }

  @Injectable({ providedIn: 'root' })
  export class TodoApi {
    private baseUrl = 'https://jsonplaceholder.typicode.com/todos';
    constructor(private http: HttpClient) {}

    listar(): Observable<Todo[]> { return this.http.get<Todo[]>(this.baseUrl); }
    obtener(id: number): Observable<Todo> { return this.http.get<Todo>(`${this.baseUrl}/${id}`); }
    crear(todo: Partial<Todo>): Observable<Todo> { return this.http.post<Todo>(this.baseUrl, todo); }
    actualizar(id: number, todo: Partial<Todo>): Observable<Todo> { return this.http.put<Todo>(`${this.baseUrl}/${id}`, todo); }
    eliminar(id: number): Observable<void> { return this.http.delete<void>(`${this.baseUrl}/${id}`); }
  }
  ```

## Routing: navegación, parámetros y lazy loading
- Configuración básica:
  ```typescript
  import { Routes } from '@angular/router';
  import { HomeComponent } from './features/home/home.component';
  import { DetalleComponent } from './features/detalle/detalle.component';

  export const routes: Routes = [
    { path: '', component: HomeComponent },
    { path: 'detalle/:id', component: DetalleComponent },
    { path: '**', redirectTo: '' }
  ];
  ```
- Navegación:
  ```html
  <a routerLink="/">Inicio</a>
  <a [routerLink]="['/detalle', item.id]">Ver detalle</a>
  <router-outlet></router-outlet>
  ```
- Parámetros:
  ```typescript
  import { ActivatedRoute } from '@angular/router';

  constructor(private route: ActivatedRoute) {
    this.route.params.subscribe(p => console.log(p['id']));
  }
  ```
- Lazy loading (módulos):
  ```typescript
  const routes: Routes = [
    { path: 'admin', loadChildren: () => import('./features/admin/admin.module').then(m => m.AdminModule) }
  ];
  ```
- Lazy con standalone:
  ```typescript
  { path: 'admin', loadComponent: () => import('./features/admin/admin.component').then(c => c.AdminComponent) }
  ```

## Formularios
- Template-driven (rápido, menos escalable):
  - Importa `FormsModule`.
  ```html
  <form #f="ngForm" (ngSubmit)="guardar(f.value)">
    <input name="nombre" [(ngModel)]="modelo.nombre" required />
    <button [disabled]="f.invalid">Guardar</button>
  </form>
  ```
- Reactive Forms (recomendado para intermedio):
  - Importa `ReactiveFormsModule`.
  ```typescript
  import { FormBuilder, Validators } from '@angular/forms';

  export class UsuarioFormComponent {
    form = this.fb.group({
      nombre: ['', [Validators.required, Validators.minLength(3)]],
      email: ['', [Validators.required, Validators.email]],
      roles: this.fb.array([])
    });
    constructor(private fb: FormBuilder) {}

    guardar() {
      if (this.form.invalid) return;
      console.log(this.form.value);
    }
  }
  ```
  ```html
  <form [formGroup]="form" (ngSubmit)="guardar()">
    <input formControlName="nombre" />
    <div *ngIf="form.get('nombre')?.invalid && form.get('nombre')?.touched">Nombre inválido</div>
    <input formControlName="email" />
    <button [disabled]="form.invalid">Guardar</button>
  </form>
  ```

## RxJS y patrones reactivos
- Observables: flujo de datos asíncronos.
- Operadores comunes: `map`, `switchMap`, `filter`, `tap`, `catchError`, `debounceTime`.
- Ejemplo: búsqueda con debounce
  ```typescript
  import { Subject } from 'rxjs';
  import { debounceTime, switchMap } from 'rxjs/operators';

  searchTerm = new Subject<string>();
  results$ = this.searchTerm.pipe(
    debounceTime(300),
    switchMap(term => this.api.buscar(term))
  );
  ```
  ```html
  <input (input)="searchTerm.next($event.target.value)" />
  <ul>
    <li *ngFor="let r of (results$ | async)">{{ r.nombre }}</li>
  </ul>
  ```

## Interceptores, Guards y Resolvers
- Interceptor (headers, logging, auth):
  ```typescript
  import { HttpInterceptor, HttpRequest, HttpHandler, HttpEvent } from '@angular/common/http';
  import { Injectable } from '@angular/core';
  import { Observable } from 'rxjs';

  @Injectable()
  export class AuthInterceptor implements HttpInterceptor {
    intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
      const authReq = req.clone({ setHeaders: { Authorization: 'Bearer token' } });
      return next.handle(authReq);
    }
  }
  ```
- Guard (proteger rutas):
  ```typescript
  import { CanActivate, Router } from '@angular/router';
  import { Injectable } from '@angular/core';

  @Injectable({ providedIn: 'root' })
  export class AuthGuard implements CanActivate {
    constructor(private router: Router) {}
    canActivate(): boolean {
      const logged = true;
      if (!logged) this.router.navigate(['/login']);
      return logged;
    }
  }
  ```
- Resolver (cargar datos antes de entrar):
  ```typescript
  import { Resolve } from '@angular/router';
  import { Injectable } from '@angular/core';

  @Injectable({ providedIn: 'root' })
  export class DetalleResolver implements Resolve<any> {
    resolve() { return this.api.obtenerInicial(); }
  }
  ```

## Módulos vs Standalone Components (Angular moderno)
- Desde Angular 15+, puedes crear componentes standalone y evitar `NgModules` para la mayoría de casos.
- Standalone:
  ```typescript
  import { Component } from '@angular/core';
  import { CommonModule } from '@angular/common';

  @Component({
    selector: 'app-home',
    standalone: true,
    imports: [CommonModule],
    template: `<h1>Home</h1>`
  })
  export class HomeComponent {}
  ```
- Routing con standalone:
  ```typescript
  export const routes: Routes = [
    { path: '', loadComponent: () => import('./features/home/home.component').then(m => m.HomeComponent) }
  ];
  ```
- ¿Cuándo usar módulos?: librerías, agrupación lógica compleja, escenarios legacy.

## Estado y comunicación entre componentes
- Inputs/Outputs:
  ```typescript
  import { Input, Output, EventEmitter } from '@angular/core';
  @Input() item!: Item;
  @Output() cambiar = new EventEmitter<Item>();
  ```
- Servicios compartidos (`Subject`/`BehaviorSubject` para estado global simple).
- Librerías de estado (nivel intermedio): NgRx, NGXS, Akita.
- Patrón smart/dumb components: contenedores manejan lógica/estado, presentacionales muestran datos.

## Testing básico
- Unit testing con Jasmine/Karma:
  ```typescript
  describe('TituloPipe', () => {
    it('debería poner en mayúsculas', () => {
      const pipe = new TituloPipe();
      expect(pipe.transform('hola')).toBe('HOLA');
    });
  });
  ```
- Pruebas de componentes (`TestBed`) y servicios (`HttpTestingController`).
- E2E: Playwright o Cypress (recomendados) para pruebas de interfaz y flujo.

## Estilos, theming y librerías UI
- SCSS modular y variables globales.
- Librerías: Angular Material, PrimeNG, NG-ZORRO, Tailwind CSS.
- Ejemplo con Angular Material:
  ```bash
  ng add @angular/material
  ```

## Performance y buenas prácticas
- `ChangeDetectionStrategy.OnPush` en componentes con datos inmutables.
- `trackBy` en `*ngFor`:
  ```html
  <li *ngFor="let item of items; trackBy: trackById">{{ item.nombre }}</li>
  ```
  ```typescript
  trackById(_: number, item: {id:number}) { return item.id; }
  ```
- Lazy loading de rutas y componentes.
- Evitar lógica pesada en plantillas; usar pipes puros o precomputar en TS.
- Desuscribirse cuando corresponda (`takeUntil`, `async` pipe).
- Evitar `any`; preferir tipos estrictos.

## Internacionalización (i18n) básica
- i18n integrado de Angular o librerías como Transloco.
- Ejemplo con Transloco:
  - Configurar claves en `assets/i18n/es.json` y `en.json`.
  - Usar el pipe: `{{ 'home.title' | transloco }}`

## Despliegue
- Build de producción:
  ```bash
  ng build --configuration production
  ```
- Opciones:
  - Vercel/Netlify: subir carpeta `dist` o conectar repositorio.
  - Firebase Hosting: `firebase init hosting`, `firebase deploy`.
  - GitHub Pages: usar `gh-pages` y configuración de `base href`.

## Mini-proyecto: “Lista de Tareas con API”
**Objetivo:** CRUD de tareas, filtro, búsqueda y detalle.

**Pasos:**
1) Crear proyecto:
   ```bash
   ng new tareas-app --routing --style=scss
   ```
2) Crear componentes:
   - `features/tareas/lista`
   - `features/tareas/detalle`
   - `shared/tarea-item`
3) Servicio de API con `HttpClient` (como el ejemplo `TodoApi`).
4) Routing:
   ```typescript
   const routes: Routes = [
     { path: '', redirectTo: 'tareas', pathMatch: 'full' },
     { path: 'tareas', loadComponent: () => import('./features/tareas/lista/lista.component').then(c => c.ListaComponent) },
     { path: 'tareas/:id', loadComponent: () => import('./features/tareas/detalle/detalle.component').then(c => c.DetalleComponent) },
     { path: '**', redirectTo: 'tareas' }
   ];
   ```
5) `ListaComponent`: obtiene tareas, permite crear y filtrar.
   ```typescript
   import { map } from 'rxjs/operators';

   tasks$ = this.api.listar().pipe(
     map(ts => ts.filter(t => !this.search || t.title.includes(this.search)))
   );
   search = '';
   crear(title: string) { this.api.crear({ title, completed:false }).subscribe(() => this.reload()); }
   ```
6) `DetalleComponent`: muestra/edita/elimina una tarea por id usando `ActivatedRoute` y API.
7) Formularios: usar Reactive Forms para validar entrada.
8) Interceptor de logging y manejo de errores con `catchError`.
9) Pruebas unitarias para el servicio y un componente.
10) Despliegue a Vercel/Netlify.

## Ejercicios propuestos
- Principiante:
  - Crear un contador con botones y estilos.
  - Lista con `*ngFor` y filtro por texto.
  - Pipe para formatear nombres.
- Intermedio:
  - Formulario reactivo con validaciones personalizadas.
  - Consumir una API, mostrar loading y errores.
  - Implementar un guard que bloquee `/admin` si no hay rol.
  - Crear un interceptor que agregue cabecera `X-App-Version`.

## Errores comunes y cómo resolverlos
- Template parse errors: revisa nombres de propiedades y módulos importados.
- Null/undefined en plantillas: usa el operador de navegación segura `?.` y tipa correctamente.
- No provider for X: asegura `providedIn` o provisión en `imports/Component`.
- CORS: configurar backend o usar proxies en desarrollo.
- Rutas que no cargan: revisar orden, `pathMatch` y que `router-outlet` esté presente.

## Recursos y próximos pasos
- Angular Docs oficiales: [Angular Documentation](https://angular.io/docs)
- Tour of Heroes (clásico): [Tour of Heroes](https://angular.io/tutorial)
- RxJS: [RxJS Docs](https://rxjs.dev)
- Angular Material: [Angular Material](https://material.angular.io)
- Guía de estilo Angular: [Angular Style Guide](https://angular.io/guide/styleguide)

---

¿Sugerencias? Puedes abrir un issue en este repo con mejoras o dudas.
