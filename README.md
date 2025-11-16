# Streaming in hadoop
## Selection using grep and mapred
The following code runs a mapper-only Hadoop Streaming job that filters lines starting with a or b from `marks.txt` into `output/.`. A parameter `-D mapred.reduce.tasks=0` means that only a mapper is used.
```shell
mapred streaming \
  -D mapred.reduce.tasks=0 \ # for only using mapper
  -input marks.txt \
  -output output \
  -mapper 'grep -E "^a|^b"'
```
The `marks.txt` file looks like the following:
```
alice 50
bruce 70
charlie 80
dan 75
```

## Transformation using sed/cut and mapred
The following converts **substitutes** using this format `s/old/new`
```bash
sed s/al/Al/
```
The following allows us to access the second variable **(seecond field)**:
```bash
cut -f2 -d" "
```
> Do note: the `d` flag is used to set a custom delimiter, the default is `TAB`.
> For choosing two or more fields use `cut -f1,2,3`


You can also pipe multiple mappers together as shown in the following example.
```bash
mapred streaming \
  -D mapred.reduce.tasks=0 \
  -input <path to input file>
  -output <path to output1 file>
  -mapper <function to be mapped to eac line>

mapred streaming \
  -D mapred.reduce.tasks=0 \
  -input <path to output1 file> \
  -output <path to output2 file> \
  -mapper <function to be mapped to eac line>
```

## Hadoop streaming with mapper/reducer
The following code counts words:
```
mapred streaming \
-input words.txt \
-output output \
-mapper /bin/cat \
-reducer /usr/bin/wc
```
Both `wc` and `cat` are shell commands but with absolute paths.

## Using python with mapper
We create the following python file `filter.py`:
```py
#!/usr/bin/env python3

import sys

for number in sys.stdin:
  if (number != "\n") and int(number) > 50:
    print(int(number))
```
We change the file mode to execute the file, `chmod x+u filter.py`.
Then we use a similar hadoop command as before:
```bash
mapred streaming \
-D mapred.reduce.tasks=0 \
-input numbers.txt\
-output output\
-mapper <smth like /home/bigdata/filter.py>
```

## Using python with mapreduce
`mapper.py`
```py
import sys

for line in sys.stdin:
  line = line.strip()
  words = line.split()
  for word in words:
    print(f"{word}\t1") # 
```
`reducer.py`
```py
#!/usr/bin/env python3
import sys
current_word = None
current_count = 0
word = None
for line in sys.stdin:
  line = line.strip()
  word, count = line.split('\t', 1)
  try:
    count = int(count)
  except ValueError:
    continue
  if current_word == word:
    current_count += count
  else:
    if current_word:
      print ('%s\t%s' % (current_word, current_count))
  current_count = count
  current_word = word
if current_word == word:
  print ('%s\t%s' % (current_word, current_count))
```




