# Conditional Statements and Loops

## if, else, and elseif

Syntax:

```powershell
# if
$ if (<condition>) {
  <statements>
}

# else
$ if (<condition>) {
  <statements>
} else {
  <statements>
}

# elseif
$ if (<condition>) {
  <statements>
} elseif(<condition>) {
  <statements>
}
```

Example:

```powershell
# eg: 1
$ $value = 1
$ if ($value -eq 1) {
  Write-Host 'Value is 1'
} elseif ($value -lt 10) {
  Write-Host 'Value is less than 10'
} else {
  Write-Host 'Value is not equal to 1'
}

# Assignment within if statemetn
$ if ($i = 1) { Write-Host "The variable i is $i" }
$ if ($interface = Get-NetAdapter | Where-Object Status -eq 'Up') {
  Write-Host "$($interface.Name) is up"
}
```

## switch Statements

Syntax:

```powershell
switch [-regex|-wildcard|-exact][-casesensitive] (<value>) {
  <case>   { <statements> }
  <case>   { <statements> }
  default  { <statements> }
}
```

Example:

```powershell
# Switch on single value
$ $value = 2
$ switch ($value) {
  1 { Write-Host 'value is 1' }
  default { Write-Host 'No conditions matched' }
}

# Switch on array value
# The switch will on for each of the $arrayOfValues value
$ $arrayOfValues = 1..3
$ switch ($arrayOfValues) {
  1 { 'One' }
  2 { 'Two' }
  3 { 'Three' }
}
```

> [!NOTE]
> A switch on an empty array will not excute any case, even default case.

Switch on File:

```powershell
# The file content is read as an array, line by line
$ switch [-regex|-wildcard] [-casesensitive] -File <Name> {
  <condition> { <statements> }
  <condition> { <statements> }
}
```
