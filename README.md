# Dmytro Hasura GraphQL Backend

Este es un Repo enfocado a desarrollar el curso de Udemy de Dmytro para Hasura GraphQL Backend como un training. Ademàs, establece los pasos y aprendizajes que se extraigan de este curso.

- [Dmytro Hasura GraphQL Backend](#dmytro-hasura-graphql-backend)
  - [Correr Hasura](#correr-hasura)
    - [Desde Hasura Cloud](#desde-hasura-cloud)
    - [En local con Docker](#en-local-con-docker)
  - [Basics](#basics)
    - [Creando Tablas en Hasura Database](#creando-tablas-en-hasura-database)

## Correr Hasura

Hay diferentes maneras de tener un instancia corriendo de Hasura, a continuación se explicarán algunas de las que se muestran en el curso.

### Desde Hasura Cloud

- Entra a la [URL de Hasura](https://hasura.io) y accede a tu cuenta en caso de tenerla, o crea un a cuenta y confírmala.
- Una vez hecho lo anterior, se creará un proyecto y podrás acceder a toda la información por default.
- Es hora de crear una variable de entorno para desarrollo, en la sección de configuración del proyecto, entra a `Env vars` y crea una nueva variable, en el buscador escribe: `DEV_MODE` y cambia el valor a `true`.
- **NOTA!** Podemos agregar un dominio propio en caso de así requerirlo, solo basta entrar a la sección `Domain` y agregar un `New Custom Domain` y listo.
- Ya podemos dar click al botón `LAUNCH CONSOLE` para ver nuestro Dashboard de Hasura GraphQL.

### En local con Docker

- Debemos tener instalado Docker en nuestra computadora
- Una vez comprobado esto, creamos nuestro proyecto en el folder que deseemos, accedemos a él y creamos un archivo de configuración siguiendo las instrucciones [Aquí](https://hasura.io/docs/1.0/graphql/core/getting-started/docker-simple.html#step-1-get-the-docker-compose-file) descargando el archivo de configuración de la manera que mejor se adapte al SO que tengamos.
- Dentro del archivo `docker-compose.yml`, podemos hacer los siguientes cambios:

```yml
services:
  postgres:
    volumes:
      - ./db_data:/var/lib/postgresql/data # Por default no trae antepuesto ./, hay que agregarlo al inicio
  graphql-engine:
    ports:
    - '8080:8080' # Podemos cambiar al puerto de nuestra preferencia en caso que el puerto 8080 esté ocupado: 8082:8080
```

- En esta [URL](https://hasura.io/docs/1.0/graphql/core/hasura-cli/config-reference.html#environment-variables) encontraremos más a detalle el listado de variables que podemos agregar o modificar en el archivo `docker-compose.yml`.
- Ya es momento de levantar el servicio en Docker, para ello, lo hacemos con el comando: `docker-compose up` en la Terminal. **NOTA!**: Asegúrate de tener iniciado Docker para que inicie correctamente.
- Si todo está hecho correctamente, ya puedes acceder en tu navegador a `http://localhost:8080` y verás el Dashboard de Hasura.

## Basics

Esta es la sección donde se ven temas genrales de Hasura y GraphQL, solo se agregarán comentarios cuando la información aporte algo adicional.

### Creando Tablas en Hasura Database

- En la parte de arriba del Dasboard encontramos la sección `DATA`, ahí encontraremos la manera general de agragar _tablas_.
- En la versión de `Hasura v2.0.3` no viene conectada la base de datos, pero lo podemos hacer agregando en name: `localhost` y elige en _Connect Database Via_ por _Database URL_, y esa información la extraes de el archivo `docker-compose.yml`:

```yml
services:
  graphql-engine:
    environment:
      HASURA_GRAPHQL_METADATA_DATABASE_URL: postgres://postgres:postgrespassword@postgres:5432/postgres # Aquí
```

- Das click en connect y listo! Puedes leer más acerca de las conexiones con Base de Datos [Aquí](https://hasura.io/docs/latest/graphql/cloud/projects/create.html#existing-database)
- Ahora si, crea una tabla en el Dashboard, en este ejempo veremos la creación de la tabla `photos`:

![Imagen de creacion de tabla photos](assets/img-001.png)

- Ponemos nombre a la tabla
- a. Al crear la columna _id_, podemos elegir como *column_type* `UUID` y en el *default_value* podemos ejecutar una function para que cree el _id_ automáticamente.
- b. Las columnas *created_at* y *updated_at* podemos elegirlas de la lista que nos despliega el botón `Frequently used columns` y estas llenan los valores de acuerdo al default de Hasura.
- Recordamos elegir nuestro `Primary key`, en este caso será el _id_.
- Al final en la parte de abajo, hay un input para describir nuestra tabla.
- Una vez aceptado y creada la tabla, en el _Dasboard -> DATA_, al abrir _public_, nos aparecerá nuestra nueva tabla con un botón al lado que dice `track`, dale click para que puedas acceder a la tabla y en el _left drawer_ te la muestre como acceso directo.
- Ya con acceso a la table, podemos crear nuestros primeros datos, dando click en el left drawer -> photos y en Insert row puedes agregar la cantidad de campos que desees.
- Listo!
