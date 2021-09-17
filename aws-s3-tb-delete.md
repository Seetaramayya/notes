# S3 Terabytes Data delete 

  Task in hand is roughly 200 million objects needs to be deleted which are stored in AWS S3. Files are stored in 
`/blah/2018/01-01/` and `//blah/2018/01-01/`. 

  This task is split into 3 parts

1. fetch all keys to file ( I tried both background process and foreground process)

```shell
# loop all folder 
for k in 01-01 02-01 01-04 01-05 01-06 01-07 01-08 01-09 01-10 01-11 01-12 01-13 01-14 01-15 01-16 01-17 01-18; do 
  echo "Background process is running $k"; 
  aws s3 ls "s3://MY-FAMOUS-S3-BUCKET-NAME/blah/2018/$k/" | sed -nre "s|[0-9-]+ [0-9:]+ +[0-9]+ |/blah/2018/$k/|p" > file-of-keys-2018-$k &
done
```
2. `run.sh` looks like this ( executed with `nohup ./run.sh >> run.log &` in EC2 instance of type `m5.xlarge` for 24 hours to complete all the items)
```shell
#!/usr/bin/env bash

set -e

for filename in $(find . -not -name "*.log" -name "file*"); do
  ./bulk-delete2 $filename >> $filename.log || true
done%
```
3. `bulk-delete2` looks like this

```shell
#!/usr/bin/env bash

set -e

[[ -z "$1" ]] && echo "Usage: $0 <file-name>" && exit 1

fileName=$1
total=$(cat $fileName | wc -l)
echo "Total records to be delete are: $total in $fileName"
cat $fileName | xargs -P5 -n1000 bash -c 'aws s3api delete-objects --bucket ais-storage --delete "Objects=[$(printf "{Key=%s}," "$@")],Quiet=true"' _
echo "Completed "
rm $fileName
```
