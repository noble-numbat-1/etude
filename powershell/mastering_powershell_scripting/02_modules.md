# Modules

## Introduction

Modules are discovered using the paths in `PSModulePath` environment variable.

```powershell
# List available modules on current system
# (modules that have been imported)
$ Get-Module

# List available modules
$ Get-Module -ListAvailable

# List all regardless of path and version
$ Get-Module [<moduleName>] -All -ListAvailable
```

> [!TIP]
> Modules that are available in system can be imported either by `Import-Module` or by running a command from a module.

### Import and Remove Module

PowerShell 3 and later auto load modules if a command is used and the module path is in `$env:PSModulePath` env. Autoloader can be disabled by setting `$PSModuleAutoLoadingPreference` to None.

```powershell
# Import module by name
# eg: Import-Module -Name ThreadJob
$ Import-Module -Name <module>

# Import module with full path
# eg: Import-Module -Name $PSHome\Modules\ThreadJob\ThreadJob.psd1
$ Import-Module -Name <path>

# Import with MinimumVersion or MaximumVersion
# eg: Import-Module PSScriptAnalyzer -MaximumVersion 1.7.0
$ Import-Module <module> -MaximumVersion <verson>
```

The `Remove-Module` command unloads the imported module from the current session. It does not remove or delete the files that make up the module.

## Finding and Installing Modules

`PowerShellGet` module can be used to register repositories and search for and install modules. By default PowerShellGet searchs the PowerShell Gallery.

> [!IMPORTANT]
> Check [PSResourceGet](https://learn.microsoft.com/powershell/module/microsoft.powershell.psresourceget/?view=powershellget-3.x) VS [PowerShellGet](https://learn.microsoft.com/powershell/module/powershellget/?view=powershellget-3.x)

`Find-Module` is used to search the PowerShell Gallery, or any registered repository

```powershell
# Search by name
$ Find-Module <name>

# Eg
$ Find-Module Carbon
$ Find-Module -Name Carbon
$ Find-Module -Name Azure*

# Use Filter to include tag
# eg: Find-Module -Filter IIS
$ Find-Module -Filter <str>
```

`Install-Module` is used to install modules from the PowerShell Gallery or registered repository. By default, it install modules on the path for CurrentUser.

```powershell
# Install a module
# eg: Install-Module posh-git
$ Install-Module <module>

# Force parameter can be used to re-install a module
$ Install-Module <module> -Force

# Install module on all user scope
# eg: Install-Module carbon -Scope AllUsers
$ Install-Module <module> -Scope AllUsers
```

> [!NOTE]
> If a recent version of a module is already installed, no installation. If a newer version is available, it will be installed alongside.

`Update-Module` is used to update any module installed using the `Install-Module` command. It attempts to update the specified module to the latest or a specified version.

`Save-Module` is used to download the module from the PowerShell Gallery (or registered repository) to a path without installing it.

```powershell
# Download a module
# eg: Save-Module -Name Carbon -Path C:\Modules -Force
$ Save-Module -Name <module> -Path <path>
```

## PSResourceGet

`Register-PSResourceRepository` is used to register repository.

```powershell
# Register the default PSGallery
# PSGallery repository is registered by default, but can be removed
$ Register-PSResourceRepository -PSGallery
```

## PowerShell Repositories

Creating an **SMB** repository is a simple way to share PowerShell content.

```powershell
# eg: register a file share
$ $params = @{
  Name = 'Internal'
  SourceLocation = '\\server\share\directory'
  InstallationPolicy = 'Trusted'
}
$ Register-PSRepository @params
```

`Publish-Module` is used to publish existing installed module to the repository.

```powershell
# eg: publish to the Internal repository
$ $params = @{
  Name = 'pester'
  RequiredVersion = '5.4.0'
  Repository = 'Internal'
}
$ Publish-Module @params
```
