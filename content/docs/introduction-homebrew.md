---
title: "Introduction to Homebrew and Installation"
---

## What is Homebrew?

Hombrew has become an indispensable tool for my workflow. It's the first software I always install on a my Mac.Homebrew is a free and open-source package management system that simplifies the installation of software on macOS and Linux. Often referred to as "The Missing Package Manager for macOS" because it doesn't ship with a package manager by default.
Homebrew allows developers and users to easily install command-line tools, applications, and libraries that aren't included in the operating system.

### Key Benefits

- **Simple Installation**: Install software with a single command
- **Dependency Management**: Automatically handles software dependencies
- **Up-to-date Packages**: Maintains current versions of software
- **Uninstall Easily**: Remove packages cleanly when no longer needed
- **Large Repository**: Access to thousands of packages through formulae and casks

## Installation

Run the official installation script:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

{{< callout type="warning" >}}
Always verify installation scripts before running them. You can view the script at [install.sh](https://github.com/Homebrew/install/blob/main/install.sh) before executing.
{{< /callout >}}

You will be prompted to enter your password. This is because Homebrew needs to modify your system files. When entering the password, you will not see any characters being typed. This is normal.

### Post-Installation Setup

After installation, you need to add Homebrew to your PATH. Otherwise, you will get command not found error when you try to use Homebrew. The installer will provide specific instructions, but typically you'll need to run:

For **Apple Silicon Macs** (M1/M2):
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

For **Intel Macs**:
```bash
echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/usr/local/bin/brew shellenv)"
```

## Verify Installation

Run the built-in diagnostics:

```bash
brew doctor
```

You should see an output like this:
```bash
Your system is ready to brew.
```
## Understanding Formulae and Casks

Homebrew uses two main package types to organize different kinds of software:

### Formulae
**Formulae** are packages for command-line tools, libraries, and other software that runs in the terminal. These are typically developer tools, utilities, and services that don't have a graphical user interface.

Examples of formulae:
- `git` - Version control system
- `neovim` - An awesome text editor

### Casks
**Casks** are packages for GUI applications - the kind of software you would normally download as a `.dmg` file and drag to your Applications folder. Casks make it easy to install and manage desktop applications from the command line. This is one of the most powerful features of Homebrew in my opinion.

Examples of casks:
- `zen` - A minimalistic web browser
- `spotify` - Music streaming app



## Basic Usage

### Install a Package

```bash
brew install [package-name]
```

### Install a GUI Application

```bash
brew install --cask [application-name]
```

### Finding Packages

You can search for available formulae and casks:
```bash
brew search [search-term]
```

Or browse them online by going to [brew.sh](https://brew.sh) and then pressing `CMD + K` to open the search bar. This is my preferred way to find packages if you don't have the [Brew extension](https://www.raycast.com/nhojb/brew) installed in Raycast.

### List Installed Packages

```bash
brew list
# Or
brew ls
```

### Upgrade Packages

```bash
brew upgrade
```

### Uninstall a Package

```bash
brew uninstall [package-name]
# Or
brew rm [package-name]
```
