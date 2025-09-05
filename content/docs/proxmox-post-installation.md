---
title: "Configuración Post-Instalación de Proxmox VE 9"
linkTitle: "Configuración Post-Instalación"
weight: 3
---

Después de completar la instalación inicial de Proxmox VE 9, Existen varios pasos de configuración importantes para optimizar tu sistema. Esta guía te acompaña a través de las tareas esenciales posteriores a la instalación mediante scripts mantenidos por la comunidad.


## Opcional: Instalación de Fish

Personalmente, prefiero usar Fish para una mejor experiencia de línea de comandos, pero esto es completamente opcional y no es necesario para comenzar el proceso posterior a la instalación. Si quieres probarlo, puedes instalarlo con:

```bash
apt install fish -y
```

{{< callout type="info" >}}
Fish proporciona resaltado de sintaxis, autocompletado y una mejor experiencia de usuario comparado con Bash por defecto.
{{< /callout >}}

## Script auxiliar posterior a la instalación de Proxmox VE

Utilizaremos un script mantenido por la comunidad [Proxmox VE Helper-Scripts](https://community-scripts.github.io/ProxmoxVE/scripts?id=post-pve-install) para automatizar la mayoría de las tareas de configuración.

### Ejecutando el Script

Ejecuta el siguiente comando para descargar y ejecutar el script:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
```

### Proceso de Ejecución del Script

Cuando ejecutes el script, verás el banner en arte ASCII y se te pedirá que inicies:

```
    ____ _    ________   ____             __     ____           __        ____
   / __ \ |  / / ____/  / __ \____  _____/ /_   /  _/___  _____/ /_____ _/ / /
  / /_/ / | / / __/    / /_/ / __ \/ ___/ __/   / // __ \/ ___/ __/ __ `/ / /
 / ____/| |/ / /___   / ____/ /_/ (__  ) /_   _/ // / / (__  ) /_/ /_/ / / /
/_/     |___/_____/  /_/    \____/____/\__/  /___/_/ /_/____/\__/\__,_/_/_/

This script will Perform Post Install Routines.

Start the Proxmox VE Post Install Script (y/n)?
```

**Respuesta: `y`**

## Pasos de Configuración

El script te guiará a través de varias opciones de configuración. Aquí está lo que configura cada paso:

### 1. Configuración de Repositorios

#### Eliminación de Repositorio Enterprise
El script detectará repositorios enterprise existentes y preguntará qué hacer:

```
'pve-enterprise' repository already exists.
What do you want to do? 
keep    Keep as is
disable Comment out (disable) this repo
delete  Delete this repo file.
```

**Opción recomendada: `delete`**

{{< callout type="warning" >}}
Los repositorios enterprise requieren una suscripción válida de Proxmox. Para laboratorios caseros y uso no comercial, se recomienda eliminarlos para evitar errores de actualización.
{{< /callout >}}

Se te hará la misma pregunta para el repositorio 'ceph enterprise':
**Opción recomendada: `delete`**

#### Repositorio No-Subscription
```
The 'pve-no-subscription' repository provides access
to all of the open-source components of Proxmox VE. 

Add 'pve-no-subscription' repository (deb822)?
```

**Opción recomendada: `yes`**

{{< callout >}}
El repositorio no-subscription proporciona actualizaciones estables para Proxmox VE sin requerir una suscripción comercial.
{{< /callout >}}

#### Repositorio de Pruebas
```
The 'pve-test' repository can give advanced users
access to new features and updates before they are
officially released.

Add (Disabled) 'pvetest' repository (deb822)?
```

**Opción recomendada: `no`**

{{< callout type="info" >}}
El repositorio de pruebas contiene características de vanguardia que pueden no ser estables. Solo habilita esto en entornos de desarrollo.
{{< /callout >}}

### 2. Eliminación del Recordatorio de Suscripción

```
This will disable the nag message reminding you to
purchase a subscription every time you log in to the
web interface.
Disable subscription nag?
```

**Opción recomendada: `yes`**

{{< callout >}}
Esto elimina el popup de suscripción que aparece al acceder a la interfaz web sin una suscripción comercial.
{{< /callout >}}

El script mostrará un mensaje para apoyar monetariamente a los desarrolladores:
```
Supporting the software's development team is essential. Check their official website's Support Subscriptions for pricing. Without their dedicated work, we wouldn't have this exceptional software.
```

**Presiona Enter para continuar**

### 3. Configuración de Alta Disponibilidad

```
HIGH AVAILABILITY

If you plan to utilize a single node instead of a clustered environment, you can disable unnecessary high availability (HA) services, thus reclaiming system resources.

If HA becomes necessary at a later stage, the services can be re-enabled.

Disable high availability?
```

**Para configuraciones de nodo único: `yes`**

**Para entornos de clúster: `no`**

{{< callout type="info" >}}
Los servicios de Alta Disponibilidad solo son necesarios en entornos de clúster multi-nodo. Deshabilitarlos en instalaciones de nodo único libera recursos del sistema.
{{< /callout >}}

### 4. Configuración de Corosync

```
COROSYNC
Disable Corosync for a Proxmox VE Cluster?
```

**Para configuraciones de nodo único: `yes`**

**Para entornos de clúster: `no`**

{{< callout >}}
Corosync es la capa de comunicación del clúster. No se necesita para instalaciones de nodo único pero es esencial para clústeres.
{{< /callout >}}

### 5. Actualización del Sistema

```
UPDATE
Update Proxmox VE now?
```

**Opción recomendada: `yes`**

El script actualizará los paquetes de tu sistema a las últimas versiones.

## Resumen de Finalización del Script

Después de que todas las configuraciones sean aplicadas, verás un resumen:

```
 ✓ Deleted 'pve-enterprise' repository file
 ✓ Deleted 'ceph enterprise' repository file
 ✓ Added 'pve-no-subscription' repository
 ✓ 'ceph' package repository (no-subscription) already exists (skipped)
 ✗ Selected no to Adding 'pvetest' repository
 ✓ Disabled subscription nag (Delete browser cache)
 ✓ Disabled high availability
 ✓ Disabled Corosync
 - Updating Proxmox VE (Patience)...
```

### Recordatorio Post-Instalación

El script mostrará un recordatorio importante:

```
┌──────────────────────────┤ Post-Install Reminder ├───────────────────────────┐
│ IMPORTANT:                                                                   │ 
│                                                                              │ 
│ If you have multiple Proxmox VE hosts in a cluster, please make sure to run  │ 
│ this script on every node individually.                                      │ 
│                                                                              │ 
│ After completing these steps, it is strongly recommended to REBOOT your      │ 
│ After the upgrade or post-install routines, always clear your browser cache  │ 
│ or perform a hard reload (Ctrl+Shift+R) before using the Proxmox VE Web UI   │ 
│                                    <Ok>                                      │ 
└──────────────────────────────────────────────────────────────────────────────┘
```

**Presiona Enter para continuar**

### Reinicio del Sistema

```
REBOOT
Reboot Proxmox VE now? (recommended)
```

**Opción recomendada: `yes`**

{{< callout type="important" >}}
Se recomienda encarecidamente un reinicio después de la configuración post-instalación para asegurar que todos los cambios tomen efecto apropiadamente.
{{< /callout >}}

## Resumen Final

La post-instalación completada mostrará:

```
 ✓ Deleted 'pve-enterprise' repository file
 ✓ Deleted 'ceph enterprise' repository file
 ✓ Added 'pve-no-subscription' repository
 ✓ 'ceph' package repository (no-subscription) already exists (skipped)
 ✗ Selected no to Adding 'pvetest' repository
 ✓ Disabled subscription nag (Delete browser cache)
 ✓ Disabled high availability
 ✓ Disabled Corosync
 ✓ Updated Proxmox VE
 ✓ Completed Post Install Routines
```

## Pasos Post-Reinicio

Después de que el sistema se reinicie:

1. **Limpia la caché del navegador** o realiza una actualización forzada (Ctrl+Shift+R) antes de acceder a la interfaz web
2. **Verifica el acceso web** en `https://tu-ip-proxmox:8006`
