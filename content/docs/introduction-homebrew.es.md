---
title: "Introducción a Homebrew e Instalación"
---

## ¿Qué es Homebrew?

Homebrew se ha convertido en una herramienta indispensable para mi flujo de trabajo. Es el primer software que siempre instalo en mi Mac. Homebrew es un sistema de gestión de paquetes gratuito y de código abierto que simplifica la instalación de software en macOS y Linux. A menudo se le conoce como "El Gestor de Paquetes que Falta en macOS" porque no incluye un gestor de paquetes por defecto.
Homebrew permite a desarrolladores y usuarios instalar fácilmente herramientas de línea de comandos, aplicaciones y bibliotecas que no están incluidas en el sistema operativo.

### Beneficios Clave

- **Instalación Sencilla**: Instala software con un solo comando
- **Gestión de Dependencias**: Maneja automáticamente las dependencias del software
- **Paquetes Actualizados**: Mantiene versiones actuales del software
- **Desinstalación Fácil**: Remueve paquetes limpiamente cuando ya no los necesites
- **Repositorio Amplio**: Acceso a miles de paquetes a través de formulae y casks

## Instalación

Ejecuta el script oficial de instalación:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

> [!WARNING]
> Siempre verifica los scripts de instalación antes de ejecutarlos. Puedes ver el script en [install.sh](https://github.com/Homebrew/install/blob/main/install.sh) antes de ejecutarlo.

Se te pedirá que ingreses tu contraseña. Esto es porque Homebrew necesita modificar archivos del sistema. Al ingresar la contraseña, no verás ningún carácter siendo escrito. Esto es normal.

### Configuración Post-Instalación

Después de la instalación, necesitas agregar Homebrew a tu PATH. De lo contrario, obtendrás un error de comando no encontrado cuando trates de usar Homebrew. El instalador proporcionará instrucciones específicas, pero típicamente necesitarás ejecutar:

Para **Macs Apple Silicon** (M1/M2):
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

Para **Macs Intel**:
```bash
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/usr/local/bin/brew shellenv)"
```

## Verificar la Instalación

Ejecuta el diagnóstico integrado:

```bash
brew doctor
```

Deberías ver un resultado como este:
```bash
Your system is ready to brew.
```

## Entendiendo Formulae y Casks

Homebrew usa dos tipos de paquetes principales para organizar diferentes tipos de software:

### Formulae
**Formulae** son paquetes para herramientas de línea de comandos, bibliotecas y otro software que se ejecuta en la terminal. Estos son típicamente herramientas de desarrollo, utilidades y servicios que no tienen una interfaz gráfica de usuario.

Ejemplos de formulae:
- `git` - Sistema de control de versiones
- `neovim` - Un editor de texto increíble

### Casks
**Casks** son paquetes para aplicaciones GUI - el tipo de software que normalmente descargarías como un archivo `.dmg` y arrastrarías a tu carpeta de Aplicaciones. Los casks facilitan la instalación y gestión de aplicaciones de escritorio desde la línea de comandos. Esta es una de las características más poderosas de Homebrew en mi opinión.

Ejemplos de casks:
- `zen` - Un navegador web minimalista
- `spotify` - Aplicación de streaming de música

## Uso Básico

### Instalar un Paquete

```bash
brew install [nombre-del-paquete]
```

### Instalar una Aplicación GUI

```bash
brew install --cask [nombre-de-la-aplicación]
```

### Encontrar Paquetes

Puedes buscar formulae y casks disponibles:
```bash
brew search [término-de-búsqueda]
```

O navegar en línea yendo a [brew.sh](https://brew.sh) y luego presionando `CMD + K` para abrir la barra de búsqueda. Esta es mi forma preferida de encontrar paquetes si no tienes la [extensión de Brew](https://www.raycast.com/nhojb/brew) instalada en Raycast.

### Listar Paquetes Instalados

```bash
brew list
# O
brew ls
```

### Actualizar Paquetes

```bash
brew upgrade
```

### Desinstalar un Paquete

```bash
brew uninstall [nombre-del-paquete]
# O
brew rm [nombre-del-paquete]
``` 
