# Conceptos

## ¿Qué es? :thinking:

[Angular](https://angular.io/) es un framework de diseño de aplicaciones y plataforma de desarrollo para crear aplicaciones de una sola
página (SPA) eficientes y sofisticadas. Lo que quiere decir que a la hora de cambiar de una pantalla a otra el navegador
no "refresca" la página entera.

- Para usar Angular necesitamos tener instalado [node.js](https://nodejs.org/es/)
- Ejecutamos el comando `npm install -g @angular/cli`
- Creamos el proyecto con `ng new mi-aplicacion`
- Avanzamos `cd mi-aplicacion`
- Lanzamos el servidor `ng serve --o`
- Si necesitamos otro puerto usamos `ng serve --port 5100`

## Módulos

Las aplicaciones Angular son modulares y Angular tiene su propio sistema de modularidad llamado [NgModules](https://docs.angular.lat/guide/ngmodules).

Cada aplicación Angular tiene al menos una clase NgModule, el módulo raíz, que se llama convencionalmente AppModule y
reside en un archivo llamado app.module.ts. Inicia tu aplicación cargando el NgModule raíz.

Para crear un módulo usamos el comando `ng generate module <nombre> [opciones]` o
simplemente `ng g m <nombre> [opciones]`

::: tip
Si queremos ejecutar `ng generate` pero no estamos seguros de los archivos a generar agregamos `--dry-run` para obtener
una previsualizacion de los archivos que se van a generar
:::

### Opciones

| Opción                | Tipo de valor                   | Valor por defecto |
| --------------------- | ------------------------------- |-------------------|
| --flat                | boolean                         | false             |
| --module              | string                          |                   |
| --project             | string                          |                   |
| --route               | string                          | false             |
| --routing             | boolean                         | false             |
| --routing-scope       | Child - Root                    | Child             |

### Metadatos de los módulos

- **declarations:** Los componentes, directivas, y pipes que pertenecen a este NgModule.

- **exports:** El subconjunto de declaraciones que deberían ser visibles y utilizables en las plantillas de componentes
  de otros NgModules.

- **imports:** Otros módulos cuyas clases exportadas son necesarias para las plantillas de componentes declaradas en
  este NgModule.

- **providers:** Creadores de servicios que este NgModule aporta a la colección global de servicios; se vuelven
  accesibles en todas las partes de la aplicación. (También puedes especificar proveedores a nivel de componente, que a
  menudo es preferido).

- **bootstrap:** La vista principal de la aplicación, llamado el componente raíz, que aloja todas las demás vistas de la
  aplicación. Sólo el NgModule raíz debe establecer la propiedad bootstrap.

### Ejemplo:

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
@NgModule({
imports:      [ BrowserModule ],
providers:    [ Logger ],
declarations: [ AppComponent ],
exports:      [ AppComponent ],
bootstrap:    [ AppComponent ]
})
export class AppModule { }
```

## Componentes

Un componente controla una parte en pantalla llamada vista.

Para crear un componente usamos el comando `ng generate component <nombre> [opciones]` o
simplemente `ng g c <nombre> [opciones]`

Este comnando genera los siguientes cuatro archivos:

- Archivo **.TS** del componente
- Archivo **.HTML** de plantilla _(puede ser opcional)_
- Archivo **.CSS** de estilos _(puede ser opcional)_
- Archivo **.SPEC.TS** de pruebas unitarias _(puede ser opcional)_

### Opciones

| Opción                | Tipo de valor                   | Valor por defecto |
| --------------------- | ------------------------------- |-------------------|
| --change-detection    | Default o OnPush                | default           |
| --display-block       | boolean                         | false             |
| --export              | boolean                         | false             |
| --flat                | boolean                         | false             |
| --inline-style        | boolean                         | false             |
| --inline-template     | boolean                         | false             |
| --module              | string                          |                   |
| --prefix              | string                          |                   |
| --project             | string                          |                   |
| --selector            | string                          |                   |
| --skip-import         | boolean                         | false             |
| --skip-selector       | boolean                         | false             |
| --skip-tests          | boolean                         | false             |
| --style               | css - scss - sass - less - none | css               |
| --type                | string                          | Component         |
| --view-encapsulation  | Emulated - None - ShadowDom     |                   |

### Ejemplo de un componente:

#### src/app/hero-list.component.ts (class)

```ts
export class HeroListComponent implements OnInit {
  heroes: Hero[];
  selectedHero: Hero;

  constructor(private service: HeroService) { }

  ngOnInit() {
    this.heroes = this.service.getHeroes();
  }

  selectHero(hero: Hero) { this.selectedHero = hero; }
}
```

#### src/app/hero-list.component.html

```html
<h2>Hero List</h2>

<p><i>Pick a hero from the list</i></p>
<ul>
  <li *ngFor="let hero of heroes" (click)="selectHero(hero)">
    {{hero.name}}
  </li>
</ul>

<app-hero-detail *ngIf="selectedHero" [hero]="selectedHero"></app-hero-detail>
```

Como se puede observar, hay varias maneras en las cuales la información fluye entre los archivos .ts y .html. Angular
admite enlace de datos bidireccional, un mecanismo para coordinar las partes de una plantilla con las partes de un
componente:

![img](/img/binding-angular.png)

## Servicios

Un componente puede delegar ciertas tareas a los servicios, como obtener datos del servidor o validar la entrada del
usuario. Al definir tales tareas de procesamiento en una clase de servicio _inyectable_, hace que esas tareas sean
disponibles para cualquier componente.

Para crear un servicio lo hacemos con el comando: `ng generate service <nombre> [opciones]` o
simplemente `ng g s <nombre> [opciones]`

### Las opciones pueden ser:

| Opción        | Descripcion                                                     | Tipo de valor | Valor por defecto |
| ------------- |-----------------------------------------------------------------| --------------|-------------------|
| --flat        | Crear el archivo un nivel por encima sin crearlo en una carpeta | boolean       | true              |
| ~~--lint-fix~~| **Deprecated:** Usar "ng lint --fix"                            | boolean       |                   |
| --project     | El nombre del proyecto                                          | string        |                   |
| --skip-tests  | No crea el archivo "spec.ts" para las pruebas                   | boolean       | false             |

### Ejemplo de un servicio para consumo de API REST

```ts
@Injectable({
  providedIn: 'root'
})
export class UserService {

  public url: string;

  get token(): string {
    return localStorage.getItem('token') || '';
  }

  get headers(): object {
    return {
      headers: {
        'x-token': this.token
      }
    };
  }

  constructor(public httpClient: HttpClient) {
    this.url = environment.apiUrl + '/api/users/';
  }

  public findAll(id: string): Observable<any> {
    return this.httpClient.get(this.url + 'findAll/', this.headers);
  }
  
  public save(user: User): Observable<any> {
    return this.httpClient.post(this.url + 'save', user, this.headers);
  }

  public update(user: User): Observable<any> {
    return this.httpClient.put(this.url + 'update/' + user.id, user, this.headers);
  }

  public delete(user: User): Observable<any> {
    return this.httpClient.delete(this.url + 'delete/' + user.id, this.headers);
  }
}
```

### Utilización en un componente

```ts
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
  styleUrls: ['./user.component.scss']
})
export class UserComponent implements OnInit, OnDestroy {
  users: User[];
  subscriptions: Subscription[] = [];

  constructor(private service: UserService) { }

  ngOnInit() {
    this.findAllUsers();
  }
  
  ngOnDestroy() {
    this.subscriptions.forEach((sb) => sb?.unsubscribe());
  }
  
  findAllUsers() {
    const usersSub = this.service.findAll().subscribe(
      ({users}) => this.users = users,
      error => console.warn(error.error.message),
    );
    this.subscriptions.push(usersSub);
  }

}
```

## Ejemplos
- [Repositorio del curso MEAN avanzado de Fernando Herrera](https://github.com/ShInX12/angular-adv-adminpro)
- [Repositorio de ejemplo](https://github.com/ShInX12/ProyectoGrado-frontend)
