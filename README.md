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

### construir la imagen de la aplicacion

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

Ahora usando un editor de codigo vamos a editar el nuevo archivo Dockerfile asi como se muestra en la siguiente imagen.

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

Ya tenemos una imagen con todo lo necesario para correr una app, vamos a correr dicha imagen con el comando docker run especificando el nombre de la imagen.

```
docker run -dp 127.0.0.1:3000:3000 getting-started
```

<img src="/img Marco/runDocker.png" alt="gitclone command" width="500"/>
<br>
El comando va a construir un contenedor y desplegar nuestra app ademas de generar un SHA 256 indentificador, ahora podemos abrir dentro de un navegador de nuestra eleccion la ruta correspondiente al [localhost](http://localhost:3000)