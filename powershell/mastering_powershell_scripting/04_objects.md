# Working with Objects in PowerShell

## Pipelines

### Output Stream

Standard output (the output stream) of a command is used to assign to a variable. Non-standard output will not be assigned to the variable.

Each of the streams has a command assiciated with it:

| Stream          | Command           | Stream number |
|-----------------|-------------------|---------------|
| Standard output | Write-Output      | 1             |
| Error           | Write-Error       | 2             |
| Warning         | Write-Warning     | 3             |
| Verbose         | Write-Verbose     | 4             |
| Debug           | Write-Debug       | 5             |
| Information     | Write-Information | 6             |

> [!NOTE]
> In PS 5 and later, `Write-Host` is a wrapper for `Write-Information`. Prior to PS5, it did not have a dedicated stream.

## Members

`Get-Member` is used to view the different members of an object.

```powershell
# List member of process object
$ Get-Process -Id $PID | Get-Member

# Filter by MemberType
$ Get-Process -Id $PID | Get-Member -MemberType Property

# Some properties are not visible, use `-Force` to display those properties
$ Get-Process -Id $PID | Get-Member PS* -Force
```

Accessing Object Properties

Example:

```powershell
# Accessing `Name` property
$ $process = Get-Process -Id $PID
$ $process.Name

# Or
$ (Get-Process -Id $PID).Name
```

### Creating and Modifying Objects

`New-Object` command is used to create an instance of .NET objects and COM objects. It is Deprecated in PowerShell 3 with the introduction of `[PSCustomObject]`.

`Add-Member` is used to add arbitrary properties to an existing object.

Example:

```powershell
# Add property member
$ $dir = Get-ChildItem
$ $dir | Add-Member -NotePropertyName ComputerName -NotePropertyValue $env:COMPUTERNAME

# Or
$ $customObject | Add-Member -Name Username -Value 'username' -MemberType NoteProperty

# Or using positional parameter
# Add-Member also has `PassThru` parameter
$ $c = New-Object Object | Add-Member Username 'username' -PassThru | Add-Member Email 'test@test.com' -PassThru
```

## Enumerating and Filtering

`ForEach-Obejct` command is used to work on each object in an input pipeline.

```powershell
# Process param: run once for each item
# eg: Work on each of the results from Get-Process in turn
$ Get-Process | ForEach-Object { Write-Host $_.Name -ForegroundColor Green }

# Or
$ Get-Process | ForEach-Object -Process { Write-Host $_.Name -ForegroundColor Green }

# Begin param: run once at the begin before iteration
# End param: run once after the iteration
# eg: run Begin, then the Process of each item (if any), after run End
$ 1..5 | ForEach-Object -Begin { Write-Host 'Begin'; $value = 0 } -Process { Write-Host "itm: $_"; $value += $_ } -End { Write-Host 'Finish'; $value }
```

> [!TIP]
> `$PSItem` variable may be used in place of `$_`.

`ForEach-Object` gains a `Parallel` parameter in PowerShell 7, which is used to run process block in parallel. By default, it run 5 instances of process block at a time. This is controlled by the `ThrottleLimit` parameter.

```powershell
# eg: Using Parallel with Start-Sleep
$ 1..10 | ForEach-Object -Parallel { Start-Sleep -Seconds 2; $_ }

# `using` scope modifier is required to access variable outside parallel process
# The `using` scope is read-only
$ $str = 'test'
$ 1..10 | ForEach-Object -Parallel { $using:str }

# Advanced array types and hashtables can be changed
$ $val = @{}
$ 1..5 | ForEach-Object -Parallel { ($using:val).Add($_, $_) }
```

`MemberName` parameter is used to get a single property or execute a single method.

```powershell
# eg: Get the Path property of each item
$ Get-Process | ForEach-Object -MemberName Path
```
