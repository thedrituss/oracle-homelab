# Oracle Cloud HomeLab ☁️

![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Oracle](https://img.shields.io/badge/Oracle_Cloud-F80000?style=for-the-badge&logo=oracle&logoColor=white)

Repositorio de **Infraestructura como Código (IaC)** para desplegar un HomeLab seguro y automatizado en una instancia "Always Free" de Oracle Cloud (ARM64).

El objetivo es autohospedar servicios personales manteniendo la privacidad, con acceso remoto seguro vía VPN y monitorización de recursos.

## 🏗️ Arquitectura

- **Host:** Oracle Cloud Infrastructure (VM.Standard.A1.Flex)
  - **CPU:** 4 OCPUs (ARM64 Ampere)
  - **RAM:** 24 GB
  - **OS:** Ubuntu Server 22.04 LTS
- **Orquestación:** Docker Compose & Portainer
- **Red:** Acceso restringido vía WireGuard VPN

## 📋 Prerrequisitos del Sistema

Antes de desplegar los servicios, es necesario preparar el servidor (Host).

### 1. Instalación de Docker Engine
El servidor debe tener instalado Docker y el plugin de Docker Compose.

```bash
# Actualizar repositorios
sudo apt update && sudo apt upgrade -y

# Instalar Docker
curl -fsSL [https://get.docker.com](https://get.docker.com) -o get-docker.sh
sudo sh get-docker.sh

# Añadir el usuario actual al grupo Docker (para no usar sudo siempre)
sudo usermod -aG docker $USER
newgrp docker
```



### 2. Estructura de Directorios

Para mantener el orden y la persistencia de datos, se recomienda crear la siguiente estructura en el Host:

``` bash
mkdir -p ~/docker/adguard/conf ~/docker/adguard/work
mkdir -p ~/docker/syncthing/config ~/docker/syncthing/data
mkdir -p ~/docker/heimdall/config
mkdir -p ~/docker/portainer/data
```

### ⚙️ Consideraciones Técnicas (Docker)

El problema del UID/GID 1001

A diferencia de las distribuciones estándar, la imagen de Ubuntu en Oracle Cloud asigna al usuario por defecto (ubuntu) el ID 1001 en lugar del estándar 1000.

Para evitar problemas de permisos de escritura en los volúmenes montados (especialmente en Syncthing y Heimdall), es crítico definir las variables de entorno PUID y PGID correctamente en los archivos docker-compose.yml:

``` yaml
environment:
  - PUID=1001  # ID específico del usuario ubuntu en Oracle
  - PGID=1001
```

Si usas otra distribución o proveedor, verifica tu ID con el comando id $USER.

### 🚀 Servicios y Despliegue

| Servicio | Puerto (Interno) | Descripción |
| :--- | :--- | :--- |
| **WireGuard** | 51820 (UDP) | VPN. Punto de entrada único y seguro a la red. |
| **AdGuard Home** | 8080 | DNS Sinkhole. Bloqueo de publicidad y trackers a nivel de red. |
| **Syncthing** | 8384 | Cloud Personal. Sincronización P2P de archivos. |
| **Heimdall** | 8090 | Dashboard. Panel de control de servicios. |
| **Portainer** | 9443 | Gestión Docker. UI para administrar contenedores. |

#### Cómo desplegar un servicio

1. Clonar repositorio:

``` bash
git clone [https://github.com/tu-usuario/oracle-homelab.git](https://github.com/tu-usuario/oracle-homelab.git)
cd oracle-homelab
```

2. Crear un archivo .env (si aplica) con las credenciales necesarias (ej. contraseñas de Wireguard)

3. Levantar el stack:

``` bash
cd adguard
docker-compose up -d
```

### 🔒 Seguridad

- Firewall: Todos los puertos de gestión (8080, 8384, 9443, etc.) están bloqueados en la Oracle Cloud Security List y en iptables para el acceso público. Solo son accesibles estando conectado a la VPN WireGuard.

- Datos Sensibles: Las carpetas config/ y data/ están excluidas del control de versiones mediante .gitignore.
