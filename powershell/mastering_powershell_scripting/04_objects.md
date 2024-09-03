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

`Where-Object` command is used to filter the output from commands.

```powershell
# eg: Filter processes stated after 9 am
$ Get-Process | Where-Object StartTime -GT (Get-Date 9:00:00)

# eg: Using FilterScript param
$ Get-Service | Where-Object { $_.StartType -eq 'Manual' -and $_.Status -eq 'Running' }
```

## Selecting and Sorting

`Select-Object` is used to select a subset of properties, to change properties, to add new properties, or to limit the number objects returned.

```powershell
# eg: Select all properies
$ Get-Process -Id $PID | Select-Object *

# eg: Limit property
$ Get-Process | Select-Object -Property Name, Id

# eg: Limit using wildcard
$ Get-Process | Select-Object -Property Name, *Memory*

# eg: Exclude properties
$ Get-Process | Select-Object -Property * -ExcludeProperty *Memory*

# eg: Limit by First or Last
$ Get-ChildItem C:\ -Recurse | Select-Object -First 2
$ Get-ChildItem C:\ | Select-Object -Last 3

# Other param: -Skip, -Index, -SkipIndex
```

Add new Properties or rename existing properties:

```powershell
# Format
@{ Name = 'PropertyName'; Expression = { 'PropertyValue' } }
@{ Label = 'PropertyName'; Expression = { 'PropertyValue' } }
@{ n = 'PropertyName'; e = { 'PropertyValue' } }
@{ l = 'PropertyName'; e = { 'PropertyValue' } }
```

Example:

```powershell
# Rename Id to ProcessId, both are the same
$ Get-Process | Select-Object @{ Name = 'ProcessId'; Expression = 'Id' }
$ Get-Process | Select-Object @{ Name = 'ProcessId'; Expression = { $_.Id } }

# Enclose in a list: @()
$ Get-Process | Select-Object -Property @(
  'Name'
  @{Name = 'ProcessId'; Expression = 'ID' }
  @{Name = 'FileOwner'; Expression = { (Get-Acl $_.Path).Owner }}
)

# Use `ExpandPropery` param to map the property out
# eg: extract the string value from Name props
$ Get-Process | Select-Object -First 5 -ExpandProperty Name
# eg: extract all VersionInfo's props and merge with FullName and Length
$ Get-ChildItem $env:SystemRoot\*.dll | Select-Object FullName, Length -ExpandProperty VersionInfo | Format-List

# Filter unique value
$ 1, 1, 1, 3, 5, 2, 2, 4 | Select-Object -Unique
$ 1, 1, 1, 3, 5, 2, 2, 4 | Sort-Object | Get-Unique

# Filter unique with properties
$ $val = 1, 2, 3, 1, 2, 3 | ForEach-Object { [PSCustomObject]@{ Number = $_ } }
$ $val | Select-Object -Property * -Unique
```

`Sort-Object` is used to perform both simple and complex sorting and by default in ascending order.

```powershell
# Sort number
$ 5, 4, 3, 2, 1 | Sort-Object

# String are sort irrespective of uppercase or lowercase
$ 'ccc', 'BBB', 'aaa' | Sort-Object

# Sort on property
$ Get-Process | Sort-Object -Property Id

# Sort on multiple property: LastWriteTime and then Name
$ Get-ChildItem C:\Windows\System32 | Sort-Object LastWriteTime, Name

# Sort with script block
$ $examResults = @(
  [PSCustomObject]@{ Exam = 'Music'; Result = 'N/A'  Mark = 0 }
  [PSCustomObject]@{ Exam = 'History'; Result = 'Fail'; Mark = 23 }
  [PSCustomObject]@{ Exam = 'Biology'; Result = 'Pass'; Mark = 78 }
  [PSCustomObject]@{ Exam = 'Physics'; Result = 'Pass'; Mark = 86 }
  [PSCustomObject]@{ Exam = 'Maths'; Result = 'Pass'; Mark = 92 }
)
$ $examResults | Sort-Object { switch ($_.Result) { 'Pass' { 1 }; 'Fail' { 2 }; 'N/A' { 3 } } }

# Multiple sort: mix script block
$ $examResults | Sort-Object { switch ($_.Result) { 'Pass' { 1 }; 'Fail' { 2 }; 'N/A' { 3 } } }, Mark

# Sort Descending
$ 3, 5, 4, 2, 1 | Sort-Object -Descending

# Multiple script block using @()
$  $examResults | Sort-Object @(
  { switch ($_.Result) { 'Pass' { 1 }; 'Fail' { 2 }; 'N/A' { 3 } } }
  @{ Expression = { $_.Mark }; Descending = $true }
)
```

`Sort-Object` can also be used to sort with unique the result.

```powershell
# Unique sort in Sort-Object is not case-sensitive
$ 'dog', 'dog', 'cat', 'cat', 'mouse', 'Mouse' | Sort-Object -Unique
```

> [!CAUTION]
> The Unique parameter of `Sort-Object` is not case-sensitive.

## Grouping and Measuring

`Group-Object` command is used to group object together under a single property name or expression. By default, it returns collection of objects.

```powershell
# Create GroupInfo: Name, Count, Group
$ 6, 7, 7, 8, 8, 8 | Group-Object

# `NoElement` param remove Group property
$ 6, 7, 7, 8, 8, 8 | Group-Object -NoElement

# Group by property: Name
$ Get-ChildItem C:\Windows\assembly\ -Filter *.dll -File -Recurse | Group-Object Name -NoElement

# Group by multiple property: Name and Length
$ Get-ChildItem C:\Windows\assembly\ -Filter *.dll -File -Recurse | Group-Object Name, Length -NoElement

# Custom group: group by domain
$ 'one@one.example', 'two@one.example', 'three@two.example' | Group-Object { ($_ -split '@')[1] }

# Return hashtable instead of collection
# Using AsString the ensure the key is String
# If AsString is omit, the key to access must match the type
$ $val = @([IPAddress]'10.0.0.1', [IPAddress]'10.0.0.2', [IPAddress]'10.0.0.1')
$ $hashtable = $val | Group-Object -AsHashtable -AsString
```

> [!CAUTION]
> By default, `Group-Object` is not case-sensitive. Using `CaseSensitive` paramter to make is case-sensitive.

`Measure-Object` command is used to apply mathematical operation(count, average, sum, etc) on objects. It also allows characters, words or lines to be counted in text fields.

```powershell
# Count number of items
$ 1, 5, 9, 79 | Measure-Object

# Other measure property
$ 1, 5, 9, 79 | Measure-Object -Average -Maximum -Minimum -Sum -StandardDeviation

# Measure a specific property
$ Get-Process | Measure-Object WorkingSet -Average

# Measuring String
$ Get-Content c:\windows\WindowsUpdate.log | Measure-Object -Line -Word -Character
```

## Comparing

`Compare-Object` command is used to compare two collections of objects. If either of the compare value is null, an error will be displayed. If both values are equal, it does not return anything by default.

```powershell
# The compare values are equal, nothing return
$ Compare-Object -ReferenceObject 1, 2 -DifferenceObject 1, 2

# The compare values are not equal
$ Compare-Object -ReferenceObject 1, 2, 3, 4 -DifferenceObject 1, 2

# Other params
$ $params = @{ ReferenceObject = 1, 2, 3,4; DifferenceObject = 1, 2, 5; IncludeEqual = $true; ExcludeDifferent = $true; PassThru = $true }
$ Compare-Object @params

# Compare by object properties
# eg: compare file that has the same name and same size
$ $params = @{
    ReferenceObject = Get-ChildItem C:\Windows\System32 -File
    DifferenceObject = Get-ChildItem C:\Windows\SysWOW64 -File
    Property = 'Name', 'Length'
    IncludeEqual = $true
    ExcludeDifferent = $true
  }
```

## Importing, Exporting, and Converting

`Export-Csv` command is used to write data from objects to textfile. By default, it's using UTF8 encoding and overwrites any file when using the same name.

```powershell
# Write csv to file, overwrite any existing file
$ Get-Process | Export-Csv processes.csv

# Write csv, append to existing file
# Csv header must be the same, otherwise an error will occur
$ Get-Process -Id $PID | Select-Object Name, Id | Export-Csv process.csv -Append

# When append, any extra header will be ignore, if any
# eg: here in the second command, Id will be ignore when append
$ Get-Process pwsh | Select-Object Name | Export-Csv process.csv
$ Get-Process explorer | Select-Object Name, Id | Export-Csv process.csv -Append

# Other parma:
# - IncludeTypeInformation: add .NET type info
# - NoTypeInformation: remove .NET type info
```

`ConvertTo-Csv` command is used to write data from objects to command output as csv.

```powershell
# Write csv to command output
$ Get-Process pwsh | Select-Object Name, Id | ConvertTo-Csv

# Convert value before write to csv
$ [PSCustomObject]@{ Name = "Numbers"; Value = 1, 2, 3, 4, 5 } | ForEach-Object { $_.Value = $_.Value -join ', '; $_ } | ConvertTo-Csv
```

`Import-Csv`

Export-CliXml and Import-CliXml
Tee-Object
