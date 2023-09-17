# Practica DockerHub

## Paso 2 (Containerize an application)

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

<img src="/img Marco/ConsImagen.png" alt="gitclone command" width="500"/>

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

## Paso 4 (Share the application)

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

2. Utilice el **docker tag** comando para darle a la getting-started imagen un nuevo nombre. Reemplácelo con su nombre de usuario y con su ID de Docker.

```
docker tag getting-started marcomn7/getting-started
```

3. Ahora ejecutemos el comando push

```
docker push marcomn7/getting-started
```
<img src="/img Marco/push.png" alt="gitclone command" width="500"/>
<br>

el comando se encargara de empujar la imagen al nuevo repositorio de **DockerHub**, podemos revisar la correcta ejecucion dentro del mismo **DockerHub**

<img src="/img Marco/dockerhub.png" alt="gitclone command" width="600"/>

## paso 6 (use bind mounts)    

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

<img src="/img Marco/bashUbuntu.png" alt="gitclone command" width="500"/>
<br>
3. Dentro de la bash de ubuntu cambiemos al directorio src, Este es el directorio que montó al iniciar el contenedor. Al enumerar el contenido de este directorio se muestran los mismos archivos que en el getting-started-app directorio de su máquina host.
<br>
<img src="/img Marco/archivosUbuntu.png" alt="gitclone command" width="500"/>
<br>
4. Creamos un nuevo archivo llamado **myfile.txt** y observemos si se encuentra o no dentro de el directorio.
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






