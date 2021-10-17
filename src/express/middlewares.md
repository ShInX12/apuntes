# Middlewares

Las funciones de middleware son funciones que tienen acceso al objeto de solicitud **(req)**, al objeto de respuesta 
**(res)** y a la siguiente función de middleware en el ciclo de solicitud/respuestas de la aplicación. La siguiente 
función de middleware se denota normalmente con una variable denominada **next**.

Las funciones de middleware pueden realizar las siguientes tareas:

- Ejecutar cualquier código.
- Realizar cambios en la solicitud y los objetos de respuesta.
- Finalizar el ciclo de solicitud/respuestas.
- Invocar la siguiente función de middleware en la pila.

---

## Validación de campos y encriptación de contraseñas
Instalamos [express-validator](https://www.npmjs.com/package/express-validator)
y [bcryptjs](https://www.npmjs.com/package/bcryptjs) para realizar validaciones y encriptaciones de contraseñas

`npm i express-validator bcryptjs`

---

En el archivo **middlewares/validator.js** escribimos:

```js
const { response } = require('express'); // Opcional si no queremos ayuda de tipado
const { validationResult } = require('express-validator');
                         // lo igualamos a response para tener ayudas de tipado
const validate = (req, res = response, next) => {

    const errores = validationResult( req );
    if(!errores.isEmpty()){
        return res.status(400).json({
            errores: errores.mapped()
        });
    }
    next();
}

module.exports = {
    validate
}
```

---

## Usando Json Web Token
El [Json Web Token](https://jwt.io/) (JWT) sirve para generar un token para asegurarnos de que las peticiones que 
se hagan sean de un usuario que haya iniciado sesión previamente 

Lo instalamos de la siguiente manera:

`npm i jsonwebtoken`

En el **.ENV** creamos el jwt_secret, ejemplo:

`JWT_SECRET=palabraalazarconmuchoscaracteres`

::: warning AVISO
Debemos de cuidar de terceros esta llave ya que con ella podremos decodificar los tokens
:::

### Validando el token

En el archivo **src/middlewares/jwt-validator.js** insertamos lo siguiente para validar que sea correcto en cada 
peticion en el que se use:

```js
const jwt = require('jsonwebtoken');

const validateJWT = (request, response, next) => {

    // Leer el token
    const token = request.header('x-token');

    if (!token) {
        return response.status(401).json({
            message: 'No hay token en la peticion'
        });
    }

    try {

        // Desestructuramos el payload para sacar el uid
        const {uid} = jwt.verify(token, process.env.JWT_SECRET);
        // Agregamos el uid al request
        request.uid = uid;
        // Continuamos al siguiente middleware o controller
        next();
    } catch (error) {
        return response.status(401).json({
            message: 'Token incorrecto'
        });
    }
}

module.exports = {
    validateJWT
}
```

---

### Generando el token

En el archivo **src/helpers/jwt.js** insertamos lo siguiente para generar tokens de acuerdo a la necesidad:

```js
const jwt = require('jsonwebtoken');

const generateJWT = (uid) => {

    return new Promise((resolve, reject) => {

        const payload = {
            uid
        };

        jwt.sign(payload, process.env.JWT_SECRET, {
            expiresIn: '24h'
        }, (error, token) => {
            if (error) {
                console.warn(error);
                reject('No se pudo generar el token');
            } else {
                resolve(token);
            }
        });
    });

}

module.exports = {
    generateJWT
}
```