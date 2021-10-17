# Angular + Firebase

Usando los servicios de firebase en Angular :fire:

## Instalacion de Angular Fire

::: tip Información sacada de:
[Guia oficial](https://github.com/angular/angularfire/blob/master/docs/install-and-setup.md)
:::

Para instalar Angular Fire en nuestro proyecto ejecutamos el comando:


`ng add @angular/fire`


### Enlace del proyecto a la consola de Firebase

Una vez creado el proyecto en firebase, nos dirigimos a la consola de firebase > configuración del proyecto > Añadir app
web, descargamos el archivo de configuración y copiamos su contenido en los archivos **environment.ts**

#### Ejemplo:

```js
export const environment = {
    firebaseConfig: {
        apiKey: 'AIzaSyB9rfdsafdsaasur33Wd-Z2JiPz4eDgfdsY',
        authDomain: 'proyecto.firebaseapp.com',
        projectId: 'proyecto',
        storageBucket: 'proyecto.appspot.com',
        messagingSenderId: '9515435349878',
        appId: '1:954586489878:web:f439ef3499bdsada637'
    },
}
```

Continuamos la guía... :runner:

#### En el app module importamos:

```ts
import { AngularFireModule } from '@angular/fire';

@NgModule({
  declarations: [
    ...
  ],
  imports: [
    ...
    AngularFireModule.initializeApp(environment.firebaseConfig),
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

#### Finalmente, inyectamos el módulo deseado en un componente, ejemplo:

`constructor(firestore: AngularFirestore) { }`

::: tip
Para importar más servicios de Angular Fire mirar la [documentación](https://github.com/angular/angularfire/blob/master/docs/install-and-setup.md)
:::