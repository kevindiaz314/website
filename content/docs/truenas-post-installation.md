---
title: "TrueNAS Community 25.10 (Goldeye) Post‑instalación"
linkTitle: "Post‑instalación de TrueNAS"
weight: 4
---

{{< callout type="info" >}}
La instalación de TrueNAS Community es bastante sencilla y guiada por el instalador.
Así que esto no es un tutorial en sí, sino una documentación de cómo configuro mi servidor.
Hay muchas formas válidas de configurar TrueNAS; esta es simplemente la mía.
{{< /callout >}}

## Cambiar la zona horaria

TrueNAS viene por defecto con Los Ángeles. Cámbiala a tu zona horaria local.

- Ve a System → General Settings → Localization

## Crear un pool de almacenamiento

1. Ve a Storage → Pools → Add → Create new pool
2. Elijo el nombre más típico para un pool: `tank`
3. Layout: selecciono Mirror (tengo dos SSD)
4. Disk size: con dos SSD idénticos de 2 TB, la opción disponible suele ser “1.82 TiB (SSD)”
5. Width queda en 2 (coincide con dos discos) y Number of VDEVs queda en 1 — lo dejo así
6. Clic en Next → dejo los pasos opcionales con sus valores por defecto → Save And Go To Review
7. Reviso la configuración → Create Pool
8. Confirmo la advertencia: “The contents of all added disks will be erased.”

{{< callout type="tip" >}}
Es buena práctica usar discos idénticos en un pool. Aunque dos discos tengan la misma capacidad marcada, el tamaño utilizable puede variar ligeramente. La capacidad del pool se limita por el disco más pequeño, así que usar unidades iguales evita sorpresas.
{{< /callout >}}

## Crear una cuenta de usuario

Ve a Credentials → Users y haz clic en Add (arriba a la derecha).

Referencia de documentación: [Managing Users](https://www.truenas.com/docs/scale/scaletutorials/credentials/managelocalusersscale/)

{{< callout type="info" >}}
Esa página puede abrir por defecto la versión “Next” (desarrollo). Usa el selector de versión (arriba a la derecha) para elegir tu versión instalada.
{{< /callout >}}

- Los campos obligatorios están marcados con un asterisco
- El email no es obligatorio, pero me gusta incluirlo
- El UID viene por defecto con 3000 — lo dejo tal cual

> Ayuda: UID — Las cuentas de usuario tienen un ID mayor que 1000 y las cuentas de sistema suelen tener un ID igual al puerto por defecto del servicio.

- Auxiliary Groups es opcional, pero agrego `builtin_administrators` para darle privilegios de administrador a mi usuario
- Create new primary group: Yes (por defecto)

### Directorio Home y permisos

Para el Home Directory, lo apunto a un dataset dentro de `tank`:

1. Abre el selector de dataset → elige `tank`
2. Clic en Create Dataset y nómbralo con el nombre de usuario que elegiste antes
3. Dejo “Create Home Directory” desmarcado

{{< callout >}}
¿Por qué así? Simplemente me gusta ver el dataset del home de forma explícita en Datasets, lo que hace ver las cosas mas organizadas en mi opinión.
{{< /callout >}}

### Autenticación y shell

TrueNAS tiene una terminal integrada, pero prefiero conectarme por SSH.

- Authorized Keys: pego mi llave pública SSH
- Shell (obligatorio): selecciona `zsh` si quieres. Me gustaría usar `fish`, pero aún no han brindado compatibilidad para ello.
- Marca “Allow all sudo commands” y “Allow all sudo commands with no password”
Si te sientes **_aventurero_**.

{{< callout type="warning" >}}
Otorgar sudo sin contraseña no es una buena práctica en general. Yo lo hago por conveniencia al trabajar en la terminal, pero la GUI es el método de configuración recomendado en TrueNAS.
{{< /callout >}}

- SMB User: déjalo marcado si quieres acceder a tus archivos vía SMB desde otros dispositivos. Que probablemente sí — para eso instalaste un NAS.

Verás una confirmación:

> “With this configuration, the existing directory `/mnt/tank/<username>` will be used as a home directory without creating a new directory for the user.”

Confirma y continúa.

Por último, cierra sesión de `truenas_admin` e inicia sesión con tu nuevo usuario.

{{< callout type="warning" >}}
Si agregaste tu llave pública SSH, tienes que habilitar el servicio SSH en Services.
{{< /callout >}}

## Preferencias personales de la interfaz

- Tema: cambia a Dracula (Menú de usuario → Preferences → Theme)
- Tiempo de sesión: súbelo de 5 minutos a 1 hora (3600 segundos) (Menú de usuario → Preferences)
- Activo “Redirect to HTTPS” y “Show Console Messages” como mejora de calidad de vida

## Preparar datasets para Apps

Creo un dataset `apps` y lo preconfiguro para aplicaciones:

1. Datasets → `tank` → Add Dataset
2. Nombre: `apps`
3. Dataset Preset (obligatorio): en este caso selecciono “Apps” → Save

Sigo esta costumbre: nombro los datasets en minúsculas para que sea más cómodo escribir rutas en la CLI y en archivos `compose.yaml`.

Crea sub‑datasets dentro de `apps` para cada aplicación que instales más adelante (por ejemplo, `apps/portainer`).

## Configurar scrubs programados

Configuro el scrub del pool y ajusto el límite:

- Ve a Storage → Storage Health → Schedule Scrubs → Configure
- Cambia Threshold Days de 35 a 30

> Es más práctico buscar esta opción con la barra de búsqueda de la parte superior de la página.


## Habilitar el servicio de Apps

Ve a Apps. Si ves “Apps Services Not Configured” (arriba a la derecha, junto al icono de engranaje):

1. Clic en Configuration → Choose Pool
2. Selecciona `tank` o el nombre de tu pool
3. El estado debería cambiar a “Apps Services Running”

## Configuración de red

Como tengo dos servidores TrueNAS, configuro un hostname para distinguirlos.

- Network → Global Configuration → Hostname (p. ej., `truenas01`, `truenas02`)

## Notificaciones por correo (SMTP)

Marcador de posición: documentaré la configuración de SMTP por separado y enlazaré aquí cuando esté listo.

{{< callout type="info" >}}
Guía de configuración de Email (SMTP): próximamente.
{{< /callout >}}

## Primera app: Portainer

Instalo Portainer primero. Publicaré la guía paso a paso de instalación de apps en TrueNAS por separado y la enlazaré aquí.

{{< callout type="info" >}}
Guía de Portainer en TrueNAS: próximamente.
{{< /callout >}}


