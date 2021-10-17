# Instalación

1. Creamos la carpeta del proyecto
---
2. Adentro de la carpeta iniciamos un proyecto NPM con:


    `npm init`
---

3. Instalamos [express](https://www.npmjs.com/package/express):


    `npm install express --save`

::: tip Si necesitamos una version en especial:

`npm install express@4.17.1 --save`

:::

---

4. Creamos un archivo **index.js** y escribimos adentro:
```js
const express = require('express');

// Crear el servidor de express
const app = express();

// Configuramos el puerto
app.listen(3000, () => {
    console.log('Servidor corriendo en puerto 3000')
});
```

:information_source: Para correrlo usamos:


`node index.js`

---

5. Para que se se reinicie el servidor al guardar un cambio instalamos [nodemon](https://www.npmjs.com/package/nodemon):


`npm install nodemon`

---

5. En **package.json** agregamos en "scripts":


`"start:dev": "nodemon index.js"`

Y lo levantamos con:


`npm run start:dev`

:information_source: Para usarlo manualmente usamos:


`nodemon index.js`


:information_source: Para usar git agregamos al **.gitignore**:


`node_modules/`

----------------------------------------------------------------------

6. Configuramos nuestras variables de entorno:


    - Instalamos [dotenv](https://www.npmjs.com/package/dotenv): `npm install dotenv`

    - creamos en nuestra carpeta raiz un archivo ".env", allí ponemos el puerto, db, tokens, keys, etc...

    En este caso agregamos el puerto: `PORT=3000`

    - Luego en **index.js** lo importamos:

    `require('dotenv').config();`

    - Finalmente, lo usamos en nuestro **index.js**

```js
app.listen(process.env.PORT, () => {
    console.log(`[info] Servidor corriendo en el puerto ${process.env.PORT}`);
});
// Esta instrucción la colocamos al final del index.js
```
---
7. Instalamos [cors](https://www.npmjs.com/package/cors):


    `npm install cors`

    Luego lo importamos en index.js:
    `const cors = require('cors');`

    Y lo agregamos al proyecto:
    `app.use(cors());`

---

8. Usamos lo siguiente para leer y parsear json:


    `app.use(express.json());`

---
9. Creamos las carpetas necesarias (recomendable dentro de la carpeta src):
```{3-8}
.
├─ src
│  ├─ controllers
│  ├─ database
│  ├─ helpers
│  ├─ middlewares
│  ├─ models
│  └─ routes
├─ .env
├─ .gitignore
├─ index.js
├─ package-lock.json
└─ package.json

```
---
10. Creamos las rutas en el **index.js** usando la siguiente instrucción:
```js
app.use('/api/user', require('./src/routes/users'));
```


Para hacer rutas genéricas en el **index.js**:
```js
app.get('/', (request, response) => {
    response.status(400).json({
        ok: true,
        message: 'hola mundo'
    });
});
```

--- 

### Instalaciones usadas:

`npm install express nodemon dotenv cors`

### Plantilla:

#### index.js
```js
require('dotenv').config();
const express = require('express');
const cors = require('cors');

const app = express();
app.use(cors());
app.use(express.json());


app.get('/', (request, response) => {
    response.status(400).json({
        ok: true,
        message: 'hola mundo'
    });
});

app.listen(process.env.PORT, () => {
    console.log(`[info] Servidor corriendo en el puerto ${process.env.PORT}`);
});
```