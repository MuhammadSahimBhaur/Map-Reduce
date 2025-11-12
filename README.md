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
The following converts substitutes using this format `s/old/new`
```bash
sed s/al/Al/
```
The following allows us to access the second variable
```bash
cut -f2 -d" "
```
> Do note: the `d` flag is used to set a custom delimiter, the default is `TAB`.




