# PRACTICA DOCKERHUB

## PASO 2 (CONTAINERIZE AN APPLICATION)

Debemos clonar el repositorio de la aplicacion usando el siguiente comando.

```
git clone https://github.com/docker/getting-started-app.git
```

Clonamos el repositorio dentro de alguna carpeta local en nuestra maquina para luego editar el codigo fuente.

<img src="/img Marco/gitClone.png" alt="gitclone command" width="1000"/>

<br>
Realizada la clonacion del repositorio, podemos abrir el proyecto dentro de nuestro editor de codigo de preferencia (para este caso vs code), y podemos mirar los archivos con los que vamos a trabajar.

<br> 

<img src="/img Marco/archivos.png" alt="gitclone command" width="300"/>

<br>

### Construir la imagen de la aplicacion

Ahora vamos a contruir la imagen de la aplicacion necesitaremos  utilizar un Dockerfile. Un Dockerfile es simplemente un archivo basado en texto sin extensión de archivo que contiene un script de instrucciones. Docker usa este script para crear una imagen de contenedor.

Debemos estar seguros de tener la ruta correcto en este caso es la ruta donde estan alojados todos los archivos de la app.

```
C:\Users\MARCO\OneDrive\Escritorio>
```
```
C:\Users\MARCO\OneDrive\Escritorio>cd getting-started-app
```

dentro de la ruta correcta vamos a crear un nuevo archivo vacio llamado Dockerfile.

```
C:\Users\MARCO\OneDrive\Escritorio\getting-started-app>type nul > Dockerfile
```

Ahora usando un editor de codigo vamos a editar el nuevo archivo **Dockerfile** asi como se muestra en la siguiente imagen.

<img src="/img Marco/Dockerfile.png" alt="gitclone command" width="500"/>
<br>
Dentro del Dockerfile ponemos las dependencias en particular de las cuales se apoya la app para desplegar su funcionamiento.

Para construir la imagen usamos el siguiente comando en terminal:

```
C:\Users\MARCO\OneDrive\Escritorio\getting-started-app>type nul > docker build -t getting-started .
```

tener en cuenta el punto al final.

Luego de correr el comando anterior se contruira la imagen de docker en base al archivo Dockerfile que creamos antes.

<img src="/img Marco/ConsImagen.png" alt="gitclone command" width="700"/>

<br>

### iniciar un contenedor de aplicaciones

Ya tenemos una imagen con todo lo necesario para correr una app, vamos a correr dicha imagen con el comando **docker run** especificando el nombre de la imagen.

```
docker run -dp 127.0.0.1:3000:3000 getting-started
```

<img src="/img Marco/runDocker.png" alt="gitclone command" width="500"/>
<br>
El comando va a construir un contenedor y desplegar nuestra app ademas de generar un SHA 256 indentificador, ahora podemos abrir dentro de un navegador de nuestra eleccion la ruta correspondiente al local host y ver nuestra app en funcionamiento

[localhost](http://localhost:3000) 

<img src="/img Marco/app.png" alt="gitclone command" width="500"/>

<br>

## PASO 3 (Modificar la aplicacion)

Debemos ir a el editor de texto y hacer los cambios que se deseen, en este caso, se cambiaron las líneas 56, 99 y 109 del archivo app.js para que quedara en español.

<img src="/Img Luis/Update.JPG " alt="gitclone command" width="1000"/>

<br>
Luego se crea la nueva versión de la imagen utilizando el comando:

```
$ docker build -t getting-started .
```
Y iniciamos el nuevo contenedor con el código actualizado, utilizando nuevamente 
```
$ docker run -dp 127.0.0.1:3000:3000 getting-started
```
Pero para evitar el siguiente error:
<img src="/Img Luis/Update2.JPG " alt="gitclone command" width="1000"/>
 es necesario remover el anterior contenedor que está corriendo también en el puerto 3000 y solo un proceso especifico puede ocupar un puerto.
Para remover el contenedor que se encuentra corriendo se debe:
1)	Obtener el Id de este con el comando:
   
```
$ docker ps
```

2)	Detener el contenedor con el comando:
```
$ docker stop <the-container-id>
```
3)	Remover el contenedor con el commando: 

```
$ docker rm <the-container-id>
```

Ahora es possible iniciar el contendor modificado sin problema.
<img src="/Img Luis/Update3.JPG " alt="gitclone command" width="1000"/>
Resultado: 
<img src="/Img Luis/Update4.JPG " alt="gitclone command" width="1000"/>


## PASO 4 (SHARE THE APPLICATION)

Ahora que hemos creado una imagen, podemos compartirla. Para compartir imágenes de Docker, debemos utilizar un registro de Docker. El registro predeterminado es **DockerHub** y es de donde provienen todas las imágenes que se han  utilizado.

### Crear un repositorio

1. inicie sesión en **DockerHub**

2. Seleccione el botón Crear repositorio .

3. Para el nombre del repositorio, utilice getting-started. Asegúrese de que la visibilidad sea pública .

4. Seleccione **Crear**.

### Push (empujar la imagen)

1. Inicie sesion en **DockerHub** desde la linea de comandos

```
docker login -u YOUR-USER-NAME
```

<img src="/img Marco/loginDockerhub.png" alt="gitclone command" width="400"/>
<br>

1. Utilice el **docker tag** comando para darle a la imagen getting-started  un nuevo nombre. Reemplácelo con su nombre de usuario y con su ID de Docker.

```
docker tag getting-started marcomn7/getting-started
```

3. Ahora ejecutemos el comando push

```
docker push marcomn7/getting-started
```
<img src="/img Marco/push.png" alt="gitclone command" width="700"/>
<br>

el comando se encargara de empujar la imagen al nuevo repositorio de **DockerHub**, podemos revisar la correcta ejecucion dentro del mismo **DockerHub**

<img src="/img Marco/dockerhub.png" alt="gitclone command" width="800"/>

## PASO 5 (Persistir la base de datos)
Cada contenedor cuenta con su propio espacio temporal por lo que aunque se utilice la misma imagen los cambios no se guardaran.
La manera en la que se suguiere que se vea esto es con el siguiente comando: 
```
$ docker run -d ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
```
<img src="/Img Luis/DB1.JPG " alt="gitclone command" width="1000"/>

La idea es usar este comando para crear un contenedor en Ubuntu que crearía un numero aleatorio del 1 al 10000 y verificar revisando el archivo data.txt de la misma imagen, pero en otro contenedor. Con el siguiente comando identificamos que las dos imágenes no tienen los mismos archivos.
```
docker run -it ubuntu ls /
```
<img src="/Img Luis/DB2.JPG " alt="gitclone command" width="1000"/>

Conservar los datos.
 La imagen que se creo trabaja de forma predeterminada con una base de datos llamada SQLite, esta guarda los datos en un archivo, la manera en la que se pueden conservar los datos es creando un Volumen y adjuntándolo con el comando mount 
Primer se crea un volumen usando el comando 
```
docker volume create todo-db
```
Luego se detiene y elimina el contenedor

<img src="/Img Luis//DB2.5.JPG " alt="gitclone command" width="1000"/>

para volver a montar la imagen Utilizando el comando: 
```
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started 
```
<img src="/Img Luis//DB3.JPG " alt="gitclone command" width="1000"/>

Y se realizand las pruebas para ver si se estan conservando los datos. 
<img src="/Img Luis/DB4.JPG " alt="gitclone command" width="1000"/>
<img src="/Img Luis/DB5.JPG " alt="gitclone command" width="1000"/>
<img src="/Img Luis/Db6.JPG " alt="gitclone command" width="1000"/>

## PASO 6 (USE BIND MOUNTS)   

Diferencias entre montajes de volumen y montajes de enlace.

| Aspecto                            | Named volumes                                       | Bind mounts                   |
|-----------------------------------|-----------------------------------------------------|-------------------------------|
| Ubicación en el host              | Docker elige la ubicación                           | Tú decides                    |
| Ejemplo de montaje (usando --mount)| type=volume, src=my-volume, target=/usr/local/data | type=bind, src=/path/to/data, target=/usr/local/data |
| Puebla el nuevo volumen con el contenido del contenedor | Sí                                                 | No                            |
| Soporta controladores de volumen   | Sí                                                 | No                            |

## probando montajes con enlaces

1. en una terminal aseguremos que estamos parados sobre la ruta correcta. en este caso la carpeta getting-started
   <br>
2. Ejecutamos el siguiente comando para comenzar bash en un ubuntu contenedor con un montaje de enlace.
<br>

```
docker run -it --mount "type=bind,src=$pwd,target=/src" ubuntu bash
```

>La **--mount** opción le dice a Docker que cree un montaje de enlace, donde **src** está el directorio de trabajo actual en su máquina host ( getting-started-app), y **target** es donde ese directorio debe aparecer dentro del contenedor **(/src)**.

Luego de ejecutado el anterior comando en power shell (en CMD dio problemas), nos aparecera una bash de ubuntu.

<img src="/img Marco/bashUbuntu.png" alt="gitclone command" width="700"/>
<br>
3. Dentro de la bash de ubuntu cambiemos al directorio src, Este es el directorio que montó al iniciar el contenedor. Al enumerar el contenido de este directorio se muestran los mismos archivos que en el getting-started-app directorio de su máquina host.
<br>
<img src="/img Marco/archivosUbuntu.png" alt="gitclone command" width="500"/>
<br>
4. Creamos un nuevo archivo llamado myfile.txt y observemos si se encuentra o no dentro de el directorio.
<img src="/img Marco/myfile.png" alt="gitclone command" width="500"/>
<br>
5. Volvamos a la terminal donde estuvimos antes del docker run y vemos que ahora tenemos acceso al mismo archivo dentro de getting-start-app.
<br>
<img src="/img Marco/mifileEnlace.png" alt="gitclone command" width="500"/>
<br>

Esto se debe a que hemos enlasado los datos del volumen de un contenedor de ubuntu con el host principal de nuestra maquina local.

> **Si deseamos eliminar el archivo myfile.txt podemos simplemente eliminar el archivo en el host principal o en el contenedor y el cambio se vera reflejado en ambos**

## Ejecutar nuestra app en un contenedor de desarrollo 

> El uso de montajes vinculados es común para las configuraciones de desarrollo local. La ventaja es que la máquina de desarrollo no necesita tener instalados todos los entornos y herramientas de compilación. Con un solo comando de ejecución de Docker, Docker extrae dependencias y herramientas.

- Monte su código fuente en el contenedor
- Instalar todas las dependencias
- Comience nodemon para observar los cambios en el sistema de archivos

1. Debemos asegurarnos de no tener ningun contenedor con nuestra app ejecutandose.
2. Ejecutemos el siguiente comando desde el getting-started-app directorio. en powershell debido a que CMD nos da problemas.

```
docker run -dp 127.0.0.1:3000:3000 `
    -w /app --mount "type=bind,src=$pwd,target=/app" `
    node:18-alpine `
    sh -c "yarn install && yarn run dev"
```
<br>
<img src="/img Marco/dockerDesarrollo.png" alt="gitclone command" width="600"/>
<br>


3. Podemos hacer uso de los logs o registros del contenedor para saber en que estado se encuentra.

```
docker logs -f <container-id>
nodemon src/index.js
[nodemon] 2.0.20
[nodemon] to restart at any time, enter `rs`
[nodemon] watching dir(s): *.*
[nodemon] starting `node src/index.js`
Using sqlite database at /etc/todos/todo.db
Listening on port 3000
```

## PASO 7 (Aplicación multi contenedor)
Se va a empezar a utilizar las aplicaciones multicontenedor, esto puede ser por varios motivos: 
-	Mejor escalabilidad 
-	Permite versionar y actualizar de forma aislada 
-	Separación de la aplicación y el motor de base de datos.
En este caso se utilizara MySQL.
Primero se crea la red:
```
docker network create todo-app
```
Despues se inicia el contenedor MySQL, se conecta a la red y se definen variables de entorno:
```
$ docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:8.0
```
<img src="/Img Luis/Multi.JPG " alt="gitclone command" width="1000"/>

Y luego se verifica si la base de datos quedo en funcionamiento.
<img src="/Img Luis/Multi2.JPG " alt="gitclone command" width="1000"/>

como cada contenedor tiene su propia direccion ip, la manera de utilizar SQL  en este caso es con nicolaka/netshoot que contiene varias herramientas para solucionar y depurar problemas de red

<img src="/Img Luis/multi3.JPG " alt="gitclone command" width="1000"/>

Dentro del contenedor,  se usara el comando DIG , que es una herramienta DNS útil
<img src="/Img Luis/multi4.JPG " alt="gitclone command" width="1000"/>

En la seccion ANSWER SECTION: se va a ver un registro A que mostrara la direccion ip a la que se debe conectar la base de datos, pero como anteriormente se utilizo el comando --network-alias
solo es necesario conectarse al host llamado mysql

## Ejecutar la aplicacion con MYSQL

para esto debemos detener la imagen y removerla. para posteriormente volverla a correr pero especificando las variables de entorno para conectar ambos contenedores, El de MYSQL con la imagen que se ha estado
utilizando:

<img src="/Img Luis/multi5.JPG " alt="gitclone command" width="1000"/>

y con el comando "docker logs -f <container-id>" se vera un mensaje que indica que se esta utilizando la base de datos

<img src="/Img Luis/multi6.JPG " alt="gitclone command" width="1000"/>

Ahora solo queda probar la aplicacion y verificar si los registros estan quedando guardados en la base de datos:

<img src="/Img Luis/multi7.JPG " alt="gitclone command" width="1000"/

<img src="/Img Luis/multi8.JPG " alt="gitclone command" width="1000"/>

<img src="/Img Luis/multi9.JPG " alt="gitclone command" width="1000"/>


## PASO 8 (USE DOCKER COMPOSE)

>La gran ventaja de usar Compose es que puede definir la pila de su aplicación en un archivo, mantenerla en la raíz del repositorio de su proyecto (ahora tiene control de versión) y permitir fácilmente que otra persona contribuya a su proyecto. Alguien sólo necesitaría clonar su repositorio e iniciar la aplicación usando Compose. De hecho, es posible que veas bastantes proyectos en GitHub/GitLab haciendo exactamente esto ahora.

1. En el directorio getting-started-app vamos a crear un archivo  llamado **compose.yaml**.

<img src="/img Marco/compose.png " alt="gitclone command" width=""/>
<br>

### Definir el servicio de la aplicacion

en la parte anterior usamos el siguiente comando para defirnir variables de entorno y el servicio de **mySQL** 

```
$ docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:8.0
```

Ahora vamos a definir este servicio dentro de nuestro nuevo archivo **compose.yaml** 

- comenzamos definiendo el nombre y la imagen del primer servicio (o contenedor) que desea ejecutar como parte de su aplicación. El nombre se convertirá automáticamente en un alias de red, lo que será útil al definir su servicio **MySQL**.

```
services:
  app:
    image: node:18-alpine
```
- Por lo general, verá **command** cerca de la image definición, aunque no hay ningún requisito para realizar el pedido. Agregue el command a su archivo compose.

```
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
```

- Ahora migre la **-p 127.0.0.1:3000:3000** parte del comando definiendo el puerto para el servicio.

```
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
```

- A continuación, migre tanto el directorio de trabajo **( -w /app)** como la asignación de volumen **( -v "$(pwd):/app")** utilizando las definiciones **working_dir** y **.volumes**

> Una ventaja de las definiciones de volúmenes de Docker Compose es que puede utilizar rutas relativas desde el directorio actual.

```
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
```

- Finalmente, necesita migrar las definiciones de variables de entorno usando la palabra clave **environment**.

```
services:
  app:
    image: node:18-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 127.0.0.1:3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos
```

### Definir el servicio de mySQL

Dentro del mismo **compose.yaml** vamos a definir el servicio de **MYSQL**, para especificar las variables de entorno usadas por el servicio de bd en este caso **MYSQL**

- definimos el nuevo servicio y asígnamos un nombre para que obtenga automáticamente el alias de red. También especificamos la imagen que se utilizará.

```

services:
  app:
    # Definicion de la APP
  mysql:
    image: mysql:8.0
```

- A continuación, defina la asignación de volumen. Cuando ejecutó el contenedor con docker run, Docker creó el volumen nombrado automáticamente. Sin embargo, eso no sucede cuando se ejecuta con Compose. Debe definir el volumen en la seccion **volumes:** y luego especificar el punto de montaje en la configuración del servicio. Simplemente proporcionando solo el nombre del volumen, se utilizan las opciones predeterminadas.

```
services:
  app:
    # The app service definition
  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql

volumes:
  todo-mysql-data:
```

- Finalmente especificamos las variables de entorno

```
services:
  app:
    # The app service definition
  mysql:
    image: mysql:8.0
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

- Ya realizados los pasos anteriores nuestro archivo compose.yaml deberia verse asi:

<img src="/img Marco/ComposeRel.png " alt="gitclone command" width="400"/>
<br>

### Ejecutar la pila de aplicaciones

> Ahora que tenemos nuestro **compose.yaml** podemos automatizar contenedores y ejecutar servicios

1. Debemos asegurarnos de no tener en ejecucion copias de los contenedores usamos **docker ps** para enumerar los contenedores y docker **rm -f id** eliminarlos.

<img src="/img Marco/dockerps.png " alt="gitclone command" width="500"/>
<br>

2. Iniciamos la pila de aplicaciones usando el comando **docker compose** up. Agregue  **-d**  para ejecutar todo en segundo plano.
(recordemos estar dentro de la ruta del proyecto).

<img src="/img Marco/compseUp.png " alt="gitclone command" width="700"/>
<br>

> Notaremos que Docker Compose creó el volumen y también una red. De forma predeterminada, Docker Compose crea automáticamente una red específica para la pila de aplicaciones (razón por la cual no definió una en el archivo Compose).

**podemos ver los volumenes que se crearon desde _Docker desktop_**

<img src="/img Marco/volumenes.png " alt="gitclone command" width="700"/>
<br>

**podemos ver los registros de accion con el comando   _docker compose logs -f_**

```
mysql_1  | 2019-10-03T03:07:16.083639Z 0 [Note] mysqld: ready for connections.
mysql_1  | Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
app_1    | Connected to mysql db at host mysql
app_1    | Listening on port 3000
```

> Ahora podemos abrir el localhost de nuestra maquina y ver la app en funcionamiento, pudimos ver como Docker compose nos ayuda a simplificar la forma de definir y compartir aplicaciones _multiservicio_

## PASO 9 Mejores practicas para construir imagenes

se puede utilizar el historial de imagenes de docker para ver cada capa creada dentro de una imagen con el comando 
```
docker image history "Nombre"
```
<img src="/Img Luis/BestPrac1.JPG " alt="gitclone command" width="1000"/>

y al agregar la linea --not-trunc se puede ver el resultado completo
```
docker image history --no-trunc "Nombre"
```

## Almacenamiento en caché de capas

las capas juegan un papel importante en la reduccion del tiempo de compilacion de las imagenes. Ya que como se tiene el Dockerfile actualmente cada vez que una capa cambia todas deben reestructurarse
y las dependencias del hilo se tuvienen que reinstalar. para evitar eso se debe cambiar el contenido del Dockerfile.

Pasarlo de esto:
# syntax=docker/dockerfile:1
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]

a esto: 

<img src="/Img Luis/BestPrac2.JPG " alt="gitclone command" width="1000"/>

y crear un archivo nuevo llamado ".dockerignore" con el siguiente contenido:

<img src="/Img Luis/BestPrac3.JPG " alt="gitclone command" width="1000"/>

de esta manera solo se copian selectivamente los archivos relevantes de imágenes y cuando se realicen actuaizaciones envios y extracciones sera mucho mas rapido.
HTM
A continuacion se muestra como cambian los tiempos de compialcion despues de hacer un cambio en un archivo HTML:

<img src="/Img Luis/BestPrac4.JPG " alt="gitclone command" width="1000"/>

<img src="/Img Luis/BestPrac5.JPG " alt="gitclone command" width="1000"/>

<img src="/Img Luis/BestPrac6.JPG " alt="gitclone command" width="1000"/>







