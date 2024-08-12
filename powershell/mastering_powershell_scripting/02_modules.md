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

### Import Module

PowerShell 3 and later auto load modules if a command is used and the module path is in `$env:PSModulePath` env.

Autoloader can be disabled by setting `$PSModuleAutoLoadingPreference` to None.

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
