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

**Wildcard and Regex** parameters is used when matching strings.

```powershell
# Using wildcard param
# ? => any character
# * => any string of zero or more characters
# [] => ranges defined in square brackets
$ switch -Wildcard ('cat') {
  'c*' { Write-Host 'c*' }
  '???' { Write-Host '???' }
  '*t' { Write-Host '*t' }
  '*[aeiou]' { Write-Host '*[aeiou]' }
}

# Using Regex param
$ switch -Regex ('cat') {
  '^c' { Write-Host '^c' }
  '^.{3}$' { Write-Host '^.{3}$' }
  't$' { Write-Host 't$' }
}
```

**Script block case** can be used in place of the direct comparisons. It is executed and the result determines whether the case is matched.

```powershell
# eg: script block case
$ switch (Get-Date) {
  { $_ -is [DateTime] } { Write-Host 'This is DateTime' }
  { $_.Year -ge 2020 } { Write-Host 'It is 2020 or later' }
}
```

> [!CAUTION]
> Switch statement turns each of the enum case into a literal string. Possible solutions are
>
> - Use the name of the enum value
> - Enclose enum in bracket

```powershell
# Enum in switch statement's case will be converted to literal string
# eg: The following enum case will not match
$ switch ((Get-Date).DayOfWeek) {
  [DayOfWeek]::Monday    { 'Monday' }
  [DayOfWeek]::Tuesday   { 'Tuesday' }
  [DayOfWeek]::Wednesday { 'Wednesday' }
  [DayOfWeek]::Thursday  { 'Thursday' }
  [DayOfWeek]::Friday    { 'Friday' }
  [DayOfWeek]::Saturday  { 'Saturday' }
  [DayOfWeek]::Sunday    { 'Sunday' }
  default { 'It is not a week day at all' }
}

# Use the name of the enum value
$ switch ((Get-Date).DayOfWeek) {
  'Monday' { 'Monday' }
  'Tuesday' { 'Tuesday' }
}

# Enclose in bracket
$ switch ((Get-Date).DayOfWeek) {
  ([DayOfWeek]::Monday) { 'Monday' }
  ([DayOfWeek]::Tuesday) { 'Tuesday' }
}

# Static property in case statement
$ switch ((Get-Date).Date) {
  ([DateTime]::Today) { 'It is still today' }
}
```

### break and continue

- When break is used in a case, the switch statement ends.
- When continue is used and the value is a scaler, the switch statement ends.
- When continue is used and the value is an array, it moves to the next element.

```powershell
# Only the frist executes
$ switch (1, 2) {
  1 { Write-Host 'Equals 1'; break }
  2 { Write-Host 'Equals 2' }
}
# Equals 1

# Continue allow switch continues to the next element in the array
$ switch (1, 2) {
  1 { Write-Host 'Equals 1'; continue }
  1 { Write-Host 'value is still 1' }
  2 { Write-Host 'Equals 2' }
}
# Equals 1
# Equals 2

# Mix brean and continue
$ switch (1, 2, 3) {
  1 { Write-Host 'One'; continue }
  1 { Write-Host 'One again' }
  2 { Write-Host 'Two'; break }
  3 { Write-Host 'Three' }
}
# One
# Two
```

## Loops

### foreach loop

```powershell
# Syntax notation
$ foreach (<element> in <collection>) {
  <statements>
}

# eg
$ foreach ($process in Get-Process) {
  Write-Host $process.Name
}
```

> [!CAUTION]
> PowerShell comes with the alias `foreach` for the `ForEach-Object` command. The `foreach` alias and keyword acts depend on context.

```powershell
# foreach keyword
$ $array = 1..3
$ foreach ($value in $array) {
  'Will repeat three times!'
}

# foreach alias
$ $array = 1..3
$ $array | foreach {
  'Will repeat three times!'
}
```

### for loop

```powershell
# Syntax notation
$ for (<initial>; <condition>; <repeat>){
  <body-statements>
}

#
$ $processes = Get-Process
$ for ($i = 0; $i -lt $processes.Count; $i++) {
  Write-Host $processes[$i].Name
}
```

### do-until and do-while loops

```powershell
# Syntax notation
$ do {
  <body-statements>
} <until | while> (<condition>)

# eg: do-until loop
$ do {
  Write-Host "Waiting for boot"
Start-Sleep -Seconds 5
} until (Test-Connection 'SomeComputer' -Quiet -Count 1)

# eg: do-while loop
$ do {
  Write-Host "Waiting for shutdown"
Start-Sleep -Seconds 5
} while (Test-Connection 'SomeComputer' -Quiet -Count 1)
```

### while loop

```powershell
# Syntax notation
$ while (<condition>) {
  <body-statements>
}

# eg: loop until file exist
while (-not (Test-Path $env:TEMP\test.txt -PathType Leaf)) {
  Start-Sleep -Seconds 10
}
```

### Loops, break, and continue
