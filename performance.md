# Performance

## Perf

The [perf](https://man7.org/linux/man-pages/man1/perf.1.html) tool was started as a tool to profile application under Linux using performance counters. It has evolved into a more general purpose tool and now it allows a user to insert tracepoints, kprobes and
uprobes. Perf can profile both user-space and kernel-space execution and is based on the perf_event interface that is exposed by the kernel.

Perf provides a set of operations that each have specific arguments:  stat, record, report, top, annotate, ftrace, list, probe, etc


### perf record
- Perf record allows to record performance events per-thread, per-process and per-cpu basis.
- The Kernel needs to be configured with CONFIG_PERF_EVENTS=y.
- This is the first command in the perf command cycle, it allows execution of the program and the capture of output into perf.data.
- The perf.data file can then be analyzed using perf annotate and perf report commands. This means it is possible to collect data on a target embedded device and then subsequently analyse it on another computer.
