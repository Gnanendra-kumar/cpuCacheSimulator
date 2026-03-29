
# CPU Cache Simulator Project

## Introduction


Caches are pivotal in reducing the performance gap between processors and main
memory. Many applications are memory bound with the memory hierarchy being the
main performance bottleneck. Caches leverage the principles of locality to attempt
to reduce the average memory access time. Implemented a memory hierarchy in a simulated environment.  The framework (main.c) will perform
all of the command-line switches as well as the reading in of the trace files.

## Traces

Simulator models a cache hierarchy based on traces of real programs.
Each line in the trace file contains the address of a memory access in hex as
well as where the access should be directed, either to the I$ (I) or D$ (D).

We provide one full real-program trace (~200M memory references), and one smaller (20M references) to aid in testing the project.
```
<Address> <I or D>

Sample Trace from tsman.bz2:
0x648 I
0x64c I
0x650 I
0x654 I
0x658 I
0x40868 D
0x65c I
0x660 I
0x664 I
0x668 I
```


## Running the Cache Simulator

In order to build the simulator you simply need to run `make` in the src/
directory of the project.  You can then run the program on an uncompressed
trace as follows:

`./cache <options> [<trace>]`

If no trace file is provided then the simulator will read in input from STDIN.
Some of the traces we provided are rather large when uncompressed so we have
distributed them compressed with bzip2.  If you want to run the simulator on
a compressed trace then you can do so by doing the following:

`bunzip2 -kc trace.bz2 | ./cache <options>`

In either case the options that can be used to change the configurations of
the memory hierarchy are as follows:

```
  --help                     Print this message
  --icache=sets:assoc:hit    I-cache Parameters
  --dcache=sets:assoc:hit    D-cache Parameters
  --l2cache=sets:assoc:hit   L2-cache Parameters
  --inclusive                Makes L2-cache be inclusive
  --blocksize=size           Block/Line size
  --memspeed=latency         Latency to Main Memory
```

### Configuration

```
  [cache]Sets       // Number of sets in the cache
  [cache]Assoc      // Associativity of the cache
  [cache]HitTime    // Hit Time of the cache in cycles
  blocksize         // The Block or Line size
  inclusive         // Indicates if the L2 is inclusive
  memspeed          // Latency to Main Memory
```

Each cache can be configured to have a different number of Sets, Associativity
and Hit Time.  Additionally the block size of the memory system can be
configured.  The **I$**, **D$**, and **L2$** all have the same block size.  The L2 cache can be configured to be inclusive.  You are also able to set the latency of main memory.

### Inclusion

If the L2 is configured to be inclusive, then all valid lines in the D$ and I$
must be present in the L2.  This means if a line is evicted from the L2 then an
invalidation must be sent to both the data cache as well as the instruction
cache. If the L2 is not inclusive then this restriction doesn't hold and the
D$ and I$ could potentially hold valid lines not present in the L2.  Generally
you should observe worse cache performance when the L2 is configured to be
inclusive. 

### Replacement Policy
**LRU replacement policy**.

### Statistics

```
  [cache]Refs        // cache references (total # of accesses)
  [cache]Misses      // cache misses
  [cache]Penalties   // cache penalties
```

In addition to modeling the memory hierarchy is expected to maintain a
number of statistics for each cache in the hierarchy.  These statistics will be
used to calculate the miss rate and average access time for each cache. The Penalties
statistic will keep track of the total penalty (in cycles) for a simulation.  A
penalty is defined as any extra overhead incurred by accessing the cache beyond
the hit time. This means that the penalty does not take into account the
latency of an access due to a cache hit.  
