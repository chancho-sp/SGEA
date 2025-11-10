# Instalación y configuración de Odoo 18

En este documento se detalla paso a paso cómo instalar **Odoo 18** en un servidor **Ubuntu Server**, tanto mediante repositorio como con **Docker** (recomendado).

---

## 🖥️ 1. Preparación del entorno

Lo primero será crear una máquina virtual con **Ubuntu Server**.  
Para ello, usaremos **VirtualBox**:

---

## 🌐 2. Configuración inicial del sistema

### 2.1 Cambiar el idioma (locales)

```bash
sudo dpkg-reconfigure locales
```

Selecciona `es_ES.UTF-8` como opción predeterminada.

### 2.2 Configurar el teclado

```bash
sudo dpkg-reconfigure keyboard-configuration
```

Selecciona el modelo adecuado y el idioma **Español**.

> 🔁 **Importante:** Reinicia el sistema después de aplicar los cambios.

---

## 🔐 3. Conexión SSH y red

Actualiza el sistema e instala el servidor SSH para conectarte desde tu máquina principal:

```bash
sudo apt update
sudo apt install openssh-server -y
```

Cambia la red de **NAT** a **Adaptador puente** en VirtualBox.  
Luego ejecuta:

```bash
ip a
```

para conocer la IP del servidor.

Desde tu máquina anfitriona, conéctate vía SSH:

```bash
ssh usuario@IP_DEL_SERVIDOR
```

(Acepta con `yes` y escribe la contraseña del usuario de tu servidor Ubuntu.)

---

## 🗄️ 4. Instalación de PostgreSQL

Instala PostgreSQL, la base de datos que usará Odoo:

```bash
sudo apt install postgresql -y
```

---

## ⚙️ 5. Instalación de Odoo 18 (vía repositorio oficial)

> ⚠️ **Nota:** Al momento de redacción (noviembre de 2025), Odoo 18 puede encontrarse en versión *nightly* o de desarrollo, por lo que el repositorio podría no estar disponible.  
> Si ocurre un error, utiliza la instalación mediante **Docker** (ver sección siguiente).

Agrega la clave GPG y el repositorio:

```bash
wget -q -O - https://nightly.odoo.com/odoo.key | sudo gpg --dearmor -o /usr/share/keyrings/odoo-archive-keyring.gpg

echo 'deb [signed-by=/usr/share/keyrings/odoo-archive-keyring.gpg] https://nightly.odoo.com/18.0/nightly/deb/ ./' | sudo tee /etc/apt/sources.list.d/odoo.list

sudo apt-get update
sudo apt-get install odoo -y
```

---

## 🐳 6. Instalación de Odoo 18 mediante Docker (recomendado)

Instalaremos docker para poder ejecutar Odoo en contenedores. Esto nos permitirá desplegar nuestra instalación de Odoo fácilmente en cualquier otra máquina.

### 6.1 Configurar el repositorio de Docker

Ejecuta los siguientes comandos **línea por línea**:

```bash
# Añadir la clave GPG oficial de Docker
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Añadir el repositorio de Docker a las fuentes de APT
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu   $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
```

### 6.2 Instalar Docker Engine

```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Verifica que Docker esté activo:

```bash
sudo systemctl status docker
```

---

## 🧱 7. Crear contenedores para Odoo y PostgreSQL

Crearemos una red Docker, un contenedor de base de datos PostgreSQL y otro con el servidor Odoo.

### 7.1 Crear la red Docker

```bash
docker network create odoo-net
```

### 7.2 Crear el contenedor de PostgreSQL

```bash
docker run -d   --name db   --network odoo-net   -v /home/usuario/OdooDesarrollo/dataPG:/var/lib/postgresql/data   -e POSTGRES_USER=odoo   -e POSTGRES_PASSWORD=odoo   -e POSTGRES_DB=postgres   postgres:15
```

### 7.3 Crear el contenedor de Odoo

```bash
docker run -d   -p 8069:8069   --name odooprod   --network odoo-net   odoo:18
```

---

## 🌍 8. Acceder a Odoo 18

Comprueba la IP de tu servidor:

```bash
ip a
```

Luego abre en tu navegador:

```
http://dirección_IP:8069
```

Se mostrará el asistente de configuración inicial.  
Introduce tus datos y crea tu primera base de datos para comenzar a usar Odoo 18.

---

## 🧬 9. Conexión con GitHub

Configura tu nombre y correo de usuario:

```bash
git config --global user.name "chancho-sp"
git config --global user.email "josseamar@alu.edu.gva.es"
```

### Comandos útiles de Git

- Crear una nueva rama:
  ```bash
  git checkout -b nombre_rama
  ```

- Cambiar de rama:
  ```bash
  git checkout nombre_rama
  ```

- Clonar un repositorio:
  ```bash
  git clone URL_DEL_REPOSITORIO
  ```

- Descargar todos los cambios remotos:
  ```bash
  git fetch --all
  ```

- Actualizar la rama local:
  ```bash
  git pull
  ```

- Comprimir un archivo al máximo nivel:
  ```bash
  tar cvf micomprimido.tar.xz -I 'xz -9'
  ```
- Subir archivos a github
```bash
git add .
git commit -m "Actualización guía instalación Odoo 18"
git push origin main
```
---

## 10. Conclusión

Con estos pasos, tendrás **Odoo 18** funcionando correctamente sobre **Ubuntu Server**, con base de datos **PostgreSQL** y contenedores **Docker** configurados para producción.  

---
