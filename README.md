#TeraSort benchmark for Spark

[![Build
Status](https://travis-ci.org/ehiggs/spark-terasort.svg)](https://travis-ci.org/ehiggs/spark-terasort)

This is an example Spark program for running TeraSort benchmarks. It is based on
work from [Reynold Xin's branch](https://github.com/rxin/spark/tree/terasort),
but it is not the same TeraSort program that currently holds the
[record](http://sortbenchmark.org/). That program is
[here](https://github.com/rxin/spark/tree/sort-benchmark/core/src/main/scala/org/apache/spark/sort).

# Building

`mvn install`

# Running

`cd` to your your spark install.

## Generate data

    ./bin/spark-submit --class com.github.ehiggs.spark.terasort.TeraGen 
    path/to/spark-terasort/target/spark-terasort-1.0-SNAPSHOT-jar-with-dependencies.jar 
    1g file://$HOME/data/terasort_in 

## Sort the data
    ./bin/spark-submit --class com.github.ehiggs.spark.terasort.TeraSort
    path/to/spark-terasort/target/spark-terasort-1.0-SNAPSHOT-jar-with-dependencies.jar 
    file://$HOME/data/terasort_in file://$HOME/data/terasort_out

## Validate the data
    ./bin/spark-submit --class com.github.ehiggs.spark.terasort.TeraValidate
    path/to/spark-terasort/target/spark-terasort-1.0-SNAPSHOT-jar-with-dependencies.jar 
    file://$HOME/data/terasort_out file://$HOME/data/terasort_validate

# Known issues

## Performance

This terasort doesn't use the partitioning scheme that Hadoop's Terasort uses.
This results in not very good performance. I could copy the Partitioning code
from the Hadoop tree verbatim but I thought it would be more appropriate to
rewrite more of it in Scala.

I haven't pulled the DaytonaPartitioner from the record holding sort yet because
it's pretty intertwined into the rest of the code and AFAIK it's not really
idiomatic Spark.

## Functionality on native file systems

TeraValidate can read the file parts in the wrong order on native file systems
(e.g. if you run Spark on your laptop, on Lustre, Panasas, etc). HDFS apparently
always returns the files in alphanumeric order so most Hadoop users aren't
affected. I thought I fixed this in the TeraInputFormat, but I was able to
reproduce it since migrating the code from my Spark terasort branch.

## spark versions
To test against various versions of spark change the pom.xml spark version as appropriate.
```xml
  <scala.version>2.11.6</scala.version>
  <scala.binary.version>2.11</scala.binary.version>
  <spark.version>2.0.0-preview</spark.version>
```
# Contributing

PRs are very welcome!
