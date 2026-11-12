# Documentación de ejecución reproducible de una aplicación contenerizada

## 1. Descripción del proyecto

Este proyecto consiste en una API REST desarrollada con Node.js y Express, conectada a una base de datos MongoDB mediante Mongoose. La aplicación está contenerizada utilizando Docker y Docker Compose para facilitar su construcción, ejecución y reproducción en diferentes equipos.

La API permite realizar operaciones CRUD (crear, consultar, actualizar y eliminar) sobre registros de elementos.

## 2. Tecnologías utilizadas

- Node.js 20
- Express 4.19.2
- MongoDB 7.0
- Mongoose 8.3.1
- Docker
- Docker Compose

## 3. Requisitos previos

Para ejecutar el proyecto se necesita:

- Docker Desktop instalado.
- Docker Compose disponible.
- Git instalado.
- Visual Studio Code (opcional).
- Sistema operativo Windows, Linux o macOS.

## 4. Estructura del proyecto

```text
trabajo-autonomo-s2/
│
├── src/
│   └── app.js
│
├── .env
├── .env.example
├── .gitignore
├── Dockerfile
├── docker-compose.yml
├── package.json
└── README.md
```

## 5. Preparación de variables de entorno

El proyecto utiliza variables de entorno para configurar el puerto de ejecución y la conexión con MongoDB.

Primero se debe crear el archivo `.env` a partir del archivo de ejemplo.

En Windows PowerShell:

```powershell
Copy-Item .env.example .env
```

Contenido del archivo `.env.example`:

```env
PORT=4000
MONGO_INITDB_ROOT_USERNAME=admin
MONGO_INITDB_ROOT_PASSWORD=ejemplo_password_123
DATABASE_URL=mongodb://admin:ejemplo_password_123@mongodb:27017/app_db?authSource=admin
JWT_SECRET=secreto_de_ejemplo_no_utilizar_en_produccion
```

Las variables utilizadas son:

| Variable                   | Descripción                                          |
| -------------------------- | ---------------------------------------------------- |
| PORT                       | Puerto del servidor Node.js.                         |
| MONGO_INITDB_ROOT_USERNAME | Usuario administrador de MongoDB.                    |
| MONGO_INITDB_ROOT_PASSWORD | Contraseña de MongoDB.                               |
| DATABASE_URL               | Dirección de conexión a MongoDB.                     |
| JWT_SECRET                 | Variable reservada para un secreto de autenticación. |

**Importante:** El archivo `.env` contiene las credenciales locales y no debe publicarse en GitHub.

## 6. Construcción e inicio de la aplicación

Una vez preparado el archivo `.env`, abrir una terminal en la raíz del proyecto.

Construir la imagen e iniciar los contenedores:

```powershell
docker compose up -d --build
```

Este comando construye la imagen de Node.js e inicia los servicios de la aplicación y MongoDB.

Para comprobar el estado:

```powershell
docker compose ps
```

Para consultar los registros:

```powershell
docker compose logs
```

Para consultar únicamente el servidor:

```powershell
docker compose logs app
```

La aplicación estará disponible en:

```text
http://localhost:4000
```

## 7. Inicialización de la base de datos

MongoDB se inicia automáticamente mediante Docker Compose.

El usuario administrador se configura mediante las variables de entorno del servicio MongoDB.

La base de datos utilizada por la aplicación es:

```text
app_db
```

La conexión se realiza mediante Mongoose utilizando la variable `DATABASE_URL`.

Para comprobar el contenedor de MongoDB:

```powershell
docker compose exec mongodb mongosh -u admin -p password123 --authenticationDatabase admin
```

Dentro de MongoDB ejecutar:

```javascript
show dbs
```

Seleccionar la base de datos:

```javascript
use app_db
```

Consultar las colecciones:

```javascript
show collections
```

La colección `items` se crea automáticamente cuando se almacena el primer registro mediante la API.

Para salir:

```javascript
exit;
```

Nota: En una instalación nueva, utilizar las credenciales configuradas en el archivo `.env`. Las variables de inicialización de MongoDB se aplican cuando se crea un volumen de datos nuevo.

## 8. Ejemplos de solicitudes CRUD

La API utiliza la siguiente ruta principal:

```text
http://localhost:4000/api/items
```

### 8.1 Crear un registro (POST)

```powershell
Invoke-RestMethod -Method POST `
-Uri "http://localhost:4000/api/items" `
-ContentType "application/json" `
-Body '{"name":"Producto de prueba","status":"Activo"}'
```

La aplicación guarda el registro y devuelve el elemento creado con su identificador.

### 8.2 Consultar registros (GET)

```powershell
Invoke-RestMethod -Method GET `
-Uri "http://localhost:4000/api/items"
```

Devuelve los registros almacenados en MongoDB.

### 8.3 Actualizar un registro (PUT)

Reemplazar el identificador por el ID del registro que se desea modificar.

```powershell
$id = "ID_DEL_REGISTRO"

Invoke-RestMethod -Method PUT `
-Uri "http://localhost:4000/api/items/$id" `
-ContentType "application/json" `
-Body '{"name":"Producto actualizado","status":"Completado"}'
```

La operación actualiza los datos del registro seleccionado.

### 8.4 Eliminar un registro (DELETE)

```powershell
$id = "ID_DEL_REGISTRO"

Invoke-RestMethod -Method DELETE `
-Uri "http://localhost:4000/api/items/$id"
```

Si el registro existe, devuelve un mensaje de confirmación de eliminación.

## 9. Detención de los contenedores

Para detener los servicios:

```powershell
docker compose down
```

Este comando detiene y elimina los contenedores y la red, pero conserva el volumen de datos de MongoDB.

Para iniciar nuevamente:

```powershell
docker compose up -d
```

Para reconstruir la imagen:

```powershell
docker compose up -d --build
```

## 10. Errores frecuentes y soluciones

### Error 1: No se puede conectar a MongoDB

**Causa:** Las credenciales de conexión son incorrectas o el servicio MongoDB no está disponible.

**Solución:**

- Revisar la variable `DATABASE_URL` del archivo `.env`.
- Comprobar que las credenciales coincidan con las utilizadas al inicializar el volumen de MongoDB.
- Verificar el estado del contenedor:

```powershell
docker compose ps
```

### Error 2: Puerto 4000 ocupado

**Causa:** Otro programa está utilizando el puerto 4000 del equipo.

**Solución:**

Identificar el proceso:

```powershell
netstat -ano | findstr :4000
```

Detener el programa que utiliza el puerto o configurar otro puerto disponible.

### Error 3: Cannot PUT o Cannot POST

**Causa:** La aplicación puede estar ejecutando una versión anterior del código o la solicitud utiliza una ruta incorrecta.

**Solución:**

Reconstruir la aplicación:

```powershell
docker compose up -d --build
```

Consultar los registros:

```powershell
docker compose logs app
```

Comprobar que las rutas utilizadas correspondan a las definidas en `src/app.js`.

## 11. Ejecución reproducible desde una copia limpia

Para comprobar que el proyecto puede ejecutarse en otro equipo, se debe clonar el repositorio desde GitHub.

```powershell
git clone URL_DEL_REPOSITORIO
```

Ingresar a la carpeta:

```powershell
cd trabajo-autonomo-s2
```

Crear el archivo de variables:

```powershell
Copy-Item .env.example .env
```

Revisar los valores de las variables de entorno y guardar el archivo.

Construir e iniciar la aplicación:

```powershell
docker compose up -d --build
```

Comprobar los contenedores:

```powershell
docker compose ps
```

Consultar los registros:

```powershell
docker compose logs app
```

Finalmente, comprobar el funcionamiento de la API mediante las solicitudes CRUD descritas anteriormente.


