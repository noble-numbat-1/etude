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
