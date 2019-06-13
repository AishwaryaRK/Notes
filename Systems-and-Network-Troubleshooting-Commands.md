# Systems Troubleshooting

### System Resources:

1. CPUs
2.  memory
3.  disks
4. network

### Steps for systems troubleshooting:

1. Get a high level idea of system resource usage and running processes:
    1. errors
    2. saturation metrics
    3. resource utilization

        uptime
        
        dmesg | tail
        
        vmstat 1
        
        mpstat -P ALL 1
        
        pidstat 1
        
        iostat -xz 1
        
        free -m
        
        sar -n DEV 1
        
        sar 1
        
        sar -n TCP,ETCP 1
        
        top
        
        ps

2. Process of elimination
    1. processes wanting to run on CPU
    2. processes blocked in uninterruptible I/O (usually disk I/O). 
    3. resource load (or demand)

### Performance analysis commands:

1. #### uptime [resource demand] → load avg
    - moving sum averages with a 1 minute, 5 minute, and 15 minute constant.

        $ uptime 
        23:51:26 up 21:31, 1 user, load average: 30.02, 26.43, 19.02

2. #### dmesg | tail [error]
    - display system message buffer, last 10 system messages

3. #### vmstat 1 [resource demand] → r(procs), free, si, so, buff, cache(mem), us,sy(cpu)
    - virtual memory stat

        $ vmstat 1
        procs -----------memory----------   ---swap-- -----io----  -system-- ------cpu-----
         r  b   swpd   free   buff  cache     si   so    bi    bo   in   cs   us sy id wa st
         2  0      0 4166832  69240 2006276    0    0  6898  3442  1349 2336  11  6 81  2  0
         1  0      0 4166708  69240 2006268    0    0     0   728  2796 7387   1  1 98  0  0

    - procs r
        - runnable tasks
        - Number of processes running on CPU and waiting for a turn
        - Saturation r > #CPUs
    - free memory <free -m>
    - si, so: Swap-ins and swap-outs
        - If these are non-zero, you’re out of memory.
    - CPU time breakdowns <>
        - us - user time
        - sy - system time (kernel)
        - id - idle
        - wa - wait I/O
        - st - stolen time

4. #### mpstat -P ALL 1 [resource demand] → %idle
    - multiprocessor stats
    - apt install sysstat
    - CPU time breakdowns per CPU
    - used to check for an imbalance.
    - A single hot CPU can be evidence of a single-threaded application.

        $ mpstat -P ALL 1
        
        15:49:51     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
        15:49:52     all    1.04    0.00    1.30    0.52    0.00    0.26    0.00    0.00    0.00   96.88
        15:49:52       0    1.03    0.00    1.03    1.03    0.00    1.03    0.00    0.00    0.00   95.88
        15:49:52       1    1.03    0.00    2.06    1.03    0.00    0.00    0.00    0.00    0.00   95.88
        15:49:52       2    2.11    0.00    1.05    0.00    0.00    0.00    0.00    0.00    0.00   96.84
        
        15:49:52     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
        15:49:53     all    1.54    0.00    2.83    0.51    0.00    0.00    0.00    0.00    0.00   95.12
        15:49:53       0    1.03    0.00    5.15    0.00    0.00    0.00    0.00    0.00    0.00   93.81
        15:49:53       1    2.02    0.00    4.04    0.00    0.00    0.00    0.00    0.00    0.00   93.94
        15:49:53       2    2.06    0.00    2.06    1.03    0.00    0.00    0.00    0.00    0.00   94.85
        

5. #### pidstat 1 / top [resource demand]

- per-process summary
- processes as responsible for consuming CPU.

    $ pidstat 1
    
    07:41:02 PM   UID       PID    %usr %system  %guest    %CPU   CPU  Command
    07:41:03 PM     0         9    0.00    0.94    0.00    0.94     1  rcuos/0
    07:41:03 PM     0      4214    5.66    5.66    0.00   11.32    15  mesos-slave
    07:41:03 PM     0      4354    0.94    0.94    0.00    1.89     8  java
    07:41:03 PM     0      6521 1596.23    1.89    0.00 1598.11    27  java
    07:41:03 PM     0      6564 1571.70    7.55    0.00 1579.25    28  java

6. #### iostat -xz 1 [resource demand] → r/s, w/s, rkB/s, wkB/s, avgqu-sz, await, svctm, %util

- poor performing disk I/O isn’t necessarily an application issue.
- Many techniques are typically used to perform I/O asynchronously, so that the application doesn’t block and suffer the latency directly (e.g., read-ahead for reads, and buffering for writes).

    $ iostat -xz 1
        
    avg-cpu:  %user   %nice %system %iowait  %steal   %idle
               2.59    0.00    1.89    0.22    0.00   95.30
    
    Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
    sda               0.04     4.58    8.98   12.95   412.66   330.32    67.76     0.04    1.74    0.35    2.71   0.50   1.09
    scd0              0.00     0.00    0.93    0.00    74.85     0.00   160.87     0.00    0.61    0.61    0.00   0.43   0.04
    scd1              0.00     0.00    0.00    0.00     0.02     0.00    34.29     0.00    0.00    0.00    0.00   0.00   0.00

- r/s, w/s, rkB/s, wkB/s - reads, writes, read Kbytes, and write Kbytes per second to the device
- await - The average time for the I/O in milliseconds. This is the time that the application suffers, as it includes both time queued and time being serviced. Larger than expected average times can be an indicator of device saturation, or device problems.
- avgqu-sz - The average number of requests issued to the device. Values greater than 1 can be evidence of saturation
- svctm - service time
- %util - Device utilization, values close to 100% usually indicate saturation.

7. #### free -m [resource demand] → buffers, cached

- buffers - used for (block device) I/O
- cache - page cache, used by file systems.
- near-zero in size, which can lead to higher disk I/O (confirm using iostat), and worse performance

    $ free -m
    
                total       used        free      shared    buffers     cached
    Mem:        245998      24545     221453         83         59        541
    
    -/+ buffers/cache:      23944     222053
    Swap:                   0          0          0

8. #### sar -n DEV 1 [resource demand]

- system activity
- check network interface throughput
- apt install sysstat
- -n  → n/w stats
    - DEV(n/w interfaces)
    - IP(IP traffic)
    - TCP(TCP traffic)
    - ETCP(TCP traffic errors)

    $ sar -n DEV 1
    
    06:24:29 AM     IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
    06:24:30 AM        lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
    06:24:30 AM      ens4      4.95      1.98      0.30      0.23      0.00      0.00      0.00      0.00

- rxkB/s and txkB/s

     sar 1 [resource demand]

- CPU stats

    $ sar 1
    
    06:19:27 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
    06:19:28 AM     all      0.00      0.00      0.00      0.00      0.00    100.00
    06:19:29 AM     all      0.00      0.00      0.00      0.00      0.00    100.00
    06:19:30 AM     all      0.00      0.00      0.00      0.00      0.00    100.00

#### sar -n TCP,ETCP 1 [resource demand]

- TCP metrics

    $ sar -n TCP,ETCP 1
    
    08:18:50 AM  active/s passive/s    iseg/s    oseg/s
    08:18:51 AM      0.00      0.00      7.00      1.00
    
    08:18:50 AM  atmptf/s  estres/s retrans/s isegerr/s   orsts/s
    08:18:51 AM      0.00      0.00      0.00      0.00      1.00

- active/sec - Number of locally-initiated TCP connections per second (e.g., via connect()).
- passive/sec - Number of remotely-initiated TCP connections per second (e.g., via accept()).
- retrans/sec - Number of TCP retransmits per second. Sign of a network or server issue

9. #### top/htop [resource demand] → %cpu, command

    $ top
    
    Processes: 380 total, 2 running, 378 sleeping, 1970 threads                                                                                                                                            14:21:15
    Load Avg: 1.28, 1.79, 1.73  CPU usage: 3.35% user, 1.67% sys, 94.96% idle  SharedLibs: 202M resident, 55M data, 17M linkedit. MemRegions: 92113 total, 6767M resident, 201M private, 1855M shared.
    PhysMem: 14G used (2425M wired), 1856M unused. VM: 1701G vsize, 1110M framework vsize, 0(0) swapins, 0(0) swapouts. Networks: packets: 107657/96M in, 80853/13M out.
    Disks: 1916703/24G read, 363418/12G written.
    
    PID   COMMAND      %CPU TIME     #TH   #WQ  #PORT MEM    PURG   CMPRS  PGRP PPID STATE    BOOSTS         %CPU_ME %CPU_OTHRS UID  FAULTS    COW     MSGSENT   MSGRECV   SYSBSD    SYSMACH  CSW       PAGEINS
    2851  top          3.6  00:03.25 1/1   0    23    4744K  0B     0B     2851 2404 running  *0[1]          0.00000 0.00000    0    23910+    120     803024+   401447+   92029+    584703+  950+      0
    2801  syncdefaults 0.0  00:00.42 4     2    128+  12M+   0B     0B     2801 1    sleeping  0[5]          0.00000 0.00000    501  7286+     282     1087      527       7992+     3174     3784+     0
    2800  quicklookd   0.0  00:00.14 4     1    87    4516K  28K    0B     2800 1    sleeping  0[0]          0.00000 0.00000    501  4747      257     310       242       2948      834      1484      0
    2789  mdworker     0.0  00:00.19 3     1    54    4068K  0B     0B     2789 1    sleeping *0[1]          0.00000 0.00000    501  6695      223     1243      501       9822      1617     1154      0
    2788  mdworker     0.0  00:00.21 3     1    54    3924K  0B     0B     2788 1    sleeping *0[1]          0.00000 0.00000    501  6692      223     1291      517       10735     1709     1187      0
    2787  ocspd        0.0  00:00.04 2     1    34    1772K  0B     0B     2787 1    sleeping *0[1]          0.00000 0.00000    0    3023      168     883       193       985       2454     400       0

10. #### ps -ef f  → PID, command

process stats

    $ ps
    
      UID   PID  PPID   C STIME   TTY           TIME CMD
        0     1     0   0 Fri10AM ??         5:28.81 /sbin/launchd
        0    42     1   0 Fri10AM ??         0:04.29 /usr/sbin/syslogd

 ps auxe [resource demand]

    $ ps auxe
    
    USER  PID  %CPU  %MEM    VSZ    RSS   TT  STAT  STARTED      TIME   COMMAND
    root  103   0.1  0.0  4350288   2072   ??  Ss   Fri10AM   0:47.83 /usr/sbin/notifyd
    _hidd  99   0.1  0.0  4380568   5024   ??  Ss   Fri10AM   6:01.77 /usr/libexec/hidd

![](http://www.brendangregg.com/Perf/linux_observability_tools.png "Brendan Gregg - linux_observability_tools")

### Other observability commands:

1. strace - system call tracer
2. tcpdump - sniff n/w packets
3. netstat 
    1. -s n/w protocol stats
    2. -i interface stats
    3. -r route table <netstat -rn>
    4. -p process details
4. ip route get <ip> 
5. nicstat - n/w throughput, %util, interface, rKB/s, wKB/s - sar
6. pidstat - process stats → by thread, disk i/o stats
7. swapon - swap device usage
8. lsof - fd usage → current active n/w connections
9. sar
10. ss - socket stats → -mop -i 
11. iotop - block device IO by process 
12. pcstat - page cache 
13. df -h → file system capacity
14. ifconfig -a; ip link  →  n/w interface config

### Profiling tools:

CPU profiling → `perf_events`
pprof

### Anti-methods:

1. street light anti-method
2. drunk man anti-method
3. blame someone else anti-method

### Methods:

1. problem statement method
    1. what makes you think there is a performance problem?
    2. has the system ever performed well?
    3. what has changed? s/w, h/w, load?
    4. latency/run time?
2. workload characterization method
    1. who is causing the load? PID, UID, IP addr
    2. why is the load called? code path, stack trace
    3. what is the load? IO/s, tput throughput, r/w
    4. how is the load changing over time?
3. USE method
    1. for every resource check - utilization, saturation, errors
4. off-cpu analysis
5. cpu profile method
6. RTFM method
7. active benchmarking
8. static performance tuning

### Scenarios:

1. #### Systems is slow
    1. what do you mean by system is slow? what exactly - eg. latency
    2. how do I reproduce the issue
    3. top
    4. iostat
    5. sar -n DEV 1
    6. vmstat
2. #### Application latency is higher
    1. top
    2. vmstat 1
    3. mpstat 1
    4. mpstat -P ALL 1
    5. iostat -x
    6. sar -n DEV 1
3. #### Application is taking forever
    1. vmstat - us, sy
    2. mpstat 1
    3. pidstat 1
    4. iostat -x  talking to disk?
    5. r -n DEV 1 talking to n/w?
    6. vmstat 1 swapping?
    7. strace -p

### Ref:

1. [Netflix Linux performance analysis](https://medium.com/netflix-techblog/linux-performance-analysis-in-60-000-milliseconds-accc10403c55)
2. [Julia Evans](https://jvns.ca/categories/)
3. [Linux performance tools](https://medium.com/netflix-techblog/netflix-at-velocity-2015-linux-performance-tools-51964ddb81cf)
