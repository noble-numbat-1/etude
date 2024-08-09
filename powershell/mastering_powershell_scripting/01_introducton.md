# Introduction To Powershell

## Get-Help Command

```powershell
# Display default help info
$ Get-Help
$ Get-Help default

# Open a Window
$ Get-Help -ShowWindow

# List available help content
$ Get-Help *
$ Get-Help -Category All

# Open in browser
$ Get-Help <topic> -Online
```

## Command Naming & Discovery

```powershell
# List approved PowerShell verbs
$ Get-Verb

# Search command that starts with Get-
# and contains Firewall
$ Get-Command Get-*Firewall*

# Search command that has verb Get or Set
# and noun contains Firewall
$ Get-Command -Verb Get, Set -Noun *Firewall*
```

Alias

Alias created in one session are not persisted.

```powershell
# List all alias
$ Get-Alias

# Find command behind an alias
# eg: Get-Alias dir
$ Get-Alias <alias>

# Find alias for the command
# eg: Get-Alias -Definition Get-ChildItem
$ Get-Alias -Definition <cmdlet>

# Create alias
# eg: New-Alias grep -Value Select-String
$ New-Alias <alias> -Value <cmdlet>

# Remove alias: use Remove-Alias or Remove-Item
# eg: Remove-Alias grep or Remove-Item alias:\grep
$ Remove-Alias <alias>
$ Remove-Item alias:\<alias>
```

## Confirm & WhatIf

### Confirm

`ConfirmPreference` has four possible values:

- High
- Medium
- Low
- None

`ConfirmImpact` uses the same four values.

```powershell
# Finding ConfirmImpact
$ [System.Management.Automation.CommandMetadata](Get-Command <cmdlet>)

# Change/set ConfirmPreference
$ $ConfirmPreference = 'Low'
```

### WhatIf

`WhatIf` is used when testing a command.

`WhatIfPreference` default value is **false**.

> [!CAUTION]
> WhatIf support is defined by a command author which may not be handled correctly.

```powershell
# If preference variable is set to true,
# all commands that support WhatIf will act as if it set explicitly
$ $WhatIfPreference = $true
```

> [!NOTE]
> WhatIf takes precedence over Confirm.

## Introduction to Providers

More info check: `Get-Help about_Providers`

```powershell
# List current available providers
$ Get-PSProvider

# Access items within provider
# eg: Get-ChildItem Alias
# eg: Get-ChildItem FileSystem::C:\Windows
$ Get-ChildItem <provider>

# Access child items
# eg: Get-Item Variable::true
$ Get-Item <provider>::<childItem>

# List drives
$ Get-PSDrive
```

## Introductino to Splatting

Splatting is a way of defining the parameters of a command before calling it. The Individual parameters are written in `hashtable (@{})`, and the `@` symbol used to read the content of hashtable as parameters.

```powershell
$getProcess = @{
  Name = 'explorer'
}
Get-Process @getProcess

# Same as
$ Get-Process -Name explorer
```

Example:

```powershell
# Normal without splatting
$ $taskAction = New-ScheduledTaskAction -Execute pwsh.exe -Argument 'Write-Host "Hello world"'
$ $taskTrigger = New-ScheduledTaskTrigger -Daily -At '00:00:00'
$ Register-ScheduledTask -TaskName 'TaskName' -Action $taskAction -Trigger $taskTrigger -RunLevel 'Limited' -Description 'This line is too long to read'

# With splatting
$ $newTaskAction = @{
  Execute = ''
  Argument = 'Write-Host "Hello world"'
}
$ $newTaskTrigger = @{
  Daily = $true
  At = '00:00:00'
}
$ $registerTask = @{
  TaskName = 'TaskName'
  Action = New-ScheduledTaskAction @newTaskAction
  Trigger = New-ScheduledTaskTrigger @newTaskTrigger
  RunLevel = 'Limited'
  Description = 'Splatting is easy to read'
}
$ Register-ScheduledTask @registerTask
```
