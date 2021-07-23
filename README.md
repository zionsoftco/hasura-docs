# Dmytro Hasura GraphQL Backend

Este es un Repo enfocado a desarrollar el curso de Udemy de Dmytro para Hasura GraphQL Backend como un training. Ademàs, establece los pasos y aprendizajes que se extraigan de este curso.

- [Dmytro Hasura GraphQL Backend](#dmytro-hasura-graphql-backend)
  - [Correr Hasura](#correr-hasura)
    - [Desde Hasura Cloud](#desde-hasura-cloud)
    - [En local con Docker](#en-local-con-docker)
  - [Basics](#basics)
    - [Creando Tablas en Hasura Database](#creando-tablas-en-hasura-database)
    - [Realizando Queries básicas con Hasura](#realizando-queries-básicas-con-hasura)
    - [Filtrando con Hasura](#filtrando-con-hasura)
    - [Haciendo búsqueda por texto completo](#haciendo-búsqueda-por-texto-completo)
    - [Combinando múltiples filtros](#combinando-múltiples-filtros)
    - [Paginación en Hasura](#paginación-en-hasura)
    - [Relaciones entre tablas](#relaciones-entre-tablas)
    - [Mutations, creando un item](#mutations-creando-un-item)
    - [Mutations, update items](#mutations-update-items)
    - [Mutations, eliminando items](#mutations-eliminando-items)
    - [Mutations, mutando items con relaciones](#mutations-mutando-items-con-relaciones)
    - [Mutations, usando variables](#mutations-usando-variables)
    - [Subscriptions, queries en tiempo real](#subscriptions-queries-en-tiempo-real)
  - [Extendiendo la lógica de negocios que ofrece Hasura](#extendiendo-la-lógica-de-negocios-que-ofrece-hasura)
    - [Configurando las Firebase Cloud Functions](#configurando-las-firebase-cloud-functions)
    - [Hasura, Event Triggers](#hasura-event-triggers)
    - [Hasura, Event Triggers - Implement Event Logic](#hasura-event-triggers---implement-event-logic)

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

### Realizando Queries básicas con Hasura

Es hora de crear nuestras primeras Queries con GraphiQL en el Dashboard de Hasura:

- En el Dashboar -> API tenemos nuestra sección para realizar consultas con GraphQL.
- Al crear nuestra tabla, se crea también la documentación en la parte derecha del Dashboard.

![Imagen del dashboard APi con la documentacion generada](assets/img-002.png)

- En la siguiente imagen veremos las secciones más representativas que tenemos en API:

![Imagen con secciones relevantes de API](assets/img-003.png)

- a. En el explorer, podemos cambiar el nombre a nuestra `Query`.
- b. Al elegir el checkbox de nuestra query, podemos ver que en _GraphiQL_ se va creando la Query de acuerdo a lo seleccionado.
- c. Una vez que tengamos las cosas en su lugar, según los datos deseados, damos _click_ a `Play` y recibimos la respuesta en la _sección derecha_ de _GraphiQL_.

### Filtrando con Hasura

Existe una observación a resltar al usar GraphiQL con Hasura en el filtrado, y es:

![Imagen con correccion en id _in](assets/img-004.png)

- Cuando eliges la opción _in, por default Hasura te escribe la Query como si fuera de un parámetro string, cuando en realidad debe ser un array de strings como se muestra en la imagen.

### Haciendo búsqueda por texto completo

Es importante mencionar que esta funcionalidad solo es aplicable a columnas tipo text.

- En caso de querer hacer una búsqueda que incluya una palabra, elegimos el operador `_like` en _where_, escribimos el símbolo de `%` antes y después, dando a entender que antes y después queremos que incluya cualquier dato alfanumérico.
- __NOTA!__ Esta búsqueda, dejándola así como está es _case sensitive_, así que si queremos que sea _case insensitive_, debemos usar en vez del operador `_like` el operador `_ilike`:

![Imagen con ejemplo de busqueda por palabra](assets/img-005.png)

- El operador `_similar` tiene un funcionamiento parecido a `_ilike`, solo que `_similar` permite trabajar con definiciones estándar de SQL o con expresiones regulares, escribiéndo la sentencia entre paréntesis y los valores de dentro van a ser considerados de manera _case sensitive_: `_similar: "%(third|Third|second)%"`, teniendo como resultado de esta búsqueda lo siguiente:

```json
{
  "data": {
    "photos": [
      {
        "created_at": "2021-07-20T19:40:02.46547+00:00",
        "description": "Third photo",
        "id": "00334fc9-44f3-4b57-97f5-87eae20f1319",
        "photo_url": "https://res.cloudinary.com/jesuskata/image/upload/v1621285308/jesuskata/card-pokedex_qq6778.jpg"
      }
    ]
  }
}
```

### Combinando múltiples filtros

Para esta sección, usaremos los operadores `_and`, `_not` y `_or`:

- Comenzamos eligiuendo el operador `_and` y después elegimos los otros dos operadores con que queramos hacer la combinación, por ejemplo:

![Imagen ejemplo del uso del operador _and](assets/img-006.png)

- __NOTA!__ Este modo también puede recibir en vez de un objeto de objetos, un array de objetos como se muestra a continuación:

```gql
query GetPhotos {
  photos(where: {_and: [
    {created_at: {_lt: "2021-07-20T19:40:02.46547+00:00"}},
    {photo_url: {_is_null: false}}
  ]}) {
    created_at
    description
    id
    photo_url
  }
}
```

### Paginación en Hasura

- Para hacer la paginación, Hasura nos ofrece los operadores limit y offset, dónde limit se encarga de definir la cantidad máxima de elementos que va a devolver la Query y offset nos permite "saltar" la cantidad de valores que deseemos, por ejemplo:

```gql
photos(limit: 2, offset: 2)
```

- Este ejemplo nos devolvería el último elemento de los 3 que tenemos en nuestra tabla. La fórmula para hacer paginación sería la siguiente:

`offset = (pageNum - 1) * limit`

### Relaciones entre tablas

Para este punto, vamos a crear una nueva tabla llamada `comments`:

![Imagen de creacion de tabla comments_001](assets/img-007.png)

![Imagen de creacion de tabla comments_002](assets/img-008.png)

- a. Al momento de crear la tabla comments, va a haber una columna que se va a relacionar con la tabla `photos`, por tanto esta es llamada photo_id, va a ser de tipo UUID, pero no se le agrega nada en la sección de default.
- b. En la parte de abajo, antes de crear la tabla, vamos a hacer la relación con una _llave foránea_, por tanto damos click a la sección y rellenamos la información como es requerido.

Ahora, es necesario que antes de poder trabajar con las Queries de GraphQL, hagamos algunas modificaciones para exponer correctamente esta información en GraphiQL.

![Imagen para crear la relación y se exponga en GraphiQL](assets/img-009.png)

- a. En Dashboar -> DATA, elegimos la tabla photos y abrimos la pestaña Relationship y accedemos a agregar la relación con la tabla comments.
- En la siguiente imagen se muestra el resultado de haber agregado la relación para la tabla photos.

![Imagen con comments dentro de photos en GraphiQL](assets/img-010.png)

- Ahora es momento de crear algunos comentarios para las diferentes photos. __NOTA!__ Recuerda hacer una Query para traer los elementos de photos y poder tomar sus id y así hacer los comments.
- __NOTA!__ Ahora que ya tenemos dos tablas y están relacionadas entre sí, podemos hacer uso del TAB Voyager, que nos sirve para ver gráficamente cómo están hechas las relaciones de nuestra base de datos.
- __NOTA!__ Otra cosa a la que podemos acceder es al TAB Analyze, que nos permite ver las sentencias de SQL realizadas para obtener los datos en GraphiQL al hacer una Query. Una herramienta bastante útil para entender cómo se están haciendo las consultas con el lenguaje de SQL.

### Mutations, creando un item

En el Dashboard, tenemos en la parte de abajo del explorer un selector `ADD NEW` que nos permite agregar una nuava `Query`, `Mutation` o `Subscription`. Elegimos Mutation para los ejemplos que vienen a continuación:

- a. Elegimos agregar mutation, podemos cambiar el nombre que nos dan por default:

![Imagen agregar mutation](assets/img-011.png)

- b. Para insertar múltiples fotos, podemos usar el operador *insert_photos*, el cual nos permite hacer armar un array de _objects_, como podemos observar en returning, nos está devolviendo un array de objects:

![Imagen con returning en forma de array](assets/img-012.png)

- c. Lo que nos deja ver que en la mutation, podemos crear un objects con un array de objects en vez de un siempl object:

![Imagen de mutation en forma de array](assets/img-013.png)

- __NOTA!__ Existe un caso particular muy útil en el que podemos usar la _mutation_ de _insert_ para modificar alguna `id` existente.
- d. Si lo quisiéramos hacer de manera normal, obtendríamos como resultado un conflicto por `constraint-violation`, como se muestra en la siguiente imagen:

![Imagen con constraint-violation](assets/img-014.png)

- e. Pero existe un método llamado _absort_ en Hasura, que nos da la oportunidad de que en caso de _conflicto_, la _insertion_ haga un _update_, y este por default, Hasura lo integra como un string:

![Imagen con on_conflict activado](assets/img-015.png)

- f. Si quiéramos que el _absort_, nos permitiera lograr editar más de un _row_, es posible envolviendo el operador *update_column* en un array:

![Imagen con update_column como array](assets/img-016.png)

### Mutations, update items

Hasura nos trae una manera de hacer _update_, y esto lo hace creando los métodos necesarios para que los tengamos disponibles desde que se crean las tablas y relaciones.

- a. El operador básico para hacer esto es *update_by_pk* que nos pide como valor requerido el `id` y los rows que deseemos afectar, como se muestra en la siguiente imagen:

![Imagen para actualizar fields](assets/img-017.png)

- b. Existe otro operador creado por Hasura llamado _update_ que nos permite hacer updates basado en _diferentes variable_, en la siguiente imagen se hace un ejemplo donde elegimos actualizar el field description para las fotos creadas después de una fecha dada:

![Imagen actualizando la descripción de varias fotos al mismo tiempo](assets/img-018.png)

### Mutations, eliminando items

Para esta sección, se puede tomar toda la información brindada en update, ya que la funcionalidad es la misma, existen dos métodos que Hasura nos da: *delete_by_pk* y _delete_ (que lo hace de manera condicional y puede hacerlo de manera múltiple en una sola instrucción).

### Mutations, mutando items con relaciones

De la misma manera que las mutations usadas para crear photos, podemos incluir dentro de la misma el operador para agregar un comentario, como se muestra en la siguiente imagen:

![Imagen creando photo con comment](assets/img-019.png)

### Mutations, usando variables

Como es bien conocido en GraphQL, es posible darle dinamismo a las mutations a través de las variables, esto lo podemos hacer de la siguiente manera:

- a. Lo primero es definir en formato json nuestras variables a usar en la sección de abajo de GraphiQL en el Dashboar -> API
- b. Lo siguiente a hacer es incluir las variables en el nombre de nuestra Mutation, añadiendo el tipo de variable a que corresponde (para saber el tipo de variables, podemos abrir la sección de Docs y elegir de la lista que nos extiende Hasura GraphiQL)
- c. Ahora, para finalizar, toca el turno de incluir las variables, donde anteriormente hardcodeábamos los valores que deseábamos mandar en los operadores.

![Imagen con una mutation usando variables](assets/img-020.png)

### Subscriptions, queries en tiempo real

Hasura nos permite realizar esta función de forma fácil. Tomamos alguna de las _queries_ que tenemos previamente en el _historial_, por ejemplo la de `GetPhotos` en anteponemos en vez de `Query` la palabra reservada `Subscription`, listo! Ya tenemos nuestra subscription oyendo los cambios en _tiempo real_ de la información que modifiquemos.

![Imagen con ejemplo de una subscription en ejecución](assets/img-021.png)

## Extendiendo la lógica de negocios que ofrece Hasura

En esta sección se verá la manera de extender el set de funcionalidades básicas que ofrece Hasura, con herramientas como:

- Event Triggers
- Hasura Actions
- Remote Schemes
- Cloud Functions (Google Firebase)

### Configurando las Firebase Cloud Functions

Estaremos viendo los pasos para tener configurada una app de Firebase con Cloud Functions.

- Accedemos al [Dashboard de Firebase](https://firebase.google.com)
- Creamos un proyecto nuevo y lo nombramos (deshabilitamos las analíticas)
- Ya dentro del Dashboard de nuestro proyecto, damos click a la sección de Functions -> Get Started
- También activamos nuestro Storage en el Dashboard -> Storage -> Get Started, elegimos la ubicación de nuestro Cloud Storage y listo
- Instalamos el set de tools de firebase `npm i -g firebase-tools`
- Hacemos login a Firebase desde nuestro local `firebase login`
- Creamos un proyecto en nuestro local (dentro de nuestro folder del proyecto) `firebase init`
  - Elegimos las dos opciones de la imagen: Functions y Storage (con la barra espaciadora a ambos) y ENTER

![Imagen de la sleccion de Functions y Storage](assets/img-022.png)

  - Usamos un proyecto existente y elegimos el que creamos en los pasos anteriores
  - Elegimos el lenguaje que queremos para nuestro proyecto (para este elegiremos TypeScript)
  - Seleccionamos que nos cree las reglas de ESLint necesarias
  - Seleccionamos que nos instale las dependencias de npm
  - Ahora (habiendo previamente activado el Storage en Firebase). te pedirá aplicar las reglas de Firebase Storage, elegimos las reglas que nos muestra por default
  - Listo
- Ahora en nuestro editor de código, entramos a la carpeta src y en index.ts podemos crear nuestra primera function. Podemos iniciar quitando los comments de la que viene por default
- En la Terminal, corremos `firebase deploy --only functions`
- Listo! Una vez finalizado el deploy, podemos entrar a la url que nos generar y comprobar que efectivamente está el mensaje default que enviamos por Firebase Functions
- __NOTA!__ Puede que al entrar al index.ts de src les mande un error el import y en el .eslintrc.js el module.exports también, puede ser de utilidad la información de este [LINK](https://stackoverflow.com/a/64940811/7990582)

### Hasura, Event Triggers

Es hora de preparar el Event Trigger para cuando se realice un comentario a alguna de las fotos que se han subido:

- En el Dashboard de Hasura -> Events -> Event Trigger (Create), llenamos los campos acorde a lo que deseamos realizar, pero, antes, para que el ejemplo lo podamos llevar a cabo en nuestro local, vamos a servir el proyecto en nuestro local: nos movemos al folder functions en la Terminal y corremos `npm run serve`, una vez ejecutado, obtenemos la url que nos genera en nuestro local:

![Imagen del servidor local de Firebase Functions](assets/img-023.png)

- Ahora si, llenamos los inputs de Hasura Events, se muestra un ejemplo a continuación:

![Imagen de Hasura Events con inputs](assets/img-024.png)

- a. Ponemos el nombre de nuestro evento
- b. Elegimos la tabla que queremos que active el evento
- c. La operación que ejecutará el evento
- d. Pegamos la URL que nos generó el servidor local, solo que hacemos algunas modificaciones, dejándolo de la siguiente manera: `http://host.docker.internal:5001/hasura-dmytro-project/us-central1/notifyAboutComment`
  - Se realizaron dos cambios, el primero: cambiar el nombre de la función a la que vamos a crear en el siguiente punto de este tutorial
  - El segundo: se cambió el `localhost` por `host.docker.internal`, ya que _Docker_ usa un _localhost_ diferente y la manera correcta de hacerlo es con el cambio implementado.
- Listo! A continuación se muestra la pantalla una vez creado el Event Trigger:

![Imagen Event Trigger creado](assets/img-025.png)

### Hasura, Event Triggers - Implement Event Logic

Iniciamos instalando algunas dependencias necesarias para enviar mail al momento de recibir el evento de que un comentario fue agregado.

- Instalamos `npm install node-fetch nodemailer @types/node-fetch @types/nodemailer`
- Creamos nuestra función, se adjunta un ejemplo de cómo se modifico la existente de Hello World:

```ts
import * as functions from 'firebase-functions';

export const notifyAboutComment = functions.https.onRequest((request, response) => {
  functions.logger.info('Request body', request.body);
  response.send('Hello from Firebase!');
});
```

- Volvemos a correr la función de firebase `npm run serve`
- Nos movemos al Dashboard de Hasura y creamos un comment a alguna de las fotos
- Verificamos en el TAB Events -> Logs que el evento se haya invocado y podemos ver los detalles del request y la respuesta
- Ahora vamos a modificar nuestra function, se deja el código ejemplo para recibir un email cuando se agregue un comment a alguna de las fotos:

```ts
// Dependencies
import * as functions from 'firebase-functions';
import fetch from 'node-fetch';
import { createTestAccount, createTransport, getTestMessageUrl } from 'nodemailer';

const GET_PHOTO_QUERY = `
  query GetPhotoById($id: uuid!) {
    photos_by_pk(id: $id) {
      photo_url
      description
    }
  }
`;

export const notifyAboutComment = functions.https.onRequest(
  async (request, response) => {
    try {
      const { event } = request.body;
      const { photo_id, comment } = event?.data?.new;
      const { session_variables } = event;

      const photoInfoQuery = await fetch('http://localhost:8080/v1/graphql', {
        method: 'POST',
        body: JSON.stringify({
          query: GET_PHOTO_QUERY,
          variables: { id: photo_id }
        }),
        headers: { ...session_variables, ...request.headers }
      });

      const {
        data: {
          photos_by_pk: {
            photo_url,
            description
          }
        }
      } = await photoInfoQuery.json();

      const testAccount = await createTestAccount();
      const transporter = createTransport({
        host: 'smtp.ethereal.email',
        port: 587,
        secure: false, // true for 465, false for other ports
        auth: {
          user: testAccount.user, // generated etheral user
          pass: testAccount.pass // generated etheral pass
        }
      });

      const sentEmail = await transporter.sendMail({
        from: `"Firebase function" <${testAccount.user}>`,
        to: 'rzarate.alex@gmail.com',
        subject: 'New comment to the photo',
        html: `
          <html>
            <head></head>
            <body>
              <h1>Hi there!</h1>
              <br> <br>
              <p>You have a new comment to your photo: <a href="${photo_url}">${description}</a></p>
              <p>The comment text is: <i>${comment}</i></p>
            </body>
          </html>
        `
      })

      functions.logger.log('getTestMessageUrl: ', getTestMessageUrl(sentEmail));

      response.status(200).send({ message: 'success!' })
    } catch (error) {
      response.status(500).send({ message: `Message: ${error.message}` })
    }
});
```
