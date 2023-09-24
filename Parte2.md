#Segunda parte PracDocker


## Desarrollando con docker

Docker es un forma eficiente de manejar apliaciones (alojarlas y desplegarlas) con contenedores podemos organizar nuestro codigo tanto de pruebas como de desarrollo, a continuacion vamos a listar buenas practicas de desarrollo con docker.

<img src="/img Marco/docker.webp" alt="gitclone command" width="800"/>

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

- **Evite almacenar datos de aplicaciones en la capa de escritura de su contenedor utilizando controladores de almacenamiento . Esto aumenta el tamaño de su contenedor y es menos eficiente desde una perspectiva de E/S que usar volúmenes o montajes vinculados.** :chart_with_upwards_trend:
  <br>
- **almacene datos usando volúmenes.Un caso en el que es apropiado utilizar montajes enlazados es durante el desarrollo, cuando es posible que desee montar su directorio fuente o un binario que acaba de crear en su contenedor. Para producción, utilice un volumen y móntelo en la misma ubicación en la que montó un soporte de enlace durante el desarrollo.** :floppy_disk:
  <br>
- **Para producción, utilice secretos para almacenar datos confidenciales de aplicaciones utilizados por los servicios y utilice configuraciones para datos no confidenciales, como archivos de configuración. Si actualmente utiliza contenedores independientes, considere migrar para utilizar servicios de réplica única, de modo que pueda aprovechar estas características de solo servicio.** :lock: 

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

