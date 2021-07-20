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

- Para hacer la paginación, Hasura nos ofrece los operadores limit y offset, dónde limit se encarga de definir la cantidad másxima de elementos que va a devolver la Query y offset nos permite "saltar" la cantidad de valores que deseemos, por ejemplo:

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
