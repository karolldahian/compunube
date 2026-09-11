# Práctica Contenedores Docker[cite: 1]
**Por:** Prof. Oscar H. Mondragón[cite: 1]

---

## 1. Objetivo
Comprender la instalación y el uso de la plataforma de contenedores Docker[cite: 1].

---

## 2. Herramientas a Utilizar
* Vagrant[cite: 1]
* VirtualBox[cite: 1]
* Docker Community Edition[cite: 1]
* Vagrant Box Ubuntu (`bento/ubuntu-22.04`)[cite: 1]

---

## 3. Desarrollo de la Práctica[cite: 1]

### PARTE 1. Configuración de Vagrant[cite: 1]
Esta práctica se desarrollará usando un Box de Ubuntu 22.04 en Vagrant[cite: 1]. El `Vagrantfile` a utilizar es el siguiente[cite: 1]:

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define :clienteUbuntu do |clienteUbuntu|
    clienteUbuntu.vm.box = "bento/ubuntu-22.04"
    clienteUbuntu.vm.network :private_network, ip: "192.168.100.2"
    clienteUbuntu.vm.hostname = "clienteUbuntu"
  end

  config.vm.define :servidorUbuntu do |servidorUbuntu|
    servidorUbuntu.vm.box = "bento/ubuntu-22.04"
    servidorUbuntu.vm.network :private_network, ip: "192.168.100.3"
    servidorUbuntu.vm.hostname = "servidorUbuntu"
  end

end
```

---

### PARTE 2. Instalación de Docker en Ubuntu 22.04[cite: 1]

#### 1. Desinstalar versiones anteriores de Docker[cite: 1]
```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

#### 2. Configurar el repositorio[cite: 1]
Agregue la clave GPG oficial de Docker[cite: 1]:
```bash
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL [https://download.docker.com/linux/ubuntu/gpg](https://download.docker.com/linux/ubuntu/gpg) -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Agregue el repositorio a Apt sources[cite: 1]:
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] [https://download.docker.com/linux/ubuntu](https://download.docker.com/linux/ubuntu) \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```

#### 3. Instalar la última versión de Docker[cite: 1]
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

> **Nota:** Puede agregar su usuario al grupo Docker; de esta manera no necesita anteponer `sudo` cuando ejecute Docker[cite: 1]. Para esto, ejecute[cite: 1]:
> ```bash
> sudo usermod -aG docker $USER
> ```
> Para que tome efecto debe salir de la máquina (`exit`) e ingresar de nuevo con `vagrant ssh`[cite: 1].

Verifique que Docker Engine quedó instalado correctamente corriendo la imagen de `hello-world`[cite: 1]:
```bash
sudo docker run hello-world
```
Este comando descarga una imagen de prueba y la ejecuta en un contenedor[cite: 1]. Cuando se ejecuta el contenedor, imprime un mensaje informativo y sale[cite: 1].

#### 4. Verificar que Docker esté corriendo[cite: 1]
```bash
$ sudo systemctl status docker
```
Salida de ejemplo[cite: 1]:
```text
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2020-04-15 16:13:34 UTC; 14min ago
```

#### 5. Ver información de Docker[cite: 1]
Todos los comandos de docker inician con la palabra `docker`[cite: 1]. Para ver la información del servicio[cite: 1]:
```bash
$ sudo docker info | more
```
Salida de ejemplo[cite: 1]:
```text
Server:
 Containers: 3
  Running: 0
  Paused: 0
  Stopped: 3
 Images: 2
 Server Version: 19.03.8
```
Aquí se puede ver información relacionada con las imágenes descargadas y los contenedores creados[cite: 1].

---

### PARTE 3. Descargar una imagen Docker existente y correr sus servicios[cite: 1]

#### 6. Verificar qué imágenes existen en los repositorios de Docker[cite: 1]
Por ejemplo, si quisiéramos saber qué imágenes de contenedores con Apache existen[cite: 1]:
```bash
$ sudo docker search apache
```
Salida[cite: 1]:
```text
NAME                     DESCRIPTION                                     STARS     OFFICIAL
tomcat                   Apache Tomcat is an open source implementati... 2643      [OK]
maven                    Apache Maven is a software project management... 1552     [OK]
httpd                    The Apache HTTP Server Project                  4867      [OK]
zookeeper                Apache ZooKeeper is an open-source server...     1413      [OK]
solr                     Apache Solr is the popular, blazing-fast, op...  982       [OK]
flink                    Apache Flink is a powerful open-source dist...   412       [OK]
cassandra                Apache Cassandra is an open-source distributed... 1523    [OK]
groovy                   Apache Groovy is a multi-faceted language fo...  140       [OK]
tomee                    Apache TomEE is an all-Apache Java EE certif... 114       [OK]
storm                    Apache Storm is a free and open source distri... 195       [OK]
spark                    Apache Spark - A unified analytics engine fo...  37        [OK]
bitnami/apache           Bitnami Apache Docker Image                     61
apache/airflow           Apache Airflow                                  503
```

#### 7. Descargar imágenes[cite: 1]
```bash
$ docker pull httpd
```
Salida[cite: 1]:
```text
Using default tag: latest
latest: Pulling from library/httpd
1546e941f15b: Pull complete
657739c97e0b: Pull complete
af4fb700ef54: Pull complete
598e131c66a1: Pull complete
4788cb06a95a: Pull complete
48aa2460acc6: Pull complete
Digest: sha256:104f07de17ee186c8f37b9f561e04fbfe4cf080d78c5e5f3802fd08fd118c3da
Status: Downloaded newer image for httpd:latest
docker.io/library/httpd:latest
```

#### 8. Ver imágenes descargadas[cite: 1]
```bash
$ sudo docker images
```
Salida[cite: 1]:
```text
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
httpd         latest    50a1bd9b2971   6 weeks ago     194MB
hello-world   latest    ee301c921b8a   10 months ago   9.14kB
```

#### 9. Ejecutar un contenedor basado en una de las imágenes descargadas[cite: 1]
```bash
$ sudo docker run -d --name web1 -p 8800:80 httpd
```
Salida[cite: 1]:
```text
71b95a509905ae37646359b28bd520093a6d4e35076cbf517a9fbd092de92eb6
```
* La opción `-d` permite correr el contenedor en background (segundo plano)[cite: 1].
* La opción `-p` permite hacer un reenvío desde el puerto 80 del contenedor al puerto 8800 del host[cite: 1].

#### 10. Verificar qué contenedores están corriendo actualmente[cite: 1]
```bash
$ sudo docker ps
```
Salida[cite: 1]:
```text
CONTAINER ID   IMAGE   COMMAND              CREATED         STATUS         PORTS                  NAMES
71b95a509905   httpd   "httpd-foreground"   6 seconds ago   Up 5 seconds   0.0.0.0:8800->80/tcp   web1
```

#### 11. Acceder a los servicios del contenedor[cite: 1]
La imagen descargada tiene activado el servicio web[cite: 1]. Desde el navegador se accede a la dirección IP de la máquina virtual de Ubuntu (`192.168.100.3`) por el puerto `8800`[cite: 1]:
```text
[http://192.168.100.3:8800](http://192.168.100.3:8800)
```
Respuesta esperada en pantalla: `It works!`[cite: 1]

#### 12. Remover un contenedor[cite: 1]
Para eliminar una o más imágenes/contenedores de Docker, use el comando `docker container rm` seguido de la ID de los contenedores que desea eliminar[cite: 1].

Listar todos los contenedores existentes[cite: 1]:
```bash
$ sudo docker container ls -a
```
Salida[cite: 1]:
```text
CONTAINER ID   IMAGE         COMMAND              CREATED          STATUS                      PORTS                  NAMES
71b95a509905   httpd         "httpd-foreground"   12 minutes ago   Up 12 minutes               0.0.0.0:8800->80/tcp   web1
a5f5098b957e   hello-world   "/hello"             28 minutes ago   Exited (0) 28 minutes ago                          competent_borg
```

Si el contenedor está corriendo, se debe detener primero y luego eliminar[cite: 1]:
```bash
$ sudo docker container stop 71b95a509905
71b95a509905

$ sudo docker container rm 71b95a509905
71b95a509905
```

Verificar que ya no existe[cite: 1]:
```bash
$ sudo docker container ls -a
```

---

### PARTE 4. Imagen Docker Propia[cite: 1]
Creación de una imagen propia con servicios personalizados a partir de la imagen oficial de Ubuntu[cite: 1].

```bash
$ sudo docker search ubuntu
```
Salida[cite: 1]:
```text
NAME             DESCRIPTION                                     STARS     OFFICIAL
ubuntu           Ubuntu is a Debian-based Linux operating sys... 17516     [OK]
ubuntu/cortex    Cortex provides storage for Prometheus, Long... 4
ubuntu/squid     Squid is a caching proxy for the Web...         108
ubuntu/nginx     Nginx, a high-performance reverse proxy & we... 128
```

#### 13. Descargar la imagen base[cite: 1]
```bash
$ sudo docker pull ubuntu
Using default tag: latest
```

#### 14. Crear un archivo Dockerfile dentro de un directorio test_docker[cite: 1]
```bash
$ mkdir test_docker
$ cd test_docker
$ mkdir html1
$ cd html1
```

Editar una página `index.html` en el directorio `html1`[cite: 1]:
```bash
vagrant@servidorUbuntu:~/test_docker/html1$ vim index.html
```
Agregar el siguiente contenido[cite: 1]:
```html
<h1> Bienvenidos al servidor de prueba </h1>
```

Volver al directorio raíz del proyecto y editar `Dockerfile`[cite: 1]:
```bash
vagrant@servidorUbuntu:~/test_docker/html1$ cd ..
vagrant@servidorUbuntu:~/test_docker$ vim Dockerfile
```

Contenido del archivo **`Dockerfile`**[cite: 1]:
```dockerfile
FROM ubuntu
RUN apt update
RUN apt install -y apache2
RUN apt install -y apache2-utils
RUN apt clean
COPY html1/ /var/www/html/
EXPOSE 80
CMD ["apache2ctl", "-D", "FOREGROUND"]
```

#### 15. Construir la imagen a partir del Dockerfile[cite: 1]
```bash
vagrant@servidorUbuntu:~/test_docker$ sudo docker build -t omondragon/ubuntuweb .
```
Salida resumida[cite: 1]:
```text
Building 1.1s (11/11) FINISHED
 => [internal] load build definition from Dockerfile
 => => transferring dockerfile: 216B
 => [internal] load metadata for docker.io/library/ubuntu:latest
 => [internal] load .dockerignore
 => [1/6] FROM docker.io/library/ubuntu:latest
 => [internal] load build context
 => CACHED [2/6] RUN apt update
 => CACHED [3/6] RUN apt install -y apache2
 => CACHED [4/6] RUN apt install -y apache2-utils
 => CACHED [5/6] RUN apt clean
 => CACHED [6/6] COPY html1/ /var/www/html/
 => exporting to image
 => => exporting layers
 => => writing image sha256:9573a7bfe89656eaee0b552ca34664779b015f8072576a5b9af30b062abd3c7e
 => => naming to docker.io/omondragon/ubuntuweb
```

#### 16. Crear el contenedor[cite: 1]
```bash
$ sudo docker run --name webprueba -d -p 9000:80 omondragon/ubuntuweb
ebb43775ccb53452db74bb9128346feb7634f758bdd56427966944c4b84acd3
```

#### 17. Verificar los contenedores en ejecución[cite: 1]
```bash
$ docker ps
```
Salida[cite: 1]:
```text
CONTAINER ID   IMAGE                   COMMAND                  CREATED          STATUS          PORTS                                              NAMES
f94e2f336ce2   omondragon/ubuntuweb    "apache2ctl -D FOREG..." 12 minutes ago   Up 12 minutes   0.0.0.0:9000->80/tcp, [::]:9000->80/tcp            webprueba
```

#### 18. Verificar el servicio funcionando[cite: 1]
Abrir en el navegador[cite: 1]:
```text
[http://192.168.100.3:9000](http://192.168.100.3:9000)
```
Muestra en pantalla el mensaje configurado en el archivo HTML[cite: 1].

---

### PARTE 5. Subir Contenedor al Registry (Docker Hub)[cite: 1]
En esta parte subiremos nuestra imagen a un repositorio de Docker público en Docker Hub[cite: 1].

1. Crear cuenta en [https://hub.docker.com](https://hub.docker.com)[cite: 1].
2. Iniciar sesión a través de la terminal[cite: 1]:
```bash
$ sudo docker login
```
Salida[cite: 1]:
```text
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to [https://hub.docker.com](https://hub.docker.com) to create one.
Username: youruser
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
[https://docs.docker.com/engine/reference/commandline/login/#credentials-store](https://docs.docker.com/engine/reference/commandline/login/#credentials-store)

Login Succeeded
```

3. Crear un tag para generar una nueva versión de la imagen con el nombre del repositorio de Docker Hub[cite: 1]:
```bash
$ sudo docker tag omondragon/ubuntuweb omondragon/ubuntuweb:v1
```
Verificar la imagen etiquetada[cite: 1]:
```bash
vagrant@servidorUbuntu:~/test_docker$ sudo docker images
```
Salida[cite: 1]:
```text
REPOSITORY              TAG       IMAGE ID       CREATED          SIZE
omondragon/ubuntuweb    latest    7e37e79f5672   13 minutes ago   583MB
omondragon/ubuntuweb    v1        7e37e79f5672   13 minutes ago   583MB
omondragon/haproxy      latest    440f9872cbfc   4 weeks ago      99.4MB
omondragon/web          latest    058fdec1d5c0   4 weeks ago      227MB
hello-world             latest    feb5d9fea6a5   17 months ago    13.3kB
```
> **Nota:** En su caso debe crear una imagen nueva con su usuario de Docker Hub[cite: 1]:
> ```bash
> $ sudo docker build -t youruser/ubuntuweb .
> $ sudo docker tag youruser/ubuntuweb youruser/ubuntuweb:v1
> ```

4. Subir la imagen al repositorio con `docker push`[cite: 1]:
```bash
$ sudo docker push omondragon/ubuntuweb:v1
```
Salida[cite: 1]:
```text
The push refers to repository [docker.io/omondragon/ubuntuweb]
5683a22e1dfe: Pushed
84d435426403: Pushed
9b4d0428265a: Pushed
5652288c41fe: Pushed
```

5. Probar la imagen desde cualquier otro equipo (por ejemplo, desde `clienteUbuntu`)[cite: 1]:
```bash
vagrant@clienteUbuntu:~$ sudo docker run --name webcliente -d -p 9900:80 omondragon/ubuntuweb:v1
```
Salida[cite: 1]:
```text
Unable to find image 'omondragon/ubuntuweb:v1' locally
v1: Pulling from omondragon/ubuntuweb
a1d0c7532777: Pull complete
a4e38749de96: Pull complete
b2f153aa63f3: Pull complete
2e051a87d5fc: Pull complete
3a044004153d: Pull complete
Digest: sha256:1cecacafeb01ee5902833866dfbded06672534679cbbdfe9bacbff8d6aa93859
Status: Downloaded newer image for omondragon/ubuntuweb:v1
ab08658c08f67d79498b58bea3717e1b8c64433a83fad60c04c596a5904c9a02
```

6. Acceder desde el navegador para verificar[cite: 1]:
```text
[http://192.168.100.2:9900](http://192.168.100.2:9900)
```

---

### PARTE 6. Copiar Archivos desde Directorio del Host[cite: 1]
Crear una imagen propia copiando archivos de una carpeta del host local a una carpeta del contenedor[cite: 1].

#### 19. Crear directorio en el host[cite: 1]
```bash
vagrant@machine1:~$ mkdir test_docker2
vagrant@machine1:~$ cd test_docker2
vagrant@machine1:~/test_docker2$ mkdir voldocker
vagrant@machine1:~/test_docker2$ cd voldocker
```

Crear archivos dentro de `voldocker`[cite: 1]:
```html
<!-- index.html -->
<h1>Prueba directorios</h1>
<a href="pagina1.html">Ir a pagina1</a>
```

#### 20. Crear archivo Dockerfile en `test_docker2`[cite: 1]
```bash
vagrant@machine1:~/test_docker2$ cd ..
vagrant@machine1:~/test_docker2$ vim Dockerfile
```

Contenido de **`Dockerfile`**[cite: 1]:
```dockerfile
FROM ubuntu
RUN apt update
RUN apt install -y apache2
RUN apt install -y apache2-utils
RUN apt clean
COPY voldocker/ /var/www/html/
EXPOSE 80
CMD ["apache2ctl", "-D", "FOREGROUND"]
```

#### 21. Construir la imagen[cite: 1]
```bash
vagrant@machine1:~/test_docker2$ sudo docker build -t omondragon/testdir .
```
Salida[cite: 1]:
```text
Sending build context to Docker daemon 4.608kB
```

#### 22. Probar el nuevo contenedor[cite: 1]
```bash
vagrant@machine1:~/test_docker2$ sudo docker run -d --name webcontainer -p 9910:80 omondragon/testdir
b05c9ac9adc2d70b320634096199080ac28d99b852a8b546d3b3ed675a1e7dc8
```
Verificar en el navegador[cite: 1]:
```text
[http://192.168.100.3:9910](http://192.168.100.3:9910)
```

#### 23. Ver logs de un contenedor[cite: 1]
```bash
docker logs <ContainerName>
# o
docker logs <ContainerID>
```
Ejemplo[cite: 1]:
```bash
$ sudo docker container ls -a
```
Salida[cite: 1]:
```text
CONTAINER ID   IMAGE                COMMAND                 CREATED         STATUS         PORTS                                      NAMES
ba59538b9f7b   omondragon/testdir   "/usr/sbin/httpd -D..." 2 minutes ago   Up 2 minutes   0.0.0.0:9910->80/tcp, :::9910->80/tcp      webcontainer
```
Consultar registros[cite: 1]:
```bash
$ sudo docker logs ba59538b9f7b
$ sudo docker logs webcontainer
```

#### 24. Entrar al terminal interactivo del contenedor[cite: 1]
```bash
sudo docker exec -it webcontainer /bin/bash
```

---

## 4. Ejercicios[cite: 1]

1. **Imagen propia + DockerHub:** Construya una imagen propia de Docker en la cual despliegue un sitio web personalizado y súbalo a su repositorio de Docker Hub[cite: 1].
2. **Contenedor para Data Science e IA:** Genere y pruebe un contenedor Docker con Jupyter notebooks y librerías que usualmente se usan en Data Science e IA (`tensorflow`, `preprocess`)[cite: 1].  
   *Ver:* https://towardsdatascience.com/make-your-data-science-life-easy-with-docker-c3e1fc0dee59[cite: 1]
3. **Volúmenes Docker:** Investigue cómo funcionan los volúmenes en Docker para compartir directorios entre el anfitrión y un contenedor[cite: 1]. Implemente un ejemplo usando volúmenes[cite: 1].  
   *Consultar:* https://ricardogeek.com/usando-volumenes-en-docker/[cite: 1]
4. **Contenedor para IA (TensorFlow + Scikit-learn):**  
   Clone el siguiente repositorio y experimente con el contenedor Docker que contiene TensorFlow y scikit-learn con Python 3.7: https://github.com/asashiho/ml-jupyter-python3[cite: 1]  
   * Si aparece el error `Package 'libav-tools' has no installation candidate`, comente la línea `libav-tools` en el Dockerfile (~línea 16)[cite: 1].  
   * En el Dockerfile modifique la línea 31: cambiar `Sklearn` por `scikit-learn`[cite: 1].

---

## 4. Desafíos [Hasta 0.5 Puntos en una nota de prácticas][cite: 1]
*Desarrolle uno de los siguientes puntos:*[cite: 1]

5. **(Vale por 1.0 puntos) CUDA + Python + Docker:** Demuestre el funcionamiento de un contenedor Docker con aceleración por GPUs (requiere máquina con GPU disponible)[cite: 1].  
   *Sugerencia:* https://developer.nvidia.com/how-to-cuda-python[cite: 1]
6. **Docker en LXD:** LXD se enfoca en contenedores de sistema[cite: 1]. Investigue cómo configurar un contenedor Docker dentro de LXD que corra una aplicación básica (ej. un servidor web)[cite: 1].  
   *Instrucciones:* https://stgraber.org/2016/04/13/lxd-2-0-docker-in-lxd-712/[cite: 1]
7. **Docker + Flask:** Clone el repositorio y pruebe la creación y ejecución de un contenedor con Flask en modo de prueba exponiendo el puerto 5000: https://github.com/omondragon/docker-flask-example[cite: 1]  
   *Documentación de Flask:* https://flask.palletsprojects.com/en/1.1.x/[cite: 1]

---

## 8. Bibliografía[cite: 1]
* Sitio oficial Docker: https://www.docker.com/[cite: 1]
* Instalar Docker en Ubuntu (Inglés): https://docs.docker.com/engine/install/ubuntu/[cite: 1]
* Instalar Docker en Ubuntu (Español): https://www.digitalocean.com/community/tutorials/como-instalar-y-usar-docker-en-ubuntu-18-04-1-es[cite: 1]
* Core OS rkt containers: https://coreos.com/rkt/[cite: 1]
* Docker en LXD: https://stgraber.org/2016/04/13/lxd-2-0-docker-in-lxd-712/[cite: 1]
* Publicar tu imagen en Docker Hub: https://www.returngis.net/2019/02/publicar-tu-imagen-en-docker-hub/[cite: 1]