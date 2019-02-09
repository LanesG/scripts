# vim
My favourite `.vimrc`:
```
colorscheme murphy

" Map Ctrl+l to clear highlighted searches
nnoremap <silent> <C-l> :<C-u>nohlsearch<CR><C-l>
```

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


# Functions
```bash
function_name () {
   command...
}
```

To call a function with arguments:
```bash
function_name "$arg1" "$arg2"
```

The function refers to passed arguments by their position (not by name), that is `$1`, `$2`, and so forth. `$0` is the name of the script itself.

Example:
```bash
function_name () {
   echo "Parameter #1 is $1"
}
```

Also, you need to call your function *after* it is declared. 


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

# Brace expansion
## Rename foo.bar to foo.txt
```
mv foo.{bar,txt}
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
length=5
for i in $(seq 0 $length)
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
## Variables
[A variable modified inside a while loop is not remembered](https://stackoverflow.com/questions/16854280/a-variable-modified-inside-a-while-loop-is-not-remembered)

see [Here Strings](#here-strings)

```bash
declare -a localgroups
while read line; do
    name=$(cut -d: -f1 <<< $line)
    localgroups=("${localgroups[@]}" $name)
done <<< "$(grep -e ^sentryadmins -e ^bigdata_ -e ^datalab_ /etc/group)"

for group in "${localgroups[@]}"; do
    echo $group
done
```

## true
```bash
while true; do
```

## Conditionals
```bash
while [[ $RC -ne 0 ]]; do
```

## continue
One can skip the rest of a loop and directly go to the next iteration with `continue`.

```bash
while read line; do
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
while read line; do
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

## exclude directory
```bash
find / -path /misc -prune -o -uid 995 -ls
```

# exec
## Write script output to file
```bash
exec > /tmp/script.out 2>&1
set –x
```

# case
```bash
case $var in
    a)            echo "a found!";;
    b | c)        echo "b or c found!"
                  echo "Zweite Zeile"
                  ;;
    day | night)  echo "time found!";;
    W[ai]nd)      echo "Found Wand or Wind.";;
    *)            echo "Other Arguments found: $VAR";;
esac
```

# awk
## First column
```bash
awk '{print $1}' <file>
```

## Last column
```bash
awk '{print $NF}' <file>
```

## Second to last column
```bash
awk '{print $(NF-1)}' <file>
```

## Number of columns
```bash
awk '{print NF}' <file>
```

## First row
```bash
awk '{if (NR == 1) print}' <file>
```

## Print text in brackets
```bash
echo „The text in (brackets) | awk -F"[(]|[)]" '{print $2}'
brackets
```

## Substrings
### From n to end
```bash
awk '{ print substr($0,<n>,length($0)) }' <file>
```

### From beginning to nth row from end
```bash
awk '{ print substr($0,1,length($0)-<n>) }' <file>
```

### Several substrings in row
```bash
echo Hello | awk '{ printf("%srr%s", substr($0,1,2), substr($0,5,1)) }'
Herro
```

# sed
## Replace in file
```bash
sed -i 's/replace_this/with_this/g' <file>
```

## Delete line with specific string in file
```bash
sed -i '/my_string/d' <file>
```

## Delete first row
```bash
sed '1d'
```

## Delete last row
```bash
sed '$d'
```

## Delete empty lines
```bash
sed '/^$/d'
```

## Delete trailing spaces
```bash
sed 's/ *$//g'
```

## Replace newline with comma
```bash
sed ':a;N;$!ba;s/\n/,/g' <Datei>
```

## Output parts of a file
### From third line
```bash
sed '1,2d' <file>
```

or

```bash
tail -n+3 <file>
```

Example file:
```
distri1
distri2
!before1
before2
!!after1
after2
```
### From beginning to first exclamation mark
```bash
sed -n '1,/^!/p' | grep –v ^!
distri1
distri2
```

### From single exclamation mark to the two exclamation marks
```bash
sed -n '/^![^!]/,/!!/p' | grep –v ^!!
!before1
before2
```

Without the two exclamation marks the file will be printed until the end.

### From two exclamation marks until the end
```bash
sed –n '/^!!/,$p'
!!after1
after2
```

# grep
## Incomplete list
* `egrep` is the same as `grep -E`, `--extended-regexp`
* `fgrep` is the same as `grep -F`, `--fixed-strings`
* `zgrep` - search possibly compressed files for a regular expression
* `pcregrep` - a `grep` with Perl-compatible regular expressions with option `-M`, `--multiline`

## Find exact string
```bash
grep -x
```

## Find several strings
```bash
grep –e foo –e bar
```

## Exclude several strings
```bash
grep -Ev 'foo|bar'
```

## Exclude grep process
```bash
ps -ef | grep [M]yProcess
```

## Search in tar.gz files
```bash
zgrep -a string myArchive.tar.gz
```

## Show surrounding lines
```bash
grep -C 2 foo
```

## grep -p
```bash
perl -00ne "print if /<search string>/" <file>
```
| Option | Description |
| --- | --- |
|`-00`|causes the perl line-separator variable `$/` to be set to `\n\n`|
|`-ne`|loop over input|


# tr
## Translate lower to upper case
```bash
tr '[:lower:]' '[:upper:]'
```

or

```bash
${username^^}
```

Lower case:
```bash
${username,,}
```


# tar
| Option | Description |
| --- | --- |
|`-A`|append tar files to an archive|
|`-c`|create new archive|
|`-d`|find differences between archive and file system|
|`-r`|append files to the end of an archive|
|`-t`|list files|
|`-u`|only append files that are newer than the existing in archive|
|`-x`|extract files from archive|
|`-v`|list name of each file as it is processed|
|`-f`|archive uses archive variable|

## Create compressed archive
```bash
tar –cvfz <archiv name>.gz <directory>
```

## Fill archive in wile loop
First you have to create an empty archive.
```bash
tar -cf my.tar -T /dev/null
find . -name *.txt | while read line
do
    tar -rvf my.tar $line
done
```


# rm
Delete a directory beginning with a dash.
```bash
rm –r -- -MyDirectory
```


# mailx
`sendmail` has to run on the system.

## Send mail
```bash
echo "<text>" | mailx –s "<subject>" foo@acme
```

## Send file per mail
```bash
mutt -s <subject> foo@acme -a <file> < /dev/null
```


# date
```bash
date +"%d/%m/%Y"
06/05/2013
```

| Option | Description |
| --- | --- |
|`%y`|last two digits of year (00..99)|
|`%Y`|year|
|`%m`|month (01..12)|
|`%d`|day of month (e.g, 01)|
|`%-d`|day of month (e.g, 1)|
|`%H`|hour (00..23)|
|`%M`|minute (00..59)|
|`%S`|second (00..60)|
|`%s`|seconds since 1970-01-01 00:00:00 UTC|

## Convert seconds since the epoch (1970-01-01 UTC) to a date
```bash
date -d@696969
Fri Jan  9 02:36:09 CET 1970
```

## Show number of days in a month
```bash
cal $(date +"%m %Y") | awk 'NF {DAYS = $NF}; END {print DAYS}'
```

## Other cool stuff
```bash
date --date="10 minutes ago"
Fri Feb 23 15:13:37 CET 2018
```

```bash
date --date="10 days ago" +%Y%m%d
20180213
```

Show date of last Monday or the 1st if last Monday was last month
```bash
[[ (`date +%d` -lt 5) && (`date +%a` = "Fri") ]] && echo $("01/`date +%m/%Y`") || echo $(date -d "last monday" +%d/%m/%Y)
```


# uniq
Discard all but one of **successive** identical lines.

| Option | Description |
| --- | --- |
|`-c`|Precedes each output line with a count of the number of times each line appeared in the input file.|
|`-d`|Displays only the repeated lines.|

Sort by event count:
```bash
uniq -c -d <Datei> | sort -k 1nr
```


# seq
Print a sequence of numbers.

```bash
seq 8 11
8
9
10
11
```

```bash
seq -w 8 11
08
09
10
11
```


# paste
Merge lines of files.

```bash
paste -d "\t" file1 file2 | while read field1 filed2; do
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


# Sticky bit

Verwendung findet das Bit bei Verzeichnissen, wo es zur Folge hat, dass Benutzer Dateien anderer Benutzer nicht löschen können. Früher wurde es bei Dateien benutzt, wo das Sticky Bit bewirkte, dass nach dem Ausführen der Datei der Programmcode weiterhin im Arbeitsspeicher verblieb (bei Swapping-Systemen ohne Paging).

Das Stiky Bit wird im Execute-Recht für die Gruppe other dargestellt. Ein kleines `t` bedeutet, dass auch das Ausführungsrecht für `other` gesetzt ist. Bei einem großen `T` ist das Ausführungsrecht nicht gesetzt.


# Volumes & Filesystems
## Check free space
```
lspv hdisk0 -> pvs
lsvg -l t2vg - Mountpoint <-> LV
```

## Enlarge filesystem
```
lvextend -L 3G /dev/mapper/rootvg-optlv
resize2fs -p /dev/mapper/rootvg-optlv  (ext2 und ext3)
resize4fs ...                          (ext4) (ab Version 6 existiert nur noch der Befehl resize2fs)
xfs_growfs /opt                        (xfs) (füllt auf LV-Größe auf)
```

## Reduce filesystem
```
resize2fs -p /dev/mapper/rootvg-optlv 3G
lvreduce -L 3G /dev/mapper/rootvg-optlv
```
