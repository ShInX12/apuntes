# Crear controladores


Creamos los controllers necesarios para el CRUD para cada modelo según la necesidad

#### Ejemplo: **src/controllers/user.js**:

```js
const { response } = require('express'); // Opcional
const User = require('../models/user');
const bcrypt = require('bcryptjs');
const { generateJWT } = require('../helpers/jwt');

const count = async (request, response) => {

    total_count = await User.countDocuments();
    response.status(200).json({ count: total_count });

}

const findAll = async (request, response) => {

    if (request.query.page < 1) {
        request.query.page = 1
    }

    const page = Number(request.query.page - 1) || 0;

    // Ejecuta todas estas promesas, esto con el fin de optimizar
    const [users, total_count] = await Promise.all([
        User.find().skip(page * 10).limit(10),
        User.countDocuments()
    ]);

    let from = (page * 10) + 1;
    let to = (page * 10) + 10;
    if (from > total_count) {
        from = total_count;
    }
    if (to > total_count) {
        to = total_count;
    }

    response.status(200).json({ users, from, to, total_count });
}

const findById = async (request, response) => {

    const uid = request.params.id;

    try {

        const userDB = await User.findById(uid);

        if (!userDB) {
            return response.status(404).json({
                message: 'No existe un usuario con ese id'
            });
        }

        response.status(200).json(userDB);

    } catch (e) {
        return response.status(404).json({
            message: 'No existe un usuario con ese id'
        });
    }
}

const saveUser = async (request, response) => {

    const { email, password } = request.body;

    try {

        const existEmail = await User.findOne({ email });

        if (existEmail) {
            return response.status(400).json({
                message: "EL correo ya está registrado"
            });
        }

        const user = new User(request.body);

        // Encriptar contrasenna
        const salt = bcrypt.genSaltSync();
        user.password = bcrypt.hashSync(password, salt);

        // Generamos el token
        const token = await generateJWT(user.id);

        const userDB = await user.save();
        response.status(201).json({
            user: userDB,
            token
        });
    } catch (e) {
        console.warn(`[error] controllers/users: createUser -> ${e.name}`);
        console.warn(`[error] ${e.message}`);
        response.status(500).json({
            name: e.name,
            message: e.message,
            file: 'controllers/users: createUser'
        });
    }
}

const updateUser = async (request, response) => {
    const uid = request.params.id;

    try {

        const userDB = await User.findById(uid);

        if (!userDB) {
            return response.status(404).json({
                message: 'No existe un usuario con ese id'
            });
        }

        const { password, email, ...campos } = request.body;

        if (userDB.email !== email) {
            const existEmail = await User.findOne({ email });
            if (existEmail) {
                return response.status(400).json({
                    message: 'Ya existe un usuario con ese correo'
                });
            }
        }

        campos.email = email;

        const userUpdated = await User.findByIdAndUpdate(
            uid,
            campos,
            { new: true, useFindAndModify: false }
        );

        response.status(200).json(userUpdated);

    } catch (e) {
        console.warn(`[error] controllers/users: updateUser -> ${e.name}`);
        console.warn(`[error] ${e.message}`);
        response.status(500).json({
            name: e.name,
            message: e.message,
            file: 'controllers/users: updateUser'
        });
    }
}

const deleteUser = async (request, response) => {

    const uid = request.params.id;

    try {

        const userDB = await User.findById(uid);

        if (!userDB) {
            return response.status(404).json({
                message: 'No existe un usuario con ese id'
            });
        }

        await User.findByIdAndDelete(uid);

        response.status(200).json({
            message: 'Usuario eliminado'
        });

    } catch (e) {
        console.warn(`[error] controllers/users: deleteUser -> ${e.name}`);
        console.warn(`[error] ${e.message}`);
        response.status(500).json({
            name: e.name,
            message: e.message,
            file: 'controllers/users: deleteUser'
        });
    }

}

module.exports = {
    count,
    findAll,
    findById,
    saveUser,
    updateUser,
    deleteUser
}

```

#### Otros ejemplos:
```js
const getMedicoById = async (request, response) => {

    const id = request.params.id;

    try {
        const medico = await Medico.findById(id)
            .populate('usuario', 'nombre img')
            .populate('hospital', 'nombre img')

        response.json({
            ok: true,
            medico
        });
    } catch (e) {
        response.json({
            message: 'Médico no encontrado'
        });

    }

}

const crearMedico = async (request, response) => {

    const uid = request.uid;
    const medico = new Medico({
        usuario: uid,
        ...request.body
    });

    try {

        const medicoDB = await medico.save();
        response.json({
            medicoDB
        });
    } catch (error) {
        response.status(500).json({
            message: 'Hable con el admin'
        });
    }
}

const actualizarMedico = async (request, response) => {

    const id = request.params.id;
    const uid = request.uid;

    try {

        medico = await Medico.findById(id);

        if (!medico) {
            return response.status(404).json({
                message: 'Médico no encontrado por id'
            });
        }

        const cambiosMedico = {
            ...request.body,
            usuario: uid
        }

        const medicoActualizado = await Medico.findByIdAndUpdate(id, cambiosMedico, {new: true});

        response.json({
            message: 'Médico actualizado',
            medico: medicoActualizado
        });

    } catch (error) {
        response.status(500).json({
            message: 'Hable con el administrador'
        });
    }
}

const borrarMedico = async (request, response) => {
    const id = request.params.id;

    try {

        medico = await Medico.findById(id);

        if (!medico) {
            return response.status(404).json({
                message: 'Medico no encontrado por id'
            });
        }

        await Medico.findByIdAndDelete(id);

        response.json({
            message: 'Médico eliminado',
        });

    } catch (error) {
        response.status(500).json({
            message: 'Hable con el administrador'
        });
    }
}
```