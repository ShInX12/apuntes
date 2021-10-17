# Agregando MongoDB

1. Creamos una base de datos en [MongoDB](https://www.mongodb.com/es)

---

2. Instalamos [mongoose](https://www.npmjs.com/package/mongoose)

    `npm install mongoose`

---

3. Copiamos el link de conexión de la base de datos dado por mongodb.com en el archivo **.env**, ejemplo:
   
    `DB_CNN=mongodb+srv://<username>:<password>@cluster0.p5ftb.mongodb.net/test`

---

4. Creamos el archivo **config.js** en la carpeta _src/database_ y agregamos algo similar a:

#### src/database/config.js
```js
const mongoose = require('mongoose');

const dbConnection = async () => {

    try {
        await mongoose.connect(process.env.DB_CNN, {
            useNewUrlParser: true,
            useUnifiedTopology: true,
            useCreateIndex: true
        });
        console.log('[info] Base de datos online');
    } catch (e) {
        console.error(`[database error] ${e.name}`);
        console.error(`[database error] ${e.message}`);
    }

}

module.exports = {
    dbConnection
}
```

---

5. Importamos el módulo en el **index.js** y lo ejecutamos:

#### index.js
```js{5,6}
...
app.use(cors());
app.use(express.json());

const {dbConnection} = require('./database/config');
dbConnection();

app.get('/', (request, response) => {
    response.status(400).json({
        ok: true,
        message: 'hola mundo'
    });
});
...
```

---

6. Creamos los modelos de nuestra base de datos en la carpeta _models_, ejemplo de **usuario.js**:
#### src/models/usuario.js
```js
// Documentación: https://mongoosejs.com/docs/guide.html

const { Schema, model } = require('mongoose');

const UserSchema = Schema({
    email: {
        type: String,
        require: true,
        unique: true
    },
    password: {
        type: String,
        require: true,
    },
    admin: {
        type: Boolean,
        default: false
    },
}, { collection: 'users' });


// Configuramos para que no se muestre __v, _id y password
// Esto es visual, no afecta el schema
UserSchema.method('toJSON', function () {
    const { __v, _id, password, ...object } = this.toObject()
    object.uid = _id
    return object
});

module.exports = model('User', UserSchema);
```