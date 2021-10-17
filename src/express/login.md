# Iniciando sesión

Finalmente podemos hacer que nuestra app de express.js tenga la funcionalidad de iniciar sesión,
los pasos para realizarlo son los siguientes:

## Creando el controlador
Creamos el archivo auth.js dentro de la carpeta controllers y ponemos:
```js
const bcrypt = require('bcryptjs');
const { generateJWT } = require('../helpers/jwt');
const User = require('../models/user');

const login = async (request, response) => {

    const {email, password} = request.body;

    try {
        // Comprobamos que el email exista
        const userDB = await User.findOne({email});

        if (!userDB) {
            return response.status(404).json({
                message: 'Correo no encontrado'
            });
        }

        // Verificar contrasenna
        const validPassword = bcrypt.compareSync(password, userDB.password);
        if (!validPassword) {
            return response.status(400).json({
                message: 'Contraseña incorrecta'
            });
        }

        // Generar el token -JWT
        const token = await generateJWT(userDB.id);

        response.json({
            token,
        });
    } catch (e) {
        console.warn(`[error] controllers/auth: login -> ${e.name}`);
        console.warn(`[error] ${e.message}`);
        response.status(500).json({
            name: e.name,
            message: e.message,
            file: 'controllers/auth: login'
        });
    }
}

const renewToken = async (request, response) => {

    const uid = request.uid;
    const token = await generateJWT(uid);
    const userDB = await User.findById(uid);

    response.json({
        token,
        user: userDB,
    });

}

module.exports = {
login,
renewToken
}
```

---

## Creando la ruta

Luego creamos el archivo auth.js dentro de la carpeta routes:

```js
const { Router } = require('express');
const { login, renewToken } = require('../controllers/auth');
const { check } = require('express-validator');
const { validate } = require('../middlewares/validator');
const { validateJWT } = require('../middlewares/jwt-validator');

const router = Router();

router.post('/',
    [
        check('email', 'El correo no es válido').isEmail(),
        check('password', 'La contraseña es obligatora').not().isEmpty(),
        validate
    ],
    login
);

router.get('/renew', validateJWT, renewToken);

module.exports = router;

```

---

## Agregando la ruta a la app

Luego en index.js agregamos la ruta con su respectivo path y controller:

```js
app.use('/api/login', require('./src/routes/auth'));
```
