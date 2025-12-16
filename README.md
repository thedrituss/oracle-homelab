# Oracle Cloud HomeLab ☁️

Este repositorio contiene la configuración de **Infraestructura como Código (IaC)** para mi servidor personal alojado en Oracle Cloud (ARM64 Ampere - 4 OCPUs, 24GB RAM).

El objetivo del proyecto es desplegar un entorno seguro, automatizado y monitorizado para servicios personales utilizando **Docker** y **Docker Compose**.

## 🏗️ Arquitectura

- **SO Host:** Ubuntu Server (Oracle Cloud Free Tier)
- **Gestión de Contenedores:** Portainer + Docker Compose
- **Seguridad y Red:** WireGuard (VPN) + AdGuard Home (DNS Sinkhole)
- **Almacenamiento:** Syncthing (Sincronización P2P con Arch Linux)
- **Dashboard:** Heimdall

## 🚀 Servicios Desplegados

| Servicio | Puerto Interno | Descripción |
| :--- | :--- | :--- |
| **WireGuard** | 51820 (UDP) | VPN Tunnel para acceso seguro a la red interna. |
| **AdGuard Home** | 8080 | Servidor DNS que bloquea publicidad y rastreadores a nivel de red. |
| **Syncthing** | 8384 | Sincronización continua de archivos (Org-mode, notas) entre dispositivos. |
| **Heimdall** | 8090 | Dashboard unificado para acceder a todos los servicios. |
| **Portainer** | 9443 | Gestión visual de contenedores y stacks. |

## 🛠️ Despliegue

Cada servicio está contenido en su propia carpeta para mantener la modularidad.

```bash
# Ejemplo para levantar AdGuard
cd adguard
docker-compose up -d
