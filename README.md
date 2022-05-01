# JMH ConcurrentHashMap Benchmarks

## Template
The project has been built using recommended approach in [JMH documentation](https://github.com/openjdk/jmh#preferred-usage-command-line)
```bash
$ mvn archetype:generate \
  -DinteractiveMode=false \
  -DarchetypeGroupId=org.openjdk.jmh \
  -DarchetypeArtifactId=jmh-java-benchmark-archetype \
  -DgroupId=dev.igabaydulin.java.benchmarks \
  -DartifactId=concurrent-hashmap-benchmarks \
  -Dversion=1.0
```

## How to Run Benchmarks
```bash
$ mvn clean verify
$ java -jar target/benchmarks.jar
```

## Details
There are two benchmark groups: the first group called `compute` removes element from map using
`ConcurrentHashMap#compute` and puts element back in parallel, while the second group called `remove`
removes element from map using `ConcurrentHashMap#remove`.

The number of total threads and the number of threads per benchmark are specified explicitly
(`12` and `6` respectively). You may change the numbers.

## Results
**REMEMBER: The numbers below are just data. To gain reusable insights, you need to follow up
on why the numbers are the way they are. Use profilers (see -prof, -lprof), design factorial
experiments, perform baseline and negative tests that provide experimental control, make sure
the benchmarking environment is safe on JVM/OS/HW level, ask for reviews from the domain experts.
Do not assume the numbers tell you what you want them to tell.**

### Removing element only
* Total number of threads: 12
* Number of groups: 12
* Number of threads to put element: 0
* Number of threads to remove element: 1
```bash
Benchmark             Mode  Cnt    Score    Error   Units
MyBenchmark.compute  thrpt   25  210.985 ±  3.872  ops/us
MyBenchmark.remove   thrpt   25  920.081 ± 20.374  ops/us
```

### Element removed more often than inserted
* Total number of threads: 12
* Number of groups: 2
* Number of threads to put element: 1
* Number of threads to remove element: 5
```bash
Benchmark                               Mode  Cnt    Score   Error   Units
MyBenchmark.compute                    thrpt   25   11.752 ± 0.265  ops/us
MyBenchmark.compute:put_compute        thrpt   25    1.763 ± 0.043  ops/us
MyBenchmark.compute:remove_by_compute  thrpt   25    9.989 ± 0.226  ops/us
MyBenchmark.remove                     thrpt   25  194.384 ± 5.292  ops/us
MyBenchmark.remove:put_remove          thrpt   25    3.600 ± 0.080  ops/us
MyBenchmark.remove:remove_by_remove    thrpt   25  190.784 ± 5.333  ops/us
```

### Only one thread per insert and deletion
* Total number of threads: 12
* Number of groups: 6
* Number of threads to put element: 1
* Number of threads to remove element: 1
```bash
Benchmark                               Mode  Cnt    Score    Error   Units
MyBenchmark.compute                    thrpt   25   68.463 ±  5.162  ops/us
MyBenchmark.compute:put_compute        thrpt   25   38.444 ±  2.017  ops/us
MyBenchmark.compute:remove_by_compute  thrpt   25   30.019 ±  3.770  ops/us
MyBenchmark.remove                     thrpt   25  193.470 ± 16.619  ops/us
MyBenchmark.remove:put_remove          thrpt   25   65.546 ±  5.172  ops/us
MyBenchmark.remove:remove_by_remove    thrpt   25  127.924 ± 15.819  ops/us
```

### Element inserted more often than removed
* Total number of threads: 12
* Number of groups: 2
* Number of threads to put element: 5
* Number of threads to remove element: 1
```bash
Benchmark                               Mode  Cnt   Score   Error   Units
MyBenchmark.compute                    thrpt   25  15.548 ± 0.387  ops/us
MyBenchmark.compute:put_compute        thrpt   25  13.069 ± 0.374  ops/us
MyBenchmark.compute:remove_by_compute  thrpt   25   2.479 ± 0.074  ops/us
MyBenchmark.remove                     thrpt   25  25.643 ± 0.323  ops/us
MyBenchmark.remove:put_remove          thrpt   25  13.461 ± 0.292  ops/us
MyBenchmark.remove:remove_by_remove    thrpt   25  12.182 ± 0.419  ops/us
```

### Element is equally inserted and removed with a lot of threads
* Total number of threads: 12
* Number of groups: 1
* Number of threads to put element: 6
* Number of threads to remove element: 6
```bash
Benchmark                               Mode  Cnt   Score   Error   Units
MyBenchmark.compute                    thrpt   25   5.880 ± 0.198  ops/us
MyBenchmark.compute:put_compute        thrpt   25   2.717 ± 0.110  ops/us
MyBenchmark.compute:remove_by_compute  thrpt   25   3.163 ± 0.096  ops/us
MyBenchmark.remove                     thrpt   25  27.339 ± 0.599  ops/us
MyBenchmark.remove:put_remove          thrpt   25   3.195 ± 0.045  ops/us
MyBenchmark.remove:remove_by_remove    thrpt   25  24.144 ± 0.615  ops/us
```

## Conclusion
Looks like `ConcurrentHashMap#remove(key)` is faster than `ConcurrentHashMap#compute(key, (k, v) -> null)`,
but it's not clear why yet. Have to check the implementation.
