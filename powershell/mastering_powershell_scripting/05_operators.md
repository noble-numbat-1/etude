# Operators

## Sub-expression operator

- Sub-expression: $( )
- Array sub-expression: @( )

```powershell
# Sub-expression used in double-quoted string
$ $word = 'one'
$ "Length: $($word.Length)"
# => Length: 3

# Without sub-expression
$ "Length: $word.Length"
# => Length: one.Length

# Sub-expression expands values in arguments
$ $dir = [PSCustomObject]@{ Path = (Get-Item ~).FullName }
$ FindStr /D:$($dir.Path) SomeValue *.txt

# Array sub-expression: create an array
$ $processes = @(Get-Process -ID $PID)
$ Get-Member -InputObject $processes

# Complex array sub-expression
$ $myArray = @(
    Get-Process | Where-Object ProcessName -In @('pwsh', 'powershell')
    Get-Process notepad
  )
```

## Comparison operators

| Operator     | Name                  |
|--------------|-----------------------|
| -eq          | equal                 |
| -ne          | not equal             |
| -like        | like                  |
| -notlike     | not like              |
| -gt          | greater than          |
| -ge          | greater than or equal |
| -lt          | less than             |
| -le          | less than or equal    |
| -contains    | contains              |
| -notcontains | not contains          |
| -in          | in                    |
| -notin       | not in                |

> [!NOTE]
> When comparing string add c before operator for case-sensitive comparison.

## Regular expression-based operators

| Operator  | Name      |
|-----------|-----------|
| -match    | match     |
| -notmatch | not match |
| -replace  | replace   |
| -split    | Split     |
