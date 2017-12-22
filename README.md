# Variables
## Pattern-matching Operators
|**Operator**|**Meaning**|
| --- | --- |
|`${variable#pattern}`|If the pattern matches the beginning of the variable's value, delete the shortest part that matches and return the rest.|
|`${variable##pattern}`|If the pattern matches the beginning of the variable's value, delete the longest part that matches and return the rest.|
|`${variable%pattern}`|If the pattern matches the end of the variable's value, delete the shortest part that matches and return the rest.|
|`${variable%%pattern}`|If the pattern matches the end of the variable's value, delete the longest part that matches and return the rest.|

## Example
```
path=/home/billr/mem/long.file.name
```

|**Expression**|**Result**|
| --- | ---:|
|`${path##/*/}`|`long.file.name`|
|`${path#/*/}`|`billr/mem/long.file.name`|
|`$path`|`/home/billr/mem/long.file.name`|
|`${path%.*}`|`/home/billr/mem/long.file`|
|`${path%%.*}`|(from start) `/home/billr/mem/long`|
|`${path%.*.*}`|(from end) `/home/billr/mem/long`|
|`${path%/*}`|`/home/billr/mem`|

## Variable defaults
```bash
VAR=${1:-'Default'}
```
