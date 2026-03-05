# Sistema de Red Corporativa Multi-Campus - UJCV

## Descripción Técnica
Infraestructura de red de alto rendimiento diseñada para la **Universidad Jose Cecilio del Valle (UJCV)**. El proyecto interconecta de forma robusta los campus de **Tegucigalpa** y **Comayagua**, aplicando un diseño jerárquico que garantiza redundancia en Capa 2, enrutamiento inter-VLAN en Capa 3 y un modelo de seguridad perimetral basado en AAA.

---

## Topología Lógica y Física
![Arquitectura UJCV](https://github.com/user-attachments/assets/9d64a738-1c7e-44f9-9572-d0fe5f7e40ec)

---

## Estructura Geográfica y Edificios

### Campus Tegucigalpa (Sede Principal)
La red se divide físicamente para aislar dominios de falla y mejorar la gestión:
1. **Edificio Administrativo:** Concentra el Core de la red, el Servidor RADIUS y el Switch de Distribución L3 (`Sw-InterVlan-Teg`).
2. **Edificio Educativo:** Aloja los switches de acceso para laboratorios, aulas y salas de maestros.
* **Redundancia:** Enlaces **EtherChannel (LACP)** activos entre edificios para alta disponibilidad.

### Campus Comayagua
1. **Edificio de Sede:** Funciona como sucursal remota con su propio Router de Campus, gestionando DHCP local y salida WAN independiente.

---

## Infraestructura Inalámbrica (WLC Dual)
Cada campus cuenta con su propia **Wireless LAN Controller (WLC)** para la gestión de Access Points ligeros, evitando dependencia del enlace serial para el tráfico Wi-Fi local.

| Atributo | Campus Tegucigalpa | Campus Comayagua |
| :--- | :--- | :--- |
| **SSID** | `UJCV-TEGUS` | `UJCV-COMAYAGUA` |
| **Password** | `#Ujcv2025` | `#Ujcv2025` |
| **Seguridad** | WPA2 + AAA Radius (Local) | WPA2 + AAA Radius (Local) |

---

## Segmentación Física de Puertos
Configuración de puertos en los switches de acceso para garantizar el aislamiento por departamento:

| Rango de Puertos | VLAN | Subred | Departamento |
| :--- | :--- | :--- | :--- |
| **fa0/1 - fa0/3** | 10 | 192.168.10.0/24 | **Estudiantes** |
| **fa0/4 - fa0/6** | 20 | 192.168.20.0/24 | **Maestros** |
| **fa0/7 - fa0/10** | 30 | 192.168.30.0/24 | **Administración** |

---

## Seguridad y Control de Acceso (ACLs)

### 1. Servidor AAA / RADIUS
* **IP Servidor:** `10.10.11.2` (Configurado en el Core de Tegucigalpa)
* **Shared Secret:** `C!sc0`
* **Protocolo:** SSHv2 obligatorio para administración remota en todos los nodos.

### 2. Políticas de Tráfico
* **Bloqueo Estudiantes:** Los usuarios de la VLAN 10 tienen denegado el acceso a la red de la VLAN 30 (Administración).
* **Gestión Limitada:** Solo la VLAN 30 tiene permisos para alcanzar las interfaces de gestión de los routers y WLCs.

---

## Bitácora de Recuperación (Password Recovery)
Registro de intervención técnica en el router de Tegucigalpa (Local):
1. **Diagnóstico:** Bloqueo de acceso en `enable secret` con hash MD5 compartido.
2. **Procedimiento:** Acceso via ROMMON, cambio de registro a `0x2142` para bypass del `startup-config`.
3. **Restauración:** Carga de configuración, actualización de contraseñas para el usuario `emilio` y el modo `enable`.
4. **Finalización:** Retorno al registro `0x2102` y validación de interfaces Serial/Gigabit.

---

## Especificaciones de Capa 2
* **STP (PVST+):** `Sw-Acad-1` configurado como **Root Bridge** (Prioridad 0) para las VLANs 10, 20 y 30.
* **Troncales:** Encapsulamiento **802.1Q** en todos los puertos de enlace entre switches.

**Ingeniería en Infotecnología** | Emilio R.
