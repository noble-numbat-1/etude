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
function first {
  $name = 'first'
  second
}
function second {
  $name = 'second'
  third
}
function third {
  $name = 'third'
  "name value in first {0}" -f @(Get-Variable -Name name -Scope 2 -ValueOnly)
  "name value in second {0}" -f @(Get-Variable -Name name -Scope 1 -ValueOnly)
}
first
```
