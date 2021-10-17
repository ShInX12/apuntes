# Firebase hosting

1. Para subir un proyecto de Angular a Firebase Hosting primero generamos la versión de producción con el comando `ng build
--configuration production`


    ::: tip Tip: para publicarla dentro de una carpeta usamos:
    `ng build --prod --aot --base-href /carpeta_que_vamos_a_usar/`
    :::


---
2. Instalamos el CLI de Firebase:


    `npm install -g firebase-tools`

3. Vamos a la consola de firebase y damos en comenzar firebase hosting

    Seguimos el tutorial de Firebase :books:...


---

4. Escribimos en la consola `firebase init`

    En las preguntas...
   - Seleccionamos hosting
   - Directorio publico escribimos la ruta: Ej: ./dist/nombreApp
   - Configurar la aplicacion de una sola página: "yes"
   - Sobrescribimos el archivo index.html?: "No"

   **Info**: Si queremos cambiar los anteriores valores vamos a firebase.json

---

5. Finalmente escribimos: `firebase deploy` para desplegarlo