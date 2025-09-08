---
title: "Instalando Portainer en TrueNAS Community Edition con Vinculación de IP"
linkTitle: "Instalación de Portainer"
weight: 5
---

{{< callout type="info" >}}
Esto es más que un simple tutorial para instalar Portainer porque voy a usar una funcionalidad recientemente introducida en TrueNAS que permite vincular aplicaciones a una dirección IP específica. Esta configuración elimina la necesidad de números de puerto en las URLs y crea un entorno de homelab más limpio y organizado.
{{< /callout >}}

## Requisitos Previos

Antes de comenzar, asegúrate de haber completado la [configuración post-instalación de TrueNAS](../truenas-post-installation/). Construiremos sobre esas configuraciones, particularmente el dataset `apps` que creamos.

## Crear un Dataset para Portainer

Lo primero que haremos es crear un nuevo dataset para la aplicación. En mi caso, lo crearé dentro del dataset `apps` que configuramos anteriormente.

1. Ve a Datasets ▸ selecciona el dataset `apps`
2. Haz clic en **Add Dataset**
3. Nombre: `portainer`
4. Deja las otras configuraciones por defecto ▸ Save

## Agregar una Interfaz de Red Puente

Ahora viene la parte interesante: crearemos un puente de red que nos permite vincular Portainer a su propia dirección IP.

{{< callout type="info" >}}
Puedes encontrar más información en la documentación de TrueNAS: [Configurando un Puente de Red](https://www.truenas.com/docs/scale/25.10/scaletutorials/network/interfaces/settingupbridge/)
{{< /callout >}}

### Creando el Puente

1. Ve a System ▸ Network ▸ Interfaces ▸ Add
2. **Type**: Selecciona `Bridge`
3. **Name**: `br0` (porque a los programadores nos gusta empezar a contar desde 0)
4. **Description**: `Bridge Network for Apps` (opcional, pero acá nos gusta seguir buenas prácticas)

{{< callout >}}
Ten en cuenta que el nombre de la red debe empezar con `br` seguido de un número único.
{{< /callout >}}

### Configuración de Direcciones IP

**Define Static IP Addresses** está seleccionado por defecto, y lo dejaremos así.

Ahora vamos a agregar las direcciones IP:

1. **Primera Dirección IP**: Esta debe ser la dirección IP ya asignada a tu interfaz de TrueNAS
   - En mi caso, es `10.21.30.100/24`

    Nota al margen: Me gusta usar redes clase A porque son más fáciles de recordar y me ahorra algunas teclas

2. **Segunda Dirección IP**: Esta será dedicada para Portainer
   - Usaré `10.21.30.101/24`

    Nota al margen: Incremento el último octeto para simplificar y saber que esta dirección IP está asociada con este servidor TrueNAS (tengo dos servidores).

{{< callout type="warning" >}}
Asegúrate de que la dirección que elijas no esté ya en uso. Esto es muy importante porque si lo está, tendrás un conflicto en tu router.
{{< /callout >}}

3. **Bridge Members**: Selecciona tu interfaz de red existente
4. Deja el resto de configuraciones por defecto
5. Haz clic en **Save**

### Probando los Cambios de Red

Verás dos botones: **Test Changes** y **Revert Changes**.

1. Haz clic en **Test Changes**
2. Si todo está configurado correctamente, la página se recargará y podrás guardar los cambios
3. Si algo sale mal, no te preocupes: los cambios se revierten automáticamente después de 60 segundos

## Configurar la Vinculación de IP de la GUI de TrueNAS

Ahora técnicamente ya podríamos vincular la aplicación a la dirección IP `.101`, pero hay un problema: no podemos usar el puerto 443 porque la GUI de TrueNAS ahora puede ser accedida desde ambas direcciones IP (`.100` y `.101`).

Para solucionar esto, necesitamos vincular la GUI de TrueNAS a una sola dirección IP:

1. Ve a System ▸ General Settings ▸ GUI
2. **Web Interface IP Address**: Cambia a `10.21.30.100`
3. Ya que estamos aquí, si no leíste mi [guía de post-instalación de TrueNAS](../truenas-post-installation/), tal vez quieras configurar:
   - **HTTP Redirect to HTTPS**
   - **Show Console Messages**
   - Y ajustar las opciones de tema
4. Haz clic en **Save**

Ahora la GUI de TrueNAS solo será accesible a través de la dirección IP `.100`, liberando el puerto 443 en la dirección IP `.101` para Portainer.

## Instalar Portainer

¡Estamos listos para instalar Portainer!

1. Ve a **Apps** ▸ Busca `Portainer` ▸ Haz clic en **Install**
2. La imagen de Community Edition está configurada por defecto - esto está bien

### Configuración de Red

1. **Publish port on the host for external access** está seleccionado por defecto - déjalo así
2. **Port**: Cambia del puerto por defecto a `443`
3. Haz clic en **Add** junto a **Host IPs**
4. Selecciona la dirección IP `10.21.30.101`
5. Deja el resto por defecto

### Configuración de Almacenamiento

En **Storage Configuration**:
1. **Portainer Storage**: Cambia a `Host Path`
2. Selecciona el dataset que creaste anteriormente (`/mnt/tank/apps/portainer`)

### Configuración de Recursos

Lo último por configurar es **Resources Configuration** si quieres ajustarlo. Personalmente, creo que 2 CPUs y 4 GB de memoria es demasiado para Portainer, así que cambiaré la memoria a 1 GB.

Haz clic en **Install**.

## Acceder a Portainer

Una vez que la instalación se complete:

1. Haz clic en el botón **HTTPS** en la sección **Application Info**
2. Esto abrirá una nueva pestaña con la GUI de Portainer
3. Necesitarás aceptar la advertencia de seguridad ya que Portainer usa certificados auto-firmados - esto está bien

{{< callout type="warning" >}}
Recuerda completar la configuración inicial dentro de los 5 minutos posteriores a instalar la aplicación. De lo contrario, Portainer bloqueará la configuración por razones de seguridad y tendrás que reiniciar el contenedor (aplicación).
{{< /callout >}}

## Por Qué Esta Configuración es Genial

Lo que realmente me gusta de esta configuración es que podemos acceder a Portainer por su propia dirección IP sin tener que escribir ningún número de puerto. Como uso Bitwarden como mi gestor de contraseñas, uso el atajo `Ctrl + Shift + L` para llenar automáticamente el usuario y contraseña.

No tengo que mover mi mano para agarrar el mouse, hacer clic en la extensión de Bitwarden, buscar las credenciales de Portainer y hacer clic en autocompletar porque tendría dos cuentas (TrueNAS y Portainer), y aún más si instalo más aplicaciones que se vinculen a la dirección IP de TrueNAS y solo usen un número de puerto diferente.

Eliminar fricción de mi flujo de trabajo es muy importante para mí.
