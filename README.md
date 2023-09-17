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

