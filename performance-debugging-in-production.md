# Thursday, June 14th - Performance Debugging: Finding bottlenecks in distributed systems

Bottlenckecs are bound to occur as systems scale!
  - Where do you start? So many metrics to look at
  - How do you identify bottlenecks in multiple services of a distributed system?
  - How do you identify bottlenecks in a single process? (is it a library, my code, my OS?)

Case study:
  - Service aggregating query metrics
  - Required a unique set of tags for each metric, so they added a tag per process
  - Throughput was pretty terrible, consisted of too many Python processes, and dashboarding multiple timeseries sucked
  - Had an edge service that would write to message queues the aggregation process would read from
  - Solutions:
    - Hash query metrics by customer. Any unique tags would now be consistent across customer, and hit the same aggregation process.
    - Move from using async I/O (Twisted) in Python to goroutoines
    - Aggregate in-memory
  - Things went wrong: performance skewed
  - To fix, they:
    - Added operational metrics
    - Reproduced the symptoms
    - Profiled heap allocations of a single aggregation process
      - Used eBPF with bcc's memleak.py to do this after using Go-Torch and Go's built-in profiler
    - Used go's benchmarking tools to measure latency for each operation too
    - Overall summary, the largest hurdle was reproducing prodcution traffics:
      - Couldn't reproduce the rate of messages
      - Couldn't reproduce the variety of metrics
    - To solve irreproducibility issues, they tried:
      - Load testing software
      - Have message queues copy production traffice into a test environment

What worked?
  - Built-in profiling tools
  - Benchmark tests around important functions
  - eBPF
  - Collecting operational metrics

What didn't work?
  - Being able to reproduce a production-level load in dev/test
  - Keeping a base comparison when making changes for the sake of performance
  - Shouldn't changed the initial service more iteratively

Case study:
  - Performance degradation of public API serivce
  - Nginx servers -> time series DB -> main DB
  - Steps taken:
    - Instrumented the I/O of the API process
    - Distributed tracing
    - Profile the on-CPU time for potential blocking code
    - Instrumented database queries as well
  - They were able to modify queries and identify specific hot pathsto be optimised

Overall, lessons:
  - Logs + metrics are invaluable!
  - Distributed tracing is awesome!
  - Profilers are great!
  - Pprof visualisations are great with eBPF
  - Keep your changes small
  - Having a baseline to compare yourself to is valuable!
