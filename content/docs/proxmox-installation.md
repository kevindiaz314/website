---
title: "Instalación de Proxmox VE 9"
linkTitle: "Instalación de Proxmox VE 9"
weight: 2
---

Proxmox Virtual Environment (Proxmox VE) es una plataforma de virtualización de código abierto que combina virtualización de contenedores y virtualización completa. Esta guía te acompaña paso a paso durante el proceso de instalación de Proxmox VE 9.

## Proceso de Instalación

La instalación de Proxmox VE es sencilla y consta de varias pantallas de configuración. Esto es lo que encontrarás:

### 1. Acuerdo de Licencia de Usuario Final

La primera pantalla que aparece es el **Acuerdo de Licencia de Usuario Final (EULA)**. Acéptalo para continuar con la instalación.

### 2. Selección del Disco de Destino y Sistema de Archivos

A continuación, deberás elegir:
- **Disco de destino**: Selecciona la unidad donde se instalará Proxmox VE
- **Sistema de archivos**: Elige entre las opciones disponibles:
  - **ext4**: Sistema de archivos tradicional de Linux, confiable y bien probado
  - **xfs**: Sistema de archivos de alto rendimiento, bueno para archivos grandes
  - **btrfs**: Sistema de archivos moderno con características avanzadas como la capacidad de crear copias de seguridad del sistema en un momento específico.
  - **ZFS**: Sistema de archivos avanzado con RAID integrado, compresión y características de integridad de datos

{{< callout type="info" >}}
Para esta instalación, se seleccionó **ZFS RAID0**. ZFS ofrece excelentes características de integridad de datos y capacidades RAID integradas, lo que lo hace ideal para entornos de virtualización.
{{< /callout >}}

### 3. Configuración de Ubicación y Teclado

Configura tus ajustes regionales:
- **País**: Selecciona tu país para configurar la zona horaria y la configuración regional
- **Zona horaria**: Elige tu zona horaria local
- **Distribución del teclado**: Selecciona la distribución de teclado adecuada para tu región

### 4. Contraseña de Administrador y Correo Electrónico

Configura la cuenta root:
- **Contraseña**: Introduce una contraseña segura para el usuario root
- **Confirmar contraseña**: Vuelve a introducir la contraseña para confirmarla
- **Correo electrónico**: Proporciona una dirección de correo electrónico para notificaciones del sistema y alertas

{{< callout type="warning" >}}
Elige una contraseña segura, ya que la cuenta root tiene privilegios administrativos completos sobre tu sistema Proxmox VE.
{{< /callout >}}

### 5. Configuración de Red de Gestión

Este es uno de los pasos de configuración más importantes. Configurarás:

#### Interfaz de Red
- **Interfaz de gestión**: Selecciona el puerto de red que se utilizará para la gestión de Proxmox VE
- Si hay múltiples puertos de red disponibles, elige el que esté conectado a tu red (indicado por un punto verde)
- En configuraciones de un solo puerto, solo una opción estará disponible y preseleccionada

#### Configuración de Red
- **Nombre de host (FQDN)**: Introduce el nombre de dominio completo de tu servidor Proxmox
  - Ejemplo: `proxmox.example.local`
  - Este nombre de host se utilizará para acceder a la interfaz web y para la comunicación del clúster

- **Dirección IP (CIDR)**: Especifica la dirección IP estática para tu servidor Proxmox
  - Ejemplo: `10.0.0.10/24`
  - El `/24` indica una máscara de subred de 255.255.255.0
  - Elige una dirección IP que esté dentro del rango de tu red pero fuera del rango DHCP

- **Puerta de enlace**: Introduce la dirección IP de la puerta de enlace de tu red
  - Ejemplo: `10.0.0.1`
  - Esta es típicamente la dirección IP de tu router

- **Servidor DNS**: Especifica la dirección IP del servidor DNS
  - Ejemplo: `10.0.0.1` (usando la puerta de enlace como DNS)
  - También puedes usar servidores DNS públicos como `8.8.8.8` o `1.1.1.1`

{{< callout >}}
**Entendiendo FQDN**: El Nombre de Dominio Completamente Calificado debe resolver a la dirección IP de tu servidor Proxmox. Esto es importante para la generación adecuada de certificados SSL y operaciones de clúster.
{{< /callout >}}

{{< callout type="info" >}}
**Planificación de Direcciones IP**: Elige una dirección IP estática fuera de tu rango DHCP para evitar conflictos. Documenta esta dirección IP ya que la usarás para acceder a la interfaz web de Proxmox.
{{< /callout >}}

### 6. Resumen de Instalación

La pantalla de configuración final muestra una tabla resumen con todas las opciones seleccionadas:
- **Sistema de archivos**
- **Disco(s)**
- **País**
- **Zona horaria**
- **Mapa del teclado**
- **Correo electrónico**
- **Interfaz de administración**
- **Nombre de host**
- **IP CIDR**
- **Puerta de enlace**
- **DNS**

Revisa todas las configuraciones cuidadosamente antes de continuar.

#### Opción de Reinicio Automático
En la parte inferior de la pantalla de resumen, encontrarás una casilla de verificación:
- **"Reiniciar automáticamente después de una instalación exitosa"** (marcada por defecto)
- Deja esta casilla marcada para reinicio automático, o desmárcala si deseas reiniciar manualmente

### 7. Progreso de Instalación

Durante el proceso de instalación, se muestra la pantalla **Plataforma de Virtualización**. Esta pantalla proporciona información sobre Proxmox VE, incluyendo:

- **Plataforma de Virtualización de Código Abierto**: Proxmox VE es completamente de código abierto
- **Virtualización de Contenedores**: Soporte para contenedores LXC
- **Virtualización Completa (KVM)**: Soporte para máquinas virtuales usando KVM
- **Recursos Adicionales**: 
  - Visita [proxmox.com](https://www.proxmox.com) para información oficial
  - Consulta el [Wiki de Proxmox VE](https://pve.proxmox.com/wiki/) para documentación y guías

Después de que la instalación se haya completado y reiniciado el sistema, puedes acceder a tu instalación de Proxmox VE como se describe a continuación.

## Post-Instalación

Ve a la interfaz web de Proxmox VE navegando a la dirección IP que configuraste en el paso 5.

Por ejemplo, con la dirección IP utilizada en esta guía:
```
https://10.0.0.10:8006
```

Inicia sesión usando:
- **Nombre de usuario**: `root`
- **Contraseña**: La contraseña que configuraste durante la instalación

## Configuración Post-Instalación

Ahora que has instalado Proxmox VE, puedes proceder a la configuración [post-instalación](../proxmox-post-installation/).

{{< callout type="important" >}}
**¡No te saltes esta configuración!** La guía cubre pasos de configuración esenciales incluyendo configuración de repositorios, gestión de suscripciones y optimización del sistema que son cruciales para una instalación de Proxmox VE que funcione apropiadamente.
{{< /callout >}}
