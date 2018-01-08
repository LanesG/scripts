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

# Here Documents
```bash
cat > /tmp/file.txt << EOF
text
$var
EOF
```

# Here Strings
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

# Parameters
Number of parameters: `$#`

```bash
#!/bin/sh

usage () {
   echo "Usage: $0 [-i|-m] <filename>"
   echo "   -i IOPS only"
   echo "   -m MB/s only"
   exit 1
}

if [[ $# == 0 ]]
then
   usage
fi

while getopts ":im" opt
do
   case $opt in
      i)        echo "IOPS";;
      m)        echo "MB/s";;
      \?)       usage;;
   esac
done
```

```bash
case `uname -s` in
    Linux)
        opt=`getopt -o cd --long create-indexes,drop-indexes -- "$@"`
        ;;

    AIX|SunOS)
        opt=`getopt cd $*`
        ;;
esac

# On getopt error terminate.
if [[ $? != 0 ]] ; then
    print_usage
    exit 1
fi

# Evaluate current option values.
eval set -- "$opt"

while true; do
    case "$1" in ...
```

# Suppress output
[Illustrated Redirection Tutorial](http://wiki.bash-hackers.org/howto/redirection_tutorial)
## STDOUT and STDERR
```
ping 127.0.0.1 > /dev/null 2>&1
```

## STDERR
```
ping 127.0.0.1 2> /dev/null
```

# Calculations
## Variables and numbers
```
var1=$((var2-4))
```

```
var1=`echo $var2-4 | bc`
```

## Only variables
```
var1=$((var2-var3))
```

```
integer var1=${var2}-${var3}
```

```
let $((var1 = $var2 + $var3))
```

## Increment variable
```
((var += 1))
```

```
let $((var += 1))
```

```
var=`expr $var + 1`
```

## Substraction in if
```bash
if [[ $(( (($VAR1)–4) -lt 4) )) ]]
then
    ...
fi
```

## hex2dec
```bash
echo $((16#FF))
255
```

## Modulus
```bash
$(($var % 5))
```

## Echo
```bash
echo 10-3
10-3
echo 10-3 | bc
7
```

# Random numbers
```
echo $((RANDOM%10+1))
```

# Arrays
[Bash arrays](http://www.artificialworlds.net/blog/2013/09/18/bash-arrays/)

Note that the `@` sign can be used instead of the `*` in constructs such as `${arr[*]}`, the result is the same except when expanding to the items of the array within a quoted string. In this case the behavior is the same as when expanding `$*` and `$@` within quoted strings:

`"${arr[*]}"` returns all the items as a **single** word, whereas  
`"${arr[@]}"` returns each item as a **separate** word.

The following example shows how unquoted, quoted `*`, and quoted `@` affect the expansion (particularly important when the array items themselves contain spaces):

```bash
array=("first item" "second item" "third" "item")

echo "Number of items in original array: ${#array[*]}"
for ix in ${!array[*]}
do
    printf "   %s\n" "${array[$ix]}"
done
echo

arr=(${array[*]})
echo "After unquoted expansion: ${#arr[*]}"
for ix in ${!arr[*]}
do
    printf "   %s\n" "${arr[$ix]}"
done
echo

arr=("${array[*]}")
echo "After * quoted expansion: ${#arr[*]}"
for ix in ${!arr[*]}
do
    printf "   %s\n" "${arr[$ix]}"
done
echo

arr=("${array[@]}")
echo "After @ quoted expansion: ${#arr[*]}"
for ix in ${!arr[*]}
do
    printf "   %s\n" "${arr[$ix]}"
done
```

When run it outputs:
```
Number of items in original array: 4
   first item
   second item
   third
   item

After unquoted expansion: 6
   first
   item
   second
   item
   third
   item

After * quoted expansion: 1
   first item second item third item

After @ quoted expansion: 4
   first item
   second item
   third
   item
```

# Hashes
[Bash associative arrays](http://www.artificialworlds.net/blog/2012/10/17/bash-associative-array-examples/)

```bash
typeset -A age
age["bob"]=42
age["alice"]=31
print ${age[bob]}

name=bob
print ${age[$name]}
```

# if
## else & elif
```
if [[ conditional expression1 ]]; then
        statement1
        statement2
        ...
elif [[ conditional expression2 ]]; then
        statement3
        statement4
        ...
else
        statement5
fi
```

## Operators
Complete list in `man bash` under `CONDITIONAL EXPRESSIONS`.

|**Operator**|**true if**|
| --- | --- |
|`-e file`|`file` exists, beware of [wildcards](https://stackoverflow.com/questions/6363441/check-if-a-file-exists-with-wildcard-in-shell-script)|
|`-a file`|like `-e` but deprecated|
|`-d file`|`file` is a directory|
|`-f file`|`file` is a regular file|
|`-L file`|`file` is a symbolic link|
|`file1 –nt file2`|`file1` is newer than `file2`, or if `file1` exists and `file2` does not|
|`-z $var`|`$var` is `NULL`|
|`-n $var`|`$var` is not `NULL`|

## String comparison
```bash
if [[ $VAR = "String" ]]
```

If you want to know if a variable ends with `ing`:
```bash
if [[ $VAR == *ing ]]
```

## Two conditions
```bash
if [[ $VAR -gt 0 && $VAR –lt 7 ]]
```

## Regular expressions
```bash
re="http://([^/]+)/"
if [[ $name =~ $re ]]; then echo ${BASH_REMATCH[1]}; fi
```

You can use negations like `[^/]`.  
You cannot use shorthand character classes like `\d`.

## Ternary operator
```bash
[[ $b = 5 ]] && (a="$c"; exit 5) || a="$d"
```

## Test exit value of command
```bash
if grep -q fooregex myfile; then
```

# printf
## Colored output
```bash
echo -e "\033[31mHello World\033[0m"
```

or

```bash
printf "\e[1;31mHello World\e[0m\n"
```

|**Color**|**Value**|
| --- | --- |
|Black|`0;30`|
|Dark Gray|`1;30`|
|Blue|`0;34`|
|Light Blue|`1;34`|
|Green|`0;32`|
|Light Green|`1;32`|
|Cyan|`0;36`|
|Light Cyan|`1;36`|
|Red|`0;31`|
|Light Red|`1;31`|
|Purple|`0;35`|
|Light Purple|`1;35`|
|Brown|`0;33`|
|Yellow|`1;33`|
|Light Gray|`0;37`|
|White|`1;37`|

## Leading zeroes
```bash
printf "%06d\n" 3
000003
```

# for
```bash
for i in {1..5}
do
    echo "$i"
done
```

```bash
start=1
end=5
echo "Countdown"
 
for (( c=$start; c<=$end; c++ ))
do
        echo -n "$c "
        sleep 1
done
 
echo
echo "Boom"
```

# while
## continue
One can skip the rest of a loop and directly go to the next iteration with `continue`.

```bash
while read line
do
    if [[ $line = *.gz ]];then
        continue
    else
        print $line
    fi
done
```

## break
One can also prematurely leave a loop with `break`.

```bash
while read line
do
    if [[ $line = *!(.c) ]];then
        break
    else
        print $line
    fi
done
```

# find
## delete files older than 30 days
End directory with `/`, except when using `.`.

```bash
find <directory> -mtime +30 –type f –delete
```

## delete files younger than two minutes
```bash
find <directory> -type f -mmin -2 -delete
```

## search only in current directory
```bash
find . -maxdepth 1 -name "*.pl"
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
|ctime|Change|`-clt`|The time when changes were made to the file or directory's inode (owner, permissions, etc.). The ctime is also updated when the contents of a file change. In UNIX, it is not possible to tell the actual creation time of a file.|
