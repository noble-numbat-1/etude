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

Type accelerator is an alias for a type.

```powershell
# These two command is equivalent
$ [System.DirectoryServices.DirectoryEntry]"WinNT://$env:COMPUTERNAME"
$ [ADSI]"WinNT://$env:COMPUTERNAME"

# Get type accelerator full type name
$ [ADSI].FullName

# Check more on type accelerator on
$ Get-Help about_Type_Accelerators
```

## Members

### Constructors

There are two ways to use a constructor.

```powershell
# Using the New-Object command
$ New-Object System.Text.StringBuilder

# Using the static method new()
$ [System.Text.StringBuilder]::new()

# Use the overload constructor
$ [System.Text.StringBuilder]::new('This is the start of the string')
```

### Properties

```powershell
$ Add-Type -TypeDefinition '
  public class MyClass
  {
    public string thisIsAField;                    // Field
    public string thisIsAProperty { get; set; }    // Property
  }
  '
$ [MyClass]::new() | Get-Member

# To show thisIsAField is a field
$ [MyClass].GetMembers() | Select-Object Name, MemberType
```

### Methods

```powershell
# Using method
$ $stringBuilder = [System.Text.StringBuilder]::new()
$ $stringBuilder.Append($true)
$ $stringBuilder.Append([Byte]1)

# Fluent interface
$ [PowerShell]::Create().
  AddCommand('Get-Process').
  AddCommand('Where-Object').
  AddParameter('Property', 'Name').
  AddParameter('Value', 'pwsh').
  AddParameter('EQ', $true).
  Invoke()

# Check static method
$ [DateTime] | Get-Member -MemberType Method -Static

# Using static method
$ $array = 1, 2, 3
$ [Array]::Reverse($array)

# List static properties
$ [DateTime] | Get-Member -MemberType Property -Static
```

## Reflection

### The TypeAccelerators type

The TypeAccelators (is not public) type is used to list and add type accelerators to the PowerShell session. The type may be found by using the `GetType()` on an `Assembly` object.

```powershell
# Assembly object may be used to run the GetType()
$ $typeAccelerators = [PowerShell].Assembly.GetType('System.Management.Automation.TypeAccelerators')

# List static method
$ $typeAccelerators | Get-Member -Static

# Add new type accelerator
$ $typeAccelerators::Add('TypeAccelerators', $typeAccelerators)

# Then it can be used
$ [TypeAccelerators]::Get
```

### The ArgumentTypeConverterAttribute type

The `ArgumentTypeConverterAttribute` attribute is used by PowerShell when casting a variable value which is got added when the type is on the left-hand side of an assignment.

```powershell
# Any values assigned will be cast to a string
$ [string]$variable = 'value'

# The present of the attribute can be seen using Get-Variable
$ (Get-Variable variable).Attributes | Format-List
```
