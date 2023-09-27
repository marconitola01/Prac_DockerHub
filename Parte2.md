# Segunda parte PracDocker

## Construye tu imagen python:

las imagenes que se crean tienen todo lo que necesita una aplicacion para correr, como codigo, dependencias y mucho otros archivos de sistema.
Se Clona el repositorio para empezar a trabajar

<img src="/Img Luis/Imagen1.JPG" alt="gitclone command" width="1000"/>

(Opcional) probar la aplicacion localmente, crea el ambiente, instala las dependencias.

<img src="/Img Luis/Imagen2.JPG" alt="gitclone command" width="1000"/>

Iniciar la aplicacion.

<img src="/Img Luis/Imagen3.JPG" alt="gitclone command" width="1000"/>

<img src="/Img Luis/Imagen4.JPG" alt="gitclone command" width="1000"/>

### Crear un DockerFile para python
Para hacer esto nos situamos en la la carpeta del proyecto clonado de github y utilizamos el
comando docker init, Este comando crea 3 archivos en la carpeta:
- Dockerfile: se usa para construir la imagen 
- .Dockerignore: se usa para especificar patrones y caminos que no quieran ser copiados en la imagen para mantenerla tan compacta como sea posible
- Compose.yaml: se usa para configurar los servicios de la aplicación

<img src="/Img Luis/Imagen5.JPG" alt="gitclone command" width="1000"/>
<br>

<img src="/img Marco/dockeriniti.png" alt="gitclone command" width="1000"/>

### archivo .dockerignore

al ejecutar **docker init** tambien hemos creado un archivo llamado **.dockerignore**, este mismo dockerignore lo usamos para especificar rutas o patrones que no deseamos implementar dentro de nuestra imagen docker asi podemos llevar un mejor manejo de los recursos y el tamaños de nuestra imagen.


<img src="/img Marco/dockerIgnore.png" alt="gitclone command" width="800"/>

### construir una imagen

Ahora contamos con un **Dockerfile** y podemos contruir una imagen en base al mismo, el **Dockerfile** se genero cuando ejecutamos el comando **docker init**
Construir imagen:
```
docker build --tag python-docker .
```

Resultado:

```
[+] Building 1.3s (12/12) FINISHED
 => [internal] load .dockerignore
 => => transferring context: 680B
 => [internal] load build definition from Dockerfile
 => => transferring dockerfile: 1.59kB
 => resolve image config for docker.io/docker/dockerfile:1
 => CACHED docker-image://docker.io/docker/dockerfile:1@sha256:39b85bbfa7536a5feceb7372a0817649ecb2724562a38360f4
 => [internal] load metadata for docker.io/library/python:3.11.4-slim
 => [base 1/5] FROM docker.io/library/python:3.11.4-slim@sha256:36b544be6e796eb5caa0bf1ab75a17d2e20211cad7f66f04f
 => [internal] load build context
 => => transferring context: 63B
 => CACHED [base 2/5] WORKDIR /app
 => CACHED [base 3/5] RUN adduser     --disabled-password     --gecos ""     --home "/nonexistent"     --shell
 => CACHED [base 4/5] RUN --mount=type=cache,target=/root/.cache/pip     --mount=type=bind,source=requirements.tx
 => CACHED [base 5/5] COPY . .
 => exporting to image
 => => exporting layers
 => => writing image sha256:37f9294069a95e5b34bb9e9035c6ea6ad16657818207c9d0dc73594f70144ce4
 => => naming to docker.io/library/python-docker
```
<img src="/img Marco/dockerbuildpy.png" alt="gitclone command" width="800"/>


### Ver imagenes locales

Para ver una lista de las imagenes locales podemos usar el comando **docker images** y se listaran las imagenes en uso para nuestro sistema.

<img src="/img Marco/dockerimages.png" alt="gitclone command" width="800"/>


### Etiquetar imagenes

El comando **docker tag** crea una nueva etiqueta para una imagen. No crea una nueva imagen. La etiqueta apunta a la misma imagen y es simplemente otra forma de hacer referencia a la imagen.

Agregando etiqueta a una imagen en especial:
```
docker tag python-docker:latest python-docker:v1.0.0
```
podemos listar las imagenes y ver los cambios

<img src="/img Marco/dockerimages2.png" alt="gitclone command" width="800"/>

>**Los cambios se ven reflejados en las imagenes que especificamos en este caso python-docker**

## Ejecutar la imagen com un contenedor
 
> Un contenedor es un proceso normal del sistema operativo, excepto que este proceso está aislado porque tiene su propio sistema de archivos, su propia red y su propio árbol de procesos aislado, separado del host.

Para ejecutar una imagen dentro de un contenedor usamos el comando **docker run** con el parametro que es el nombre de la imagen.

```
docker run python-docker
```

<img src="/img Marco/rundocker2.png" alt="gitclone command" width="800"/>

podemos ver un app que se queda a la espera de solicitudes, esto pasa por que estamos corriendo un app tipo **REST**

<img src="/img Marco/curl.png" alt="gitclone command" width="800"/>

Despues de realizar unas configuraciones podemos realizar solicitudes al localhost:8000 y recibiremos la respuesta anterior del contendor en linea.

<img src="/img Marco/res.png" alt="gitclone command" width="800"/>
Ahora podemos ver en la terminal que habiamos ejecutado antes como el server nos envia las solicitudes que lleguen.

### Ejecutar en modo independiente

Nuestra aplicacion nos esta mostrando un servidor web, pero no es necesario estar conectado al contenedor, docker puede ejecutar el contenedor en modo independiente o en segundo plano.

Usamos el comando --detach p -d para abreviar

```
docker run -d -p 8000:5000 python-docker
```
> Nota: antes de correr el comando recordemos parar los contenedores que tenemos actualmente en funcionamiento ya que estos ocupan el puerto 8000 y nos dara error

<img src="/img Marco/dockerdetach.png" alt="gitclone command" width="800"/>

Docker inició su contenedor en segundo plano e imprimió la identificación del contenedor en la terminal.

Podemos volver a verificar realizando solictudes con el comando Curl al localhost:8000


### Listar contenedores

ahora tenemos a nuestro contenedor ejecutandose en segundo plano, para saber si se esta ejecutando o si tenemos mas contenedores en ejecucion podemos listarlos con el comando **docker ps**

<img src="/img Marco/dockerps2.png" alt="gitclone command" width="800"/>

### Detener, iniciar y renombrar contenedores

Para realizae estas acciones se pueden ejecutar unos comandos sencillos 
Detener un contenedor
```
docker stop <id del contenedor>
```
reiniciar un contenedor
```
docker restart <id del contenedor>
```
Eliminar un contenedor
```
docker rm <nombre del contenedor>
```
iniciar un contenedor
```
docker run <id del contenedor>
```

## Desarrolla tu aplicacion

### Ejecutar una base de datos en un contenedor

En lugar de descargar PostgreSQL, instalar, configurar y luego ejecutar la base de datos PostgreSQL en su sistema directamente, puede usar la imagen oficial de Docker para PostgreSQL y ejecutarla en un contenedor.

antes de ejecutar postgres en un contenedor debemos crear un volumen de almacenamiento.

```
docker volume create db-data
```

Ahora creemos  una red que su aplicación y base de datos utilizarán para comunicarse entre sí.

```
docker network create postgresnet
```
<br>
<img src="/img Marco/dbnet.png" alt="gitclone command" width="800"/>

Ahora odemos ejecutar PostgreSQL en un contenedor y adjuntarlo al volumen y la red que creó anteriormente. 

```
docker run --rm -d `
  --mount type=volume,src=db-data,target=/var/lib/postgresql/data `
  -p 5432:5432 `
  --network postgresnet `
  --name db `
  -e POSTGRES_PASSWORD=mysecretpassword `
  -e POSTGRES_DB=example `
  postgres
```

<img src="/img Marco/installpostgres.png" alt="gitclone command" width="800"/>

Ahora, aseguremonos de que las base de datos PostgreSQL se esté ejecutando y de que podemos conectarnos a ella.

```
docker exec -it db psql -U postgres
```
<img src="/img Marco/postgresdev.png" alt="gitclone command" width="800"/>

>NOTA: El comando anterior inicio sesion en la base de datos de PostgreSQL

### Ejecutar aplicacion de prueba

Vamos a clonar desde el repositorio en github la nueva app que usaremos.

```
git clone https://github.com/docker/python-docker-dev
```
ejecutemos dokcer init dentro de la direccion del proyecto.
```
docker init
```
<img src="/img Marco/dockeriniti2.png" alt="gitclone command" width="800"/>

ejecutemos docer build para contruir la imagen

```
docker build -t python-docker-dev .
```
>NOTA: si tenemos mas contenedores en ejecucion o si esta ocupado el puerto 8000 eliminemoslos.

<img src="/img Marco/dockerbuild2.png" alt="gitclone command" width="800"/>

Vamos a ejecutar docker run con las siguientes opciones para ejeuctar la imagen como un contenedor en la misma red en la que esta la base de datos

```
docker run --rm -d `
  --network postgresnet `
  --name rest-server `
  -p 8000:5000 `
  -e POSTGRES_PASSWORD=mysecretpassword `
  python-docker-dev
```

<img src="/img Marco/dockerRun2.png" alt="gitclone command" width="800"/>

### utilizar compose para desarrollar localmente.

>Cuando ejecutamos docker init, además de un Dockerfile, también se crea un archivo compose.yaml.

>Este archivo de redacción es muy conveniente ya que no es necesario escribir todos los parámetros para pasar al docker comando run. Puede hacerlo de forma declarativa utilizando un archivo Compose.

Ahora vamos a ir nuestra carpeta donde tenemos el proyecto que clonamos de github y actualizamos el archivo compose.yaml

<img src="/img Marco/composeDB.png" alt="gitclone command" width="1000"/>

Podemos analizar el nuevo archivo compose que hemos actualizado y darnos cuenta que este compose especifica un password.txt archivo en elcual se contiene la contraseña de la bd. Debemos crear este archivo  ya que no esta incluido en el repositorio original que clonamos.

> En el directorio del repositorio clonado, cree un nuevo directorio llamado db y dentro de ese directorio cree un archivo llamado password.txtque contenga la contraseña de la base de datos.

<img src="/img Marco/carpetasdb.png" alt="gitclone command" width="200"/>

Dentro del archio **password.txt** vamos a agregar la contraseña en este caso:

```
mysecretpassword
```

Detengamos los contenedores que tengamos en ejecucion

Dentro de la carpeta del proyecto corremos el siguiente comando: 

```
docker compose up --build
```
<img src="/img Marco/composeresd.png" alt="gitclone command" width="1000"/>

<img src="/img Marco/composeresd2.png" alt="gitclone command" width="1000"/>

las dos imagenes anteriores son la salida del comando que corrimos anteriormente, podemos ver como desplegamos un nuevo contenedor que inicio un servidor web y una base de datos.

POdemos probar la conexion a base de datos con los comando siguientes como lo hicimos anteriormente.

```
curl http://localhost:5000/initdb
curl http://localhost:5000/widgets
```

### Configurar CI/CD para la aplicacion.

Ahora vamos a realizar el proceso de configuración y uso de Docker GitHub Actions para crear imágenes de Docker y enviar imágenes a Docker Hub.


1. **Crea un nuevo repositorio en GitHub.**

creamos un repositorio en Github

<img src="/img Marco/git.png" alt="gitclone command" width="1000"/>

El repositorio contiene un archivo DockerFile simple

<img src="/img Marco/DockerFilegit.png" alt="gitclone command" width="1000"/>

Ahora debemos crear un secreto con nombre DOCKERHUB_USERNAME y nuestro ID de Docker

Tambien debemos generar un token de acceso a DockerHub y agregarlo como segundo secreto en nuestro repositorio de Github.

<img src="/img Marco/tokenDockerhub.png" alt="gitclone command" width="1000"/>


<img src="/img Marco/Secrets.png" alt="gitclone command" width="1000"/>




1. **Configurar flujo de trabajo.**

Dentro de nuestro repositorio debemos configurar una accion con la opcion **configurar flujo de trabajo**

Esto nos llevará a una página para crear un nuevo archivo de flujo de trabajo de acciones de GitHub en su repositorio, en forma .github/workflows/main.ymlpredeterminada.



3. **Ejecute el flujo de trabajo.**






## Desarrollando con docker

Docker es un forma eficiente de manejar apliaciones (alojarlas y desplegarlas) con contenedores podemos organizar nuestro codigo tanto de pruebas como de desarrollo, a continuacion vamos a listar buenas practicas de desarrollo con docker.

<img src="/img Marco/docker.webp" alt="gitclone command" width="800"/>

Ahora podemos probar la conexion a la base datos y enumerar los widgets

```
curl http://localhost:8000/initdb
curl http://localhost:8000/widgets
```
Debería recibir el siguiente JSON de su servicio:

<img src="/img Marco/initdb.png" alt="gitclone command" width="1000"/>


<img src="/img Marco/widgets.png" alt="gitclone command" width="1000"/>



## Mejores practicas de desarrollo con docker

### Mantener las imagenes ligeras

Las imagenes mas pequeñas son mas rapidas a la hora de cargarse en memoria al iniciar contenedores o servicios. Para esto existen unas reglas generales para mantener un tamaño optimo de las imagenes.
<br>

1. **Usar imagenes oficiales:**

>Por ejemplo: si hemos destacado la necesidad de contar con **JDK** para un proyecto desplegado en Docker podemos descargar desde docker hub una imagen que contenga este recurso (OPEN JDK) en especifico, en lugar de crear una imagen por nuestra cuenta desde cero.

<img src="/img Marco/openjdk.png" alt="gitclone command" width="1000"/>



<br>

2. **Utilice compilaciones de varias etapas:**

> Dentro de un mismo Dockerfile podemos copiar artefactos que consideramos utiles dentro de de una imagen, esto significa que la imagen final no tiene por que incluir todas las bibliotecas y las  dependencias extraidas por la compilacion, solo contendremos los recursos y el entorno necesarios para ejecutar nuestro desarrollo.

<img src="/img Marco/pythonDock.png" alt="gitclone command" width="1000"/>

Este es un ejemplo de un Dockerfile donde especificamos la version de python, el tipo de volumen de almacen de datos, etc.

> Si necesariamente necesitamos ejecutar una version de Docker que no incluya compilaciones de varias etapas, podemos reducir la cantidad de capas en nuestra imagen minimizando la cantida de comandos a ejecutar por separado **"RUN"**, por ejemplo:



**Antes**
```
RUN apt-get -y update
RUN apt-get install -y python
```
**Despues**
```
RUN apt-get -y update && apt-get install -y python
```

3. **Imagenes con recursos en comun**
>Si tenemos varias imagenes con mucho en comun, podemos crear nuestra propia imagen base con los componentes compartidos y basar sus imagenes unicas en eso. Docker solo necesita cargar las capas comunes una vez y se almacenan en cache.

<img src="/img Marco/798.jpeg" alt="gitclone command" width="800"/>
   <br>

4. **Produccion** 

>Para mantener nuestra imagen de producción optimizada pero permitir la depuración, vamos a usar la imagen de producción como imagen base para la imagen de depuración. Se pueden agregar herramientas de prueba o depuración adicionales encima de la imagen de producción.

<img src="/img Marco/dockerProd.png" alt="gitclone command" width="800"/>

   <br>


5. **Etiquetas** 

> Al crear imágenes, las debemos etiquetar siempre con etiquetas útiles que codifiquen la información de la versión, el destino previsto ( **prod** o **test**), la estabilidad u otra información que sea útil al implementar la aplicación en diferentes entornos. No confíe en la etiqueta **latest** creada automáticamente.

<img src="/img Marco/tag.png" alt="gitclone command" width="800"/>

## Donde y cómo conservar los datos de la aplicacion.

- Evite almacenar datos de aplicaciones en la capa de escritura de su contenedor utilizando controladores de almacenamiento . Esto aumenta el tamaño de su contenedor y es menos eficiente desde una perspectiva de E/S que usar volúmenes o montajes vinculados. :chart_with_upwards_trend:
  <br>
- almacene datos usando volúmenes.Un caso en el que es apropiado utilizar montajes enlazados es durante el desarrollo, cuando es posible que desee montar su directorio fuente o un binario que acaba de crear en su contenedor. Para producción, utilice un volumen y móntelo en la misma ubicación en la que montó un soporte de enlace durante el desarrollo. :floppy_disk:
  <br>
- Para producción, utilice secretos para almacenar datos confidenciales de aplicaciones utilizados por los servicios y utilice configuraciones para datos no confidenciales, como archivos de configuración. Si actualmente utiliza contenedores independientes, considere migrar para utilizar servicios de réplica única, de modo que pueda aprovechar estas características de solo servicio.:lock: 

## Utilizar CI/CD para pruebas e implementacion

- Cuando se registra un cambio en el codigo fuente o se crea una solicitud de extracción, use Docker Hub u otra canalización de CI/CD para crear y etiquetar automáticamente una imagen de Docker y probarla.

<img src="/img Marco/gitdock.png" alt="gitclone command" width="800"/>


<br>

- Lleve esto aún más lejos al solicitar a sus equipos de desarrollo, pruebas y seguridad que firmen las imágenes antes de implementarlas en producción. De esta manera, antes de implementar una imagen en producción, ha sido probada y aprobada, por ejemplo, por los equipos de desarrollo, calidad y seguridad.

<img src="/img Marco/equipo.png" alt="gitclone command" width="800"/>

## Mejores practicas para escribir Dockerfiles 

Una imagen de Docker consta de capas de solo lectura, cada una de las cuales representa una instrucción Dockerfile. Las capas están apiladas y cada una es un delta de los cambios de la capa anterior.

```
# syntax=docker/dockerfile:1

FROM ubuntu:22.04
COPY . /app
RUN make /app
CMD python /app/app.py
```
En el ejemplo anterior cada instruccion crea una capa:

- **FROM** crea una capa a partir de la ubuntu:22.04imagen de Docker.
- **COPY** agrega archivos del directorio actual de su cliente Docker.
- **RUN** construye su aplicación con make.
- **CMD** especifica qué comando ejecutar dentro del contenedor.

### recomendaciones generales:

- **crear contenedores efimeros**

La imagen definida por nuestro Dockerfile debe generar contenedores que sean lo más efímeros posible. Efímero significa que el contenedor se puede detener y destruir, luego reconstruir y reemplazar con una configuración mínima absoluta.

- **canalizar un Dockerfile a través de stdin**

Docker tiene la capacidad de crear imágenes canalizando un Dockerfile a través de stdin con un contexto de compilación local o remoto. Canalizar un Dockerfile a través de stdin puede ser útil para realizar compilaciones únicas sin escribir un Dockerfile en el disco, o en situaciones en las que el Dockerfile se genera y no debe persistir después.

- **Excluir archivos con .dockerignore**

Para excluir archivos que no sean relevantes para la compilación, sin reestructurar su repositorio de origen, utilice un archivo **.dockerignore**. Este archivo admite patrones de exclusión similares a **.gitignore** archivos.

- **Utilizar compilaciones de varias etapas**

Las compilaciones de varias etapas le permiten reducir drásticamente el tamaño de su imagen final, sin tener que luchar para reducir la cantidad de capas y archivos intermedios.

> **Por ejemplo:** si su compilación contiene varias capas y desea asegurarse de que la caché de compilación sea reutilizable, puede ordenarlas desde las que se modifican con menos frecuencia hasta las que se modifican con más frecuencia.

```
# syntax=docker/dockerfile:1
FROM golang:1.20-alpine AS build

# Install tools required for project
# Run `docker build --no-cache .` to update dependencies
RUN apk add --no-cache git

# List project dependencies with go.mod and go.sum
# These layers are only re-built when Gopkg files are updated
WORKDIR /go/src/project/
COPY go.mod go.sum /go/src/project/
# Install library dependencies
RUN go mod download

# Copy the entire project and build it
# This layer is rebuilt when a file changes in the project directory
COPY . /go/src/project/
RUN go build -o /bin/project

# This results in a single layer image
FROM scratch
COPY --from=build /bin/project /bin/project
ENTRYPOINT ["/bin/project"]
CMD ["--help"]
```

- **No instalar paquetes innecesarios**
- **minimizar el numero de capas**
- **ordenar argumentos de varias lineas**

Siempre que sea posible, ordene los argumentos de varias líneas alfanuméricamente para facilitar el mantenimiento.

```
RUN apt-get update && apt-get install -y \
  bzr \
  cvs \
  git \
  mercurial \
  subversion \
  && rm -rf /var/lib/apt/lists/*
```

- **Aprovechar la cache de compilacion**

Al crear una imagen, Docker sigue las instrucciones de su Dockerfile y ejecuta cada una en el orden especificado. A medida que se examina cada instrucción, Docker busca una imagen existente en su caché, en lugar de crear una imagen nueva duplicada.

## Mejores practicas de seguridad

Para mejorar la seguridad durante nuestro procesos de desarrollo y despliegue podemos seguir una serie de recomendaciones generales

1. **Elegir la imagen base correcta:**

Debemos asegurarnos de elegir una imagen que este creada apartir de una fuente confiable.

> Docker Hub tiene más de 8,3 millones de repositorios. Algunas de estas imágenes son **imágenes oficiales**, docker también ofrece imágenes publicadas por **editores verificados** . Estas imágenes de alta calidad son publicadas y mantenidas por las organizaciones asociadas con Docker, y Docker verifica la autenticidad del contenido en sus repositorios.

<br>

<img src="/img Marco/dockerOFI2.png" alt="gitclone command" width="800"/>
<br>

2. **Utilizar compilaciones de varias etapas:**

Puede usar varias declaraciones FROM en su Dockerfile y puede usar una imagen base diferente para cada una. También puedes copiar selectivamente artefactos de una etapa a otra, dejando atrás cosas que no necesitas en la imagen final. Esto puede dar como resultado una imagen final concisa. 

> Este método de crear una imagen pequeña no sólo reduce significativamente la complejidad, sino que también reduce la posibilidad de implementar artefactos vulnerables en su imagen.

3. **Reconstruir imagenes**

Se recomienda  reconstruir una imagen de Docker con regularidad para evitar vulnerabilidades conocidas que se hayan solucionado. Al reconstruir, se puede utilizar la opción **--no-cache** para evitar accesos a la caché y garantizar una descarga nueva.

```
docker build --no-cache -t myImage:myTag myPath/
```

4. **Verifique su imagen en busca de vulnerabilidades:**

Además de seguir las mejores prácticas descritas en esta página al desarrollar imágenes, también es importante analizar y evaluar continuamente la postura de seguridad de sus imágenes utilizando herramientas de detección de vulnerabilidades.

> - Docker Hub admite una función de escaneo automático de vulnerabilidades que, cuando está habilitada, escanea automáticamente las imágenes cuando las envía a un repositorio de Docker Hub. Requiere una suscripción a Docker.

> - Docker Hub también admite una función de análisis de imágenes avanzado de acceso temprano , que amplía la solución de escaneo de vulnerabilidades "básica" con capacidades mejoradas e información más detallada y procesable.

> - Para la CLI, existe el docker scoutcomplemento CLI que le permite explorar vulnerabilidades de imágenes usando la terminal.

> - Docker Desktop tiene una vista detallada de las imágenes de su almacén de imágenes local, que visualiza todas las vulnerabilidades conocidas que afectan a una imagen.


## construir con Docker

El proyecto de ejemplo es una aplicación cliente-servidor para traducir mensajes a un idioma ficticio.
La estructura del proyecto es la siguiente.
<img src="/img Marco/Nuevaapp.png" alt="gitclone command" width="200"/>

### Capas

El orden de las instrucciones de Dockerfile es importante. Una compilación de Docker consta de una serie de instrucciones de compilación ordenadas. Cada instrucción en un Dockerfile se traduce aproximadamente en una capa de imagen. 

<img src="/img Marco/layers.png" alt="gitclone command" width="1000"/>

### Capas almacenadas en caché

Cuando se ejecuta una compilación, el constructor intenta reutilizar capas de compilaciones anteriores. Si una capa de una imagen no se modifica, el constructor la recoge del caché de compilación. Si una capa ha cambiado desde la última generación, esa capa y todas las capas siguientes deben reconstruirse.

<img src="/img Marco/cache-bust.png" alt="gitclone command" width="1000"/>

### como actualizar el orden de las instrucciones

Puede evitar esta redundancia reordenando las instrucciones en el Dockerfile. Cambie el orden de las instrucciones para que la descarga e instalación de las dependencias se produzcan antes de que el código fuente se copie en el contenedor. De esa manera, el constructor puede reutilizar la capa de "dependencias" del caché, incluso cuando realiza cambios en su código fuente.

## multi etapas

- Le permiten ejecutar pasos de compilación en paralelo, lo que hace que su proceso de compilación sea más rápido y eficiente.
- Le permiten crear una imagen final con un tamaño más pequeño y que contiene solo lo necesario para ejecutar su programa.

Luego de ejecutar docker run con el DockerFile que tenemos actualmente nos crea el siguiente container.
  
```
docker build --tag=buildme .
docker images buildme
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
buildme      latest    c021c8a7051f   5 seconds ago   150MB
```

### Agregar etapas

Al utilizar compilaciones de varias etapas, se puede optar por utilizar diferentes imágenes base para sus entornos de compilación y tiempo de ejecución. Puede copiar artefactos de compilación desde la etapa de compilación a la etapa de ejecución.

Vamos a cambiar el DockerFile
```
  # syntax=docker/dockerfile:1
  FROM golang:1.20-alpine
  WORKDIR /src
  COPY go.mod go.sum .
  RUN go mod download
  COPY . .
  RUN go build -o /bin/client ./cmd/client
  RUN go build -o /bin/server ./cmd/server
+
+ FROM scratch
+ COPY --from=0 /bin/client /bin/server /bin/
  ENTRYPOINT [ "/bin/server" ]
```

Ahora, si crea la imagen y la inspecciona, debería ver un número significativamente menor:

```
docker build --tag=buildme .
docker images buildme
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
buildme      latest    436032454dd8   7 seconds ago   8.45MB
```

## Implementacion y orquestacion

### Implementar en Kubernetes

el concepto de contenedores permite permite mover y escalar aplicaciones a nubes y centros de datos.

>Las herramientas para gestionar, escalar y mantener aplicaciones en contenedores se denominan orquestadores. Dos de las herramientas de orquestación más populares son Kubernetes y Docker Swarm. Docker Desktop proporciona entornos de desarrollo para ambos orquestadores.

oe piroba
