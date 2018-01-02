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

## Variable default
```bash
VAR=${1:-'Default'}
```

## Output parts
```bash
var=Fiesen
echo ${var:2:4}
esen
```

## Replace parts
```bash
var=Fiesen
echo ${var/i/li}
Fliesen

echo ${var/i}
Fesen
```

# Here document
```bash
cat > /tmp/file.txt << EOF
text
$var
EOF
```

# Here string
```bash
var=foo
cut -c1 <<< $var
```

```bash
if $(grep -q ^xn[0e][0-9] <<< $user); then
...
```

# Process Substitution
```bash
diff <(ls /bin) <(ls /usr/bin)
```

# Read data
## STDIN
### ksh
```ksh
read var?“Input: “
```

### bash
```bash
read -p “Eingabe: “ var
```

Hidden input
```bash
echo -n "Please enter password: "
read -s pwd
```

## Read data from file
### First line
```bash
read x < data.txt
echo $x
```

```bash
while read x; do
   echo $x
done < data.txt
```

# File timestamps
|**Type**|**`stat` name**|**`ls` option**|**Description**|
| --- | --- | --- | --- |
|atime|Access|`-ult`|The time when the data of a file was last accessed. Displaying the contents of a file or executing a shell script will update a file's atime.|
|mtime|Modify|`lt`|The time when the actual contents of a file was last modified.|
|ctime|Change|`-clt`|The time when changes were made to the file or directory's inode (owner, permissions, etc.). The ctime is also updated when the contents of a file change.
In UNIX, it is not possible to tell the actual creation time of a file.|
