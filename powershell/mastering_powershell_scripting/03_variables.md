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
