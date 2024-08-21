# Variables, Arrays and Hashtables

`Trace-Command` can be used to inspect how PowerShell is converting a value.

```powershell
# eg: Convert string to date
# implicitly calls the Parse method of DateTime
$ Trace-Command -Expression { [DateTime]'1/1/1970' } -Name TypeConversion -PSHost

# The conversion `[DateTime]'1/1/1970'` is equivalent to
$ [DateTime]::Parse('1/1/1970')
```

## Variable Commands

It is used to explore and interact with variables in PowerShell, but are rarely used to declare variables. PowerShell optimizes code when it can, but the uses of `*-Variable` commands in script block disables optimization.

`Get-Variable` is used to list variables that have been created in current session and default (automatic) variables created by PowerShell.

```powershell
# View the created variable
# eg: Get-Variable today
$ Get-Variable <name>
$ Get-Variable <name> | Format-List

# eg: explore attributes of variable
$ [string]$variable = 'Hello world'
$ Get-Variable variable | ForEach-Object Attributes
```

`New-Variable` is used to create a variable.

Example:

```powershell
# Create a variable `today` with current date
# same as: $today = Get-Date
$ New-Variable -Name today -Value (Get-Date)

# Create a constant variable
$ New-Variable -Name startTime -Value (Get-Date) -Option Constant
```

`Set-Variable` is used to change certain properties of an existing variable.

Example:

```powershell
# Set a value of an existing variable
# same as: $today = Get-Date
$ Set-Variable today -Value (Get-Date)

# Set a Description for a variable
$ Set-Variable today -Description 'The description'

# Change to private scope
# The Private scope is accessible using: $private:today
$ Set-Variable today -Option Private
```

`Remove-Variable` is used to remove variable.

Example:

```powershell
$ psProcess = Get-Process pwsh
# Remove variable
$ Remove-Variable psProcess
```

`Clear-Variable` is used to remove value from existing variable which does not remove the variable itself.

Example:

```powershell
# Clear variable
$ Clear-Variable <variable>

# It is less common to use Clear-Variable, it is normally just assign `null` to a variable
$ $var = $null
```

## Variable Scope

There are three named scopes:

- Global: is the topmost scope which the prompt in console uses and is available to all child scope.
- Script: is specific to a single script and also available in modules.
- Local: is the current scope which is relative.
  - In console, the Local scope is also the Global scope.
  - In script, the Local scope is the Script scope.
  - Function and script blocks have a local scope of their own.

Example:

```powershell
# If variable $parent were assign in function
# a new variable is created and has no effect
# on the value of variable outside the function.
$ function Write-VariableValue {
  $local = 'Local scope value'
  # $parent = 'parent in local scope'
  Write-Host "Local: $local"
  Write-Host "Parent: $parent"
}
$ $parent = 'Parent scope value'
$ Write-VariableValue
```

### Scope Modifers

A scope modifier is placed before variable name and is followed by a colon.

```powershell
# Assign to Global scope
$ $Global:variableName = 123
```

### Numeric Scopes

Variable scope was stacked on top of the other in the order of call stack. `Get-Variable` can be used to access the value of a specific parent scope by passing the number of scope away from the current scope.

Example:

```powershell
$ function first {
  $name = 'first'
  second
}
$ function second {
  $name = 'second'
  third
}
$ function third {
  $name = 'third'
  "name value in first {0}" -f @(Get-Variable -Name name -Scope 2 -ValueOnly)
  "name value in second {0}" -f @(Get-Variable -Name name -Scope 1 -ValueOnly)
}
$ first
```

### Private Variables

A private variable is hidden from child scopes which can be either created using `New-Variable` or `Private` scope modifier.

```powershell
# Create private variable
$ New-Variable -Name <name> -Option Private
$ $private:<name> = <value>
```

> [!NOTE]
> It is still possible to get the value of private variable using numeric scope with `Get-Variable` command.

## Arrays

Array Creation

```powershell
# Creation by assignment using array sub-expression operator
$ $array = @()
$ foreach ($value in 1..5) {
  $array += [PSCustomObject]@{ Value = $value }
}

# Or
$ $array = foreach ($value in 1..5) { [PSCustomObject]@{ Value = $value } }

# Create using `new` method with a specific size
$ $objArray = [Object[]]::new(10)
$ $byteArray = [byte[]]::new(10)

# Directly assign a value or using array sub-exprression
$ $valArray = "Hello", "world", "Hello me"
$ $valArray = @("Hello", "world", "Hello me")
$ $valArray = "Hello world", 2, 3.1314, (Get-Date)
```

Arrays with type

```powershell
# Create array with a specific type
$ [ulong[]]
$ [xml[]]

# Set type for array variable
# $null will convert to 0 and 3.14 will convert to 3
$ [int[]]$nb = 1, 2, $null, 3.14
```

> [!NOTE]
> When type is set for array variable, PowerShell attempts to convert value to that type, or raise an error if the value cannot be converted.

Adding elements to an array

```powershell
# Add a single item
$ $array += 'new value'
$ $array = $array + 'new value'

# Join two array
$ $array1 = 1, 2, 3
$ $array2 = 4, 5, 6
$ $array3 = $array1 + $array2

# Using array sub-expression
$ $array1 = 1, 2, 3
$ $array2 = @((Get-Date), 'Hello world', $array1)
```

## List and ArrayList

```powershell
# Create a list
$ $list = [System.Collections.Generic.List[object]]::new()

# Add item to list
$ $list.Add('Hello')

# Add range
$ $array1 = 1, 2, 3
$ $list.AddRange($array1)

# Alternative to generic List, ArrayList
$ $arrayList = [System.Collections.ArrayList]::new()

# Create List from Assignment
$ [System.Collections.Generic.List[object]]$processes = Get-Process
$ [System.Collections.Generic.List[object]]$list = foreach ($value in 1..5) { [PSCustomObject]@{ Value = $value } }
```

> [!TIP]
> `Get-Member` can be used to view available methods.
>
> eg: `Get-Member -InputObject $list`
