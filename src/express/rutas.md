# Rutas

Creamos las rutas de la siguiente manera

Ejemplo: **src/routes/user.js**

```js
const { Router } = require('express');
const { check } = require('express-validator');
const { validate } = require('../middlewares/validator');
const { validateJWT } = require('../middlewares/jwt-validator');

const {
    count,
    findAll,
    findById,
    saveUser,
    updateUser,
    deleteUser
} = require('../controllers/user');

const router = Router();

router.get('/count', count);

router.get('/findAll', validateJWT, findAll);

router.get('/findById/:id', validateJWT, findById);

router.post('/save',
    [
        check('email', 'El correo no es válido').isEmail(),
        check('password', 'La contraseña es obligatoria').not().isEmpty(),
        check('password', 'La contraseña debe tener 6 o más caracteres').isLength({ min: 6 }),
        validate
    ],
    saveUser
);

router.put('/update/:id',
    [
        validateJWT,
        check('email', 'El correo no es válido ').isEmail(),
        validate
    ],
    updateUser
);

router.delete('/delete/:id', validateJWT, deleteUser);

module.exports = router;
```

---

Luego en **index.js** (recomendado) las agregamos a la app de express:

```js {8,9}
...
app.use(cors());
app.use(express.json());

const {dbConnection} = require('./database/config');
dbConnection();

// app.use('path', 'route-controller')
app.use('/api/user', require('./src/routes/users'));

app.get('/', (request, response) => {
    response.status(400).json({
        ok: true,
        message: 'hola mundo'
    });
});
...
```
