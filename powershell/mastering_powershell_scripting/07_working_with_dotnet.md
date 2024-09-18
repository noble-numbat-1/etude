# Working with .NET

- GAC: Global Assembly Cache
- Gac Module
- ClassExplorer Module

## Assemblies

```powershell
# Assembly location
$ [System.String].Assembly.Location

# List of currently loaded assemblies of current session
$ [System.AppDomain]::CurrentDomain.GetAssemblies()
```

## Types

Types are written between square brackets.

Example: `[System.AppDomain]`, `[System.Management.Automation.PowerShell]`

### Enumerations

Example:

```powershell
# List enums value of ActionPreference enum
$ $VerbosePreference.GetType().GetEnumValues()
```

### Namespaces

A namespace is used to organize type into hierarchy, grouping types together.

```powershell
# Get the namespace of File
$ [System.IO.File].Namespace
```

## The using keyword

The `using` keyword simplifies the use of namespaces and can be used to load assemblies or PowerShell modules. It can be used in a script(only preceded by comments), a module, or in console. The `using module` statement is used to access PowerShell classes created within PowerShell module.

### using namespaces

PowerShell is, by default, using `System` top-level namespace. `using namespace` statement to add more namespace.

```powershell
# Before add System.IO namespace, cause error
$ [File]

# Using System.IO namespace
$ using namespace System.IO
$ [File]
```

> [!NOTE]
> In console, PowerShell only recognizes the last `using namespace` statement.

```powershell
# Only the last is used
$ using namespace System.IO
$ using namespace System.Data.SqlClient

# No error
$ [SqlConnection]

# Error
$ [File]
```

> [!TIP]
> To use multiple namespace in console, use **semi colon** to separate `using namespace` statement in one command.
>
> eg: `using namespace System.IO; using namespace System.Data.SqlClient`

### using assemblies

The `using assembly` statement is used to load assemblies into PowerShell session.

```powershell
# Load System.Windows.Forms assembly
$ using assembly 'C:\Program Files\PowerShell\7\System.Windows.Forms.dll'
```

## Type accelerators
