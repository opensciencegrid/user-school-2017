<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Monday Exercise 1.2: Experiment With Basic HTCondor Commands
============================================================

The goal of this exercise is to use the basic informational HTCondor commands, `condor_status` and `condor_q`. They will be useful for monitoring your jobs and available slots throughout the day.

This exercise should take only a few minutes.

Viewing Slots
-------------

As discussed in the lecture, the `condor_status` command is used to view the current state of slots in an HTCondor pool.

At its most basic, the command is very simple:

``` console
%UCL_PROMPT_SHORT% <strong>condor_status</strong>
```

This command, running on our (CHTC) pool, will produce a lot of output; there is one line per slot, and we typically have over 10,000 slots. TIP: You can widen your terminal window, which may help you to see all details of the output better.

Here is some example output (what you see will be longer):

``` console
slot1_31@e437.chtc.wisc.edu        LINUX      X86_64 Unclaimed Idle      0.000 8053  0+01:14:34
slot1_32@e437.chtc.wisc.edu        LINUX      X86_64 Unclaimed Idle      0.000 8053  0+03:57:00
slot1_33@e437.chtc.wisc.edu        LINUX      X86_64 Unclaimed Idle      0.000 8053  1+00:05:17
slot1@e438.chtc.wisc.edu           LINUX      X86_64 Owner     Idle      0.300  250  7+03:22:21
<em>slot1_1@e438.chtc.wisc.edu         LINUX      X86_64 Claimed   Busy      0.930 1024  0+02:42:08</em>
slot1_2@e438.chtc.wisc.edu         LINUX      X86_64 Claimed   Busy      3.530 1024  0+02:40:24
```

This output consists of 8 columns:

| Col        | Example                      | Meaning                                                                                                                 |
|:-----------|:-----------------------------|:------------------------------------------------------------------------------------------------------------------------|
| Name       | `slot1_1@e438.chtc.wisc.edu` | Slot name and hostname                                                                                                  |
| OpSys      | `LINUX`                      | Operating system                                                                                                        |
| Arch       | `X86_64`                     | Machine architecture (e.g., Intel 64 bit)                                                                               |
| State      | `Claimed`                    | State of the slot (`Unclaimed` is available, `Owner` is being used by the machine owner, `Claimed` is matched to a job) |
| Activity   | `Busy`                       | Is there activity on the slot?                                                                                          |
| LoadAv     | `0.930`                      | Load average, a measure of CPU activity on the slot                                                                     |
| Mem        | `1024`                       | Memory available to the slot, in MB                                                                                     |
| ActvtyTime | `0+02:42:08`                 | Amount of time spent in current activity (days + hours:minutes:seconds)                                                 |

At the end of the slot listing, there is a summary. Here is an example:

``` console
                     Machines Owner Claimed Unclaimed Matched Preempting  Drain

        X86_64/LINUX    10831     0   10194       631       0          0      6
      X86_64/WINDOWS        2     2       0         0       0          0      0

               Total    10833     2   10194       631       0          0      6
```

There is one row of summary for each machine architecture/operating system combination. The columns are the different states that a slot can be in. The final row gives a summary of slot states for the whole pool.

**Questions:**

-   For the sample above, how many 64-bit Linux slots are available? (Hint: Unclaimed = available.)
-   For the sample above, how many slots total are being used by their owner?

Now, run `condor_status` yourself and try these:

-   How many 64-bit Linux slots are in the pool right now? How does that compare to the sample above?
-   How many of those 64-bit Linux slots are available now?

### Viewing Whole Machines, Only

Also try out the `-compact` for a slightly different view of whole machines, without the individual slots shown.

``` console
%UCL_PROMPT_SHORT% <strong>condor_status -compact</strong>
```

How has the column information changed? (Below is an example of the top of the output.)

``` console
Machine                      Platform     Slots Cpus Gpus  TotalGb FreCpu  FreeGb  CpuLoad ST Jobs/Min MaxSlotGb

aci-005.chtc.wisc.edu        x64/SL6         16   16         58.86      0     1.29    1.00 Cb     0.00     12.00
aci-017.chtc.wisc.edu        x64/SL6         14   16         58.86      2     2.86    0.88 **     0.00      4.00
aci-056.chtc.wisc.edu        x64/SL6          7   16         58.86      9     0.86    0.42 **     0.05     12.00
aci-057.chtc.wisc.edu        x64/SL6          6   16         58.82     10     2.82    0.39 **     0.00     12.00
aci-058.chtc.wisc.edu        x64/SL6          6   16         58.86     10     2.86    0.38 **     0.00     12.00
```

Viewing Jobs
------------

The `condor_q` command lists jobs that are on this submit machine and that are running or waiting to run. The `_q` part of the name is meant to suggest the word “queue”, or list of jobs *waiting* to finish.

### Viewing Your Own Jobs

The simplest form of the command lists only your jobs:

``` console
%UCL_PROMPT_SHORT% <strong>condor_q</strong>
```

The main part of the output (which will be empty, because you haven't submitted jobs yet) shows one job ID per line:

``` console
-- Schedd: learn.chtc.wisc.edu : <128.104.100.43:9618?... @ 07/16/17 09:02:31
OWNER  BATCH_NAME            SUBMITTED   DONE   RUN    IDLE  TOTAL JOB_IDS
aapohl CMD: run_ffmpeg.sh   7/17 09:58      _      _      1      1 18801.0               
```

This output consists of 8 (or 9) columns:

| Col         | Example         | Meaning                                                                                                                        |
|:------------|:----------------|:-------------------------------------------------------------------------------------------------------------------------------|
| OWNER       | `aapohl`        | The user ID of the user who submitted the job                                                                                  |
| BATCH\_NAME | `run_ffmpeg.sh` | The executable or the "jobbatchname" specified within submit file(s)                                                           |
| SUBMITTED   | `7/17 09:58`    | The date and time when the job was submitted                                                                                   |
| DONE        | `_`             | Number of jobs in this batch that have completed                                                                               |
| RUN         | `_`             | Number of jobs in this batch that are currently running                                                                        |
| IDLE        | `1`             | Number of jobs in this batch that are idle, waiting for a match                                                                |
| HOLD        | `_`             | Column will show up if there are jobs on "hold" because something about the submission/setup needs to be corrected by the user |
| TOTAL       | `1`             | Total number of jobs in this batch                                                                                             |
| JOB\_IDS    | `18801.0`       | Job ID or range of Job IDs in this batch                                                                                       |

At the end of the job listing, there is a summary. Here is a sample:

``` console
1 jobs; 0 completed, 0 removed, 1 idle, 0 running, 0 held, 0 suspended
```

It shows total counts of jobs in the different possible states.

**Questions:**

-   For the sample above, when was the job submitted?
-   For the sample above, was the job running or not yet? How can you tell?

### Viewing Everyone’s Jobs

By default, the `condor_q` command shows **your** jobs only. To see everyone’s jobs that are queued on the machine, add the `-all` option:

``` console
%UCL_PROMPT_SHORT% <strong>condor_q -all</strong>
```

Run that command now and use its output to answer the following questions:

-   How many jobs are queued in total (i.e., running or waiting to run)?
-   How many jobs from this submit machine are running right now?

### Viewing Jobs without the Default "batch" Mode

The `condor_q` output, by default, groups "batches" of jobs together (if they were submitted with the same submit file as part of the same Cluster, and even for separately submitted Clusters that use the same exact executable). To see more information for EVERY job on a separate line of output, use `condor_q -nobatch` (or, to see everyone's jobs `condor_q -all -nobatch`).

``` console
%UCL_PROMPT_SHORT% <strong>condor_q -all -nobatch</strong>
```

How has the column information changed? (Below is an example of the top of the output.)

``` console
-- Schedd: learn.chtc.wisc.edu : <128.104.100.43:9618?... @ 07/17/17 09:58:44
 ID       OWNER            SUBMITTED     RUN_TIME ST PRI SIZE   CMD
18203.0   s16_alirezakho  7/27 09:51   0+00:00:00 I  0      0.7 pascal
18204.0   s16_alirezakho  7/27 09:51   0+00:00:00 I  0      0.7 pascal
18801.0   aapohl          7/28 16:58   0+00:00:00 I  0      0.0 run_ffmpeg.sh
18997.0   s16_martincum   7/29 10:59   0+00:00:32 I  0    733.0 runR.pl 1_0 run_perm.R 1 0 10
19027.5   s16_martincum   7/29 11:06   0+00:09:20 I  0   2198.0 runR.pl 1_5 run_perm.R 1 5 1000
```

The `-nobatch` output shows a line for every job and consists of 8 columns:

| Col       | Example         | Meaning                                                                        |
|:----------|:----------------|:-------------------------------------------------------------------------------|
| ID        | `18801.0`       | Job ID, which is the `cluster`, a dot character (`.`), and the `process`       |
| OWNER     | `aapohl`        | The user ID of the user who submitted the job                                  |
| SUBMITTED | `7/17 09:58`    | The date and time when the job was submitted                                   |
| RUN\_TIME | `0+00:00:00`    | Total time spent running so far (days + hours:minutes:seconds)                 |
| ST        | `I`             | Status of job: `I` is Idle (waiting to run), `R` is Running, `H` is Held, etc. |
| PRI       | `0`             | Job priority (see next lecture)                                                |
| SIZE      | `0.0`           | Current run-time memory usage, in MB                                           |
| CMD       | `run_ffmpeg.sh` | The executable command (with arguments) to be run                              |

In future exercises, you'll want to switch between `condor_q` and `condor_q -nobatch` to see different types of information about YOUR jobs.

Extra Information
-----------------

Both `condor_status` and `condor_q` have many command-line options, some of which significantly change their output. You will explore a few of the most useful options in the next lecture and set of exercises, but if you want to experiment now, go ahead! There are a few ways to learn more about the commands:

-   Use the (brief) built-in help for the commands, e.g.: `condor_q -h`
-   Read the installed man(ual) pages for the commands, e.g.: `man condor_q`
-   Find the command in [the online manual](http://research.cs.wisc.edu/htcondor/manual/); **note:** the text online is the same as the `man` text, only formatted for the web


