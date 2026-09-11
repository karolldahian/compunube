# Práctica: Contenedores Docker

Guía de implementación, despliegue y sustentación para la práctica de contenedores con Docker, Vagrant, VirtualBox y Ubuntu 22.04.

---

## 🗺️ Mapa de Infraestructura y Puertos

| Servicio / Contenedor | Máquina | IP Privada | Puerto Host | Descripción |
|---|---|---|---|---|
| **Docker Engine Base** | Ambas | `.2` y `.3` | N/A | Docker CE + daemon + `hello-world` |
| **Apache Oficial (`httpd`)** | `servidorUbuntu` | `192.168.100.3` | **`8800`** | Imagen descargada de Docker Hub (`Parte 3`) |
| **Imagen Propia (`ubuntuweb`)** | `servidorUbuntu` | `192.168.100.3` | **`9000`** | Creada desde `Dockerfile` en `test_docker/` (`Parte 4`) |
| **Descarga desde Docker Hub** | `clienteUbuntu` | `192.168.100.2` | **`9900`** | Imagen descargada de Docker Hub a cliente (`Parte 5`) |
| **Volumen Compartido (`testdir`)** | `servidorUbuntu` | `192.168.100.3` | **`9910`** | Bind mount de `/vagrant/test_docker2/voldocker` (`Parte 6`) |
| **Jupyter Data Science / IA** | `servidorUbuntu` | `192.168.100.3` | **`8888`** | Notebook con Pandas, NumPy, Scikit-learn (`Ejercicio 2/4`) |
| **Desafío Flask** | `servidorUbuntu` | `192.168.100.3` | **`5000`** | API REST en Python Flask (`Desafío 7`) |

---

## 🚀 1. Puesta en Marcha Inicial

Desde tu terminal de Windows (PowerShell) en `C:\Users\josel\ComputacionEnLaNube\PracticaDocker`:

```powershell
vagrant up
```
*El aprovisionador ejecuta automáticamente `scripts/install-docker.sh` en ambas máquinas virtuales, instalando Docker CE, Buildx, Compose y configurando el grupo `docker` para el usuario `vagrant`.*

---

## 🛠️ 2. Guía Paso a Paso para la Sustentación

### PARTE 2: Comprobación de Docker y Estado
Entra a `servidorUbuntu`:
```bash
vagrant ssh servidorUbuntu
```
Comandos a mostrar al docente:
```bash
docker --version
sudo systemctl status docker
docker info
docker run --rm hello-world
```

---

### PARTE 3: Descargar y Correr Apache Oficial (Puerto 8800)
Dentro de `servidorUbuntu`:
```bash
# 1. Descargar imagen
docker pull httpd

# 2. Correr contenedor en segundo plano (-d) con mapeo de puertos (-p 8800:80)
docker run -d --name web1 -p 8800:80 httpd

# 3. Verificar estado
docker ps
```
* **Prueba en navegador:** Entra a `http://192.168.100.3:8800/` (Debe salir: `It works!`).

---

### PARTE 4: Imagen Docker Propia con Dockerfile (Puerto 9000)
Dentro de `servidorUbuntu`:
```bash
cd /vagrant/test_docker

# 1. Construir la imagen desde el Dockerfile
docker build -t miusuario/ubuntuweb:v1 .

# 2. Lanzar el contenedor
docker run -d --name webprueba -p 9000:80 miusuario/ubuntuweb:v1

# 3. Validar contenedor activo
docker ps
```
* **Prueba en navegador:** Entra a `http://192.168.100.3:9000/` (Debe salir: `Bienvenidos al servidor de prueba`).

---

### PARTE 5: Subir a Docker Hub y Desplegar en Cliente (Puerto 9900)
1. En `servidorUbuntu`, inicia sesión en tu cuenta de Docker Hub:
   ```bash
   docker login
   # Ingresa tu Username y Password / Token de Docker Hub
   docker push TU_USUARIO_DOCKERHUB/ubuntuweb:v1
   ```
2. Sal de `servidorUbuntu` y entra a `clienteUbuntu`:
   ```bash
   exit
   vagrant ssh clienteUbuntu
   ```
3. Descarga y ejecuta la imagen pública desde Docker Hub:
   ```bash
   docker run -d --name webcliente -p 9900:80 TU_USUARIO_DOCKERHUB/ubuntuweb:v1
   ```
* **Prueba en navegador:** Entra a `http://192.168.100.2:9900/` (Muestra la misma web desde la IP de `clienteUbuntu`).

---

### PARTE 6: Volúmenes y Edición en Caliente (Puerto 9910)
En `servidorUbuntu`:
```bash
cd /vagrant/test_docker2

# 1. Construir imagen base
docker build -t miusuario/testdir:v1 .

# 2. Correr montando la carpeta del host (-v)
docker run -d --name webcontainer -p 9910:80 -v /vagrant/test_docker2/voldocker:/var/www/html miusuario/testdir:v1
```
* **Prueba en navegador:** `http://192.168.100.3:9910/`
* **Demostración de Volumen en Vivo frente al profesor:**
  1. Edita el archivo `test_docker2/voldocker/index.html` en tu Windows o con `vim` en la VM agregando cualquier texto (ej. `<h2>Modificado en vivo</h2>`).
  2. Recarga el navegador: **¡El cambio aparece inmediatamente sin necesidad de reconstruir la imagen ni reiniciar el contenedor!**
* **Comandos de inspección:**
  ```bash
  docker logs webcontainer
  docker exec -it webcontainer /bin/bash
  ```

---

### EJERCICIOS 2 y 4: Contenedor para Data Science / IA (Puerto 8888)
En `servidorUbuntu`:
```bash
cd /vagrant/jupyter_datascience

# 1. Construir la imagen
docker build -t datascience-ia:v1 .

# 2. Correr el contenedor de Jupyter
docker run -d --name jupyter-ia -p 8888:8888 -v /vagrant/jupyter_datascience/notebooks:/notebooks datascience-ia:v1
```
* **Prueba en navegador:** Abre `http://192.168.100.3:8888/`
* Abre el notebook `test_datascience.ipynb` y ejecuta las celdas para demostrar **NumPy, Pandas y Scikit-learn** importados y funcionando dentro de Docker.

---

### DESAFÍO 7: Contenedor Microservicio Flask (Puerto 5000)
En `servidorUbuntu`:
```bash
cd /vagrant/flask_app

# 1. Construir imagen Flask
docker build -t flask-api:v1 .

# 2. Correr el contenedor
docker run -d --name flask-service -p 5000:5000 flask-api:v1
```
* **Prueba en navegador / terminal:**
  ```bash
  curl http://192.168.100.3:5000/
  ```
* Responde con un JSON estructurado de artículos.

---

## 🎓 Preguntas Clave para la Sustentación

**1. ¿Cuál es la diferencia entre una Imagen y un Contenedor?**
> *Una **Imagen** es una plantilla inmutable de solo lectura que contiene el sistema de archivos, dependencias y binarios necesarios para ejecutar una aplicación (el "plano"). Un **Contenedor** es una instancia viva en ejecución de esa imagen, a la cual se le añade una capa de lectura/escritura en memoria.*

**2. ¿Cuál es la diferencia entre una Máquina Virtual y un Contenedor Docker?**
> *Una **Máquina Virtual** virtualiza el hardware completo e incluye un sistema operativo invitado completo con su propio kernel (a través de un hipervisor tipo VirtualBox), lo que consume más RAM y CPU. Un **Contenedor Docker** comparte el kernel del sistema operativo anfitrión (aislando procesos mediante Namespaces y Cgroups de Linux), siendo extremadamente ligero y levantando en milisegundos.*

**3. ¿Cuál es la diferencia entre Docker y LXD?**
> * **LXD** está enfocado en **contenedores de sistema** (simula una máquina completa con múltiples servicios, systemd y usuarios, similar a una VM ligera).
> * **Docker** está enfocado en **contenedores de aplicación / microservicios** (empaqueta típicamente un único proceso principal en primer plano, efímero y portable).*

**4. ¿Qué significan las opciones `-d`, `-p` y `-v` en `docker run`?**
> * **`-d` (*detached*)**: Ejecuta el contenedor en segundo plano liberando la consola.
> * **`-p puertoHost:puertoContenedor`**: Publica/reenvía el puerto del contenedor hacia la interfaz de red del host.
> * **`-v rutaHost:rutaContenedor`**: Crea un enlace de volumen (*bind mount*) que comparte un directorio del host con el contenedor en tiempo real.

**5. ¿Cuál es la diferencia entre `CMD` y `RUN` en un Dockerfile?**
> * **`RUN`**: Se ejecuta durante la **construcción de la imagen** (`docker build`) para instalar paquetes y crear archivos persistentes en las capas de la imagen.
> * **`CMD`**: Es el comando por defecto que se ejecuta cuando el **contenedor arranca** (`docker run`).
