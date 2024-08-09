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
