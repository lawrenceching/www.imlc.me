# How to split string into array in Bash

A snippet to split string into an array. And shows you how to retrieve elements from the array.

```
set -x

STR="/path/to/a/config/file"
TMP=$IFS
IFS='/'
WORDS=($STR)

for i in "${WORDS[@]}"; do
    echo "$i"
done

echo "#1 ${WORDS[1]}"
echo "#2 ${WORDS[2]}"
echo "#3 ${WORDS[3]}"
echo "#4 ${WORDS[4]}"
echo "#5 ${WORDS[5]}"

echo "#Last item ${WORDS[@]: -1}"

IFS=$TMP
```
