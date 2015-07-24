# CPU Metrics

## Context Switches

### Recall: Linux Process Context Switches
A mechanism to store current process *state* ie. Registers, Memory maps, Kernel structs (eg. TSS in 32bit), and load another (or a new one). Context switches are usually computationally expensive (although optimization exist), yet inevitable. For example, they are used to allow multi-tasking (eg. preemption), and to switch between user and kernel modes.

### Task CS1: Context Switches

1. Execute `vmstat 2` and write down the current context switch rate (`cs` field)
2. Let's raise that number by executing `stress -i 10`
	1. What is the current context switch rate?
	2. What is causing this rate? multi-tasking? interrupts? switches between kernel and user modes?
	3. Kill the `stress` command, and watch the rate getting lower
3. Now let's see how high context switch rate affect a dummy application
	1. Read and run the dummy application `scripts/cpu/dummy_app.py`
	2. Write down the percentiles and the context switch rate
	3. *In the same session*, raise the context switch rate using `stress -i 10 &`, and re-run the dummy application. What are the current percentiles? Did the high context switch rate affected most of `foo()` runs (ie. the 50th percentile)? If not, why?
4. Finally, make sure that we are seeing context switching overhead and not scheduling
	1. Retry this experiment, but this time using `stress -d 10 --hdd-bytes 1k &` (verify that the CPU usage is roughly the same, and the context switch rate is lower)
	2. Compare the dummy application run-time and number of context switches it experienced using: `perf stat -e cs python scripts/cpu/dummy_app.py`

### Discussion

- Can performance measurements on a staging environment truly estimate performance on production?
- Why did we ran the `stress` command and our dummy application in the same session?
	- Read about the [magic kernel patch](http://www.phoronix.com/scan.php?page=article&item=linux_2637_video&num=1)

### Tools

 - Most tools are using `/proc/vmstat` to fetch global context switching information (`ctxt` field), and `/proc/[PID]/status` for process specific information (`voluntary_context_switches` and `nonvoluntary_context_switches` fields)
 - From the command-line you can use
	 - `vmstat <delay> <count>` for global information
	 - `pidstat -w -p <pid> <delay> <count>` for process specific information

#### Next: [Memory Usage](memory-usage.md)