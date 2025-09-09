---
title: "Instalación de Nextcloud All-in-One en TrueNAS"
linkTitle: "Instalación de Nextcloud AIO"
weight: 6
toc: true
---

Mi aventura en el mundo del self-hosting comenzó con Nextcloud. Había escuchado sobre esta plataforma durante un tiempo,
pero la idea de tener mi propia nube personal y escapar de OneDrive
(que era lo que estaba usando en ese momento) me parecía realmente genial.

Mi primer intento consistió en seguir un tutorial de Learn Linux TV para instalarlo en un VPS de Linode. Esa aventura duró poco
porque el almacenamiento en un VPS no es muy barato y tampoco escalaba bien para mis necesidades. Rápidamente me di cuenta de que tener mi propia nube
traía consigo la responsabilidad de proteger mis propios datos. Esta realización me hizo buscar una solución, lo que finalmente me llevó a TrueNAS,
posiblemente el sistema operativo NAS de código abierto más popular que existe. Lo instalé en un portátil viejo, y ese simple acto marcó el verdadero comienzo de mi aventura en el self-hosting.

Me gusta desplegar Nextcloud en TrueNAS porque viene con Docker instalado por defecto.
Y como esta es mi nube y tengo que cuidar mis datos, quiero aprovechar
la protección de datos que TrueNAS proporciona con ZFS, pools, integridad de datos,
snapshots, compresión, autocuración y más.

## Configuración de Docker Compose

Puedes encontrar más información sobre Nextcloud AIO en su repositorio oficial de GitHub [aquí](https://github.com/nextcloud/all-in-one). Por cierto, esta es la forma oficial de instalar Nextcloud.

Me gusta gestionar mis contenedores de Docker con Portainer. Usaré el archivo compose del repositorio de GitHub [aquí](https://github.com/nextcloud/all-in-one/blob/main/compose.yaml) porque vamos a poner Nextcloud detrás de [Pangolin Reverse Proxy](https://docs.digpangolin.com/).

A continuación está mi archivo compose editado. Te animo a que te tomes el tiempo de leer todo el archivo oficial
y entender qué hace cada configuración para que puedas personalizarlo según tus necesidades.

```yaml
services:
  nextcloud-aio-mastercontainer:
    image: ghcr.io/nextcloud-releases/all-in-one:latest
    init: true
    restart: always
    container_name: nextcloud-aio-mastercontainer # Esta línea no se puede cambiar, de lo contrario AIO no funcionará correctamente
    volumes:
      - nextcloud_aio_mastercontainer:/mnt/docker-aio-config # Esta línea no se puede cambiar, de lo contrario la solución de respaldo integrada no funcionará
      - /var/run/docker.sock:/var/run/docker.sock:ro
    network_mode: bridge
    ports:
      - 8080:8080
    environment:
      APACHE_PORT: 11000 # Necesario cuando se ejecuta detrás de un servidor web o proxy inverso.
      APACHE_ADDITIONAL_NETWORK: pangolin # (Opcional) Conecta el contenedor apache a una red docker adicional. Necesario cuando está detrás de un proxy inverso ejecutándose en una red docker diferente en el mismo servidor.
      BORG_RETENTION_POLICY: --keep-within=5d --keep-weekly=2 --keep-monthly=3
      NEXTCLOUD_DATADIR: /mnt/tank/apps/nextcloud/data # Permite establecer el directorio del host para el datadir de Nextcloud. ⚠️⚠️⚠️ Advertencia: ¡no configures o ajustes este valor después de que se complete la instalación inicial de Nextcloud!

volumes:
  nextcloud_aio_mastercontainer:
    name: nextcloud_aio_mastercontainer # Esta línea no se puede cambiar, de lo contrario la solución de respaldo integrada no funcionará
```

{{< callout >}}
**Entendiendo la Política de Retención de Borg**: La configuración `BORG_RETENTION_POLICY` controla cuánto tiempo se conservan tus respaldos automatizados. En esta configuración:
- `--keep-within=5d`: Mantiene todos los respaldos de los últimos 5 días
- `--keep-weekly=2`: Mantiene 2 respaldos semanales después del período de 5 días
- `--keep-monthly=3`: Mantiene 3 respaldos mensuales para retención a largo plazo

Esta política equilibra el uso del almacenamiento con la disponibilidad de respaldos, asegurando que tengas respaldos recientes para recuperación rápida y respaldos más antiguos para escenarios de desastre.
{{< /callout >}}

## Configuración del Almacenamiento

Como siempre al instalar una nueva aplicación, necesitas crear los datasets para la aplicación.

1. Ve a **Datasets** ▸ haz clic en el dataset `apps`
2. Haz clic en **Add Dataset**
3. **Name**: `nextcloud`
4. **Data Preset**: Selecciona **Apps**
5. Haz clic en **Save**

Crea el subdirectorio `data` siguiendo los mismos pasos que el dataset `nextcloud`.

## Configuración del Proxy Inverso Pangolin

Tenemos que configurar Pangolin para reenviar el tráfico al contenedor de Nextcloud. El `Site` ya debe estar creado.

Agregar un `Resources` es bastante simple. Nombraré el mío Nextcloud:

1. Establece el `Subdomain` como quieras, yo usaré `cloud`
2. Asegúrate de seleccionar el `Site` en la sección `Targets Configuration`
3. Establece el `IP/Hostname` al nombre del contenedor Apache que es `nextcloud-aio-apache` y el `Port` a `11000`
4. Ve a la pestaña `Authentication` y deshabilita `Use Platform SSO` y haz clic en `Save Users & Roles`

## Despliegue con Portainer

Ahora vamos a Portainer y creamos un nuevo **stack**. Lo nombraremos `nextcloud` y pega tu archivo compose configurado. Cuando esté listo, haz clic en el botón `Deploy the stack`.

## Configuración Inicial

Una vez que el stack esté desplegado y el estado del contenedor sea `healthy`, podemos ir a la Interfaz de Nextcloud AIO.

Navega a `https://<tu-ip-de-truenas>:8080`. Asegúrate de usar `https` (no `http`) para evitar un error de **Bad Request**.

Deberías ver algo como esto:

![](/images/Nextcloud_Setup.jpeg)

Como dice la advertencia, guarda la frase de contraseña en un lugar seguro. Una vez que hayas hecho eso, haz clic en **Open Login**, pega la frase de contraseña que guardaste y haz clic en **Log in**.

Ahora deberías ver la interfaz de Nextcloud AIO.

![](/images/Nextcloud_AIO.png)

## Verificación del Dominio

El siguiente paso es verificar el nombre de dominio. Ingresa el nombre de dominio configurado en Pangolin y completa el proceso de verificación.

Una vez que el dominio esté verificado, deberías ver la Interfaz de Nextcloud AIO con la sección **Optional Containers**.

Los siguientes están habilitados por defecto:
- **Collabora**
- **Imaginary**
- **Nextcloud Talk**
- **Whiteboard**

Me gusta habilitar los siguientes contenedores:
- **ClamAV**
- **Fulltextsearch**

Y deshabilitar:
- **Nextcloud Talk**
- **Whiteboard**

Después de terminar de habilitar/deshabilitar los contenedores, haz clic en el botón **Save changes** y estarás listo para dar clic en **Download and start containers**. Esto podría tomar un tiempo, así que ten paciencia.

## Primer Inicio de Sesión

Una vez que todos los contenedores estén descargados e iniciados, puedes dar clic en **Open your Nextcloud**. Pero primero, copia la contraseña de administrador y guárdala en un lugar seguro. Por favor usa un gestor de contraseñas. Si no tienes uno, yo personalmente uso y recomiendo **Bitwarden**.

Ingresa `admin` como nombre de usuario, pega la contraseña de administrador que guardaste, haz clic en **Log in**, ¡y ya estás dentro! Bienvenido a tu nueva instancia de Nextcloud.

## Creación de tu Cuenta de Usuario

Como puedes ver, Nextcloud creó un usuario administrador, y al igual que con TrueNAS, prefiero crear una cuenta de usuario separada para mí y darle privilegios de administrador.

Haz clic en el ícono de usuario en la esquina superior derecha, selecciona **Users** y luego haz clic en el botón **New user**. Completa los campos requeridos—me gusta usar minúsculas para el **Account name** (piénsalo como tu nombre de usuario)—y haz clic en **Add new user**.

Cierra e inicia sesión nuevamente con tu cuenta de usuario que acabas de crear.

## Configuración Personal

Ahora puedes comenzar a personalizar tu Nextcloud. Usualmente empiezo yendo a **Personal settings** para cambiar mi foto de perfil y agregar mi número de teléfono (no olvides el código de país) y cualquier otra información que quiera agregar.

## Configuración de Seguridad

La seguridad es muy importante para mí—y debería serlo para ti también—así que siempre configuro 2FA. Ve a **Security** en la barra lateral izquierda y encuentra la sección **Two-Factor Authentication**. Haz clic en **Enable TOTP** para generar un código QR. Escanéalo con tu aplicación de autenticación (recomiendo 2FAS si no tienes una), luego ingresa el código generado para verificar, ¡y listo!

A continuación, genera y guarda tus códigos de respaldo haciendo clic en el botón **Generate backup codes**. Estos códigos te permiten acceder a tu cuenta si alguna vez pierdes tu dispositivo de autenticación. Guárdalos en un lugar seguro, preferiblemente separado de tu gestor de contraseñas. De esa manera, si tu gestor de contraseñas alguna vez se ve comprometido, un atacante aún necesitaría tus códigos de respaldo para acceder a tu cuenta.

## Aplicaciones Cliente

Para hacer la experiencia aún mejor, el siguiente paso es instalar los clientes de Nextcloud en tu teléfono y computadora.

Puedes encontrar los clientes [aquí](https://nextcloud.com/install/#install-clients).

## Limpieza

Finalmente, puedes limpiar eliminando el contenedor e imagen `nextcloud-aio-domaincheck`.