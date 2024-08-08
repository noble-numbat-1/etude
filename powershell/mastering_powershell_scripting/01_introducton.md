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

```powershell
# List all alias
$ Get-Alias

# Find command behind an alias
$ Get-Alias dir

# Find alias for the command
$ Get-Alias -Definition Get-ChildItem

# Create alias
$ New-Alias grep -Value Select-String

# Remove alias: use Remove-Alias or Remove-Item
$ Remove-Alias grep
$ Remove-Item alias:\grep
```
