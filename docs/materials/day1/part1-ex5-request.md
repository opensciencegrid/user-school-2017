<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Monday Exercise 1.5: Declare Resource Needs
===========================================

The goal of this exercise is to demonstrate how to test and tune the `request_<i>X</i>` statements in a submit file for when you don't know what resources your job needs.

There are three special resource request statements that you can use (optionally) in an HTCondor submit file:

\* `request_cpus` for the number of CPUs your job will use (most softwares will take an argument to control this number, and it's usually otherwise "1") \* `request_memory` for the maximum amount of run-time memory your job may use \* `request_disk` for the maximum amount of disk space your job may use (including the executable and all other data that may show up during the job)

HTCondor defaults to certain reasonable values for these request settings, so you do not need to use them to get *small* jobs to run. However, on some HTCondor pools, if your job goes over the request values, it may be removed from the execute machine and either held (awaiting action on your part) or rerun later. So it can be a disadvantage to you if you do not declare your resource needs or if you underestimate them. If you overestimate them, your jobs will match to fewer slots (and with a longer average wait time) *and* you'll be hogging up resources that you don't need, but that could be used for the jobs of other users. In the long run, it works better for all users of the pool if you declare what you really need.

But how do you know what to request? In particular, we are concerned with memory and disk here; requesting multiple CPUs and using them is covered a bit in later school materials, but true HTC splits work up into jobs that each use as few CPU cores as possible (one CPU core is always best to have the most jobs running soonest).

Determining Resource Needs Before Running Any Jobs
--------------------------------------------------

It can be very difficult to determine the memory needs of your running program. Typically, the memory size of a job changes over time, making the task even trickier. If you have knowledge ahead of time about your job’s maximum memory needs, use that, or a maybe a number that's just a bit higher, to be safe. If not, then it's best to run your program in a single test job, first, and let HTCondor tell you in the log file (or in the `condor_q -nobatch` output, if you're able to watch it), which is covered in the next section on "Determining Resource Needs by Running Test Jobs".

### Running a Job Locally On our shared submit server `learn.chtc.wisc.edu`, you should not run computationally-intensive work because it can use resources needed by HTCondor to manage the queue for all uses. However, you may have access to other computers where you can observe the memory usage of a program. The downside is that you'll have to watch a program run for essentially the entire time, to make sure you catch the maximum memory usage.

For Memory: On Mac and Windows, for example, the "Activity Monitor" and "Task Manager" applications may be useful. On a Mac or Linux system, you can use the `ps` command or the `top` command in the Terminal to watch a running program and see (roughly) how much memory it is using. Full coverage of these tools is beyond the scope of this exercise, but here are two quick examples:

Using `ps`:

``` console
%UCL_PROMPT_SHORT% <strong>ps ux</strong>
USER       PID %CPU %MEM    VSZ   <em>RSS</em> TTY      STAT START   TIME COMMAND
cat      24342  0.0  0.0  90224  <em>1864</em> ?        S    13:39   0:00 sshd: cat@pts/0  
cat      24343  0.0  0.0  66096  <em>1580</em> pts/0    Ss   13:39   0:00 -bash
cat      25864  0.0  0.0  65624   <em>996</em> pts/0    R+   13:52   0:00 ps ux
cat      30052  0.0  0.0  90720  <em>2456</em> ?        S    Jun22   0:00 sshd: cat@pts/2  
cat      30053  0.0  0.0  66096  <em>1624</em> pts/2    Ss+  Jun22   0:00 -bash
```

The Resident Set Size (`RSS`) column, highlighted above, gives a rough indication of the memory usage (in KB) of each running process. If your program runs long enough, you can run this command several times and note the greatest value.

Using `top`:

``` console
%UCL_PROMPT_SHORT% <strong>top -u <em>userid</em></strong>
top - 13:55:31 up 11 days, 20:59,  5 users,  load average: 0.12, 0.12, 0.09
Tasks: 198 total,   1 running, 197 sleeping,   0 stopped,   0 zombie
Cpu(s):  1.2%us,  0.1%sy,  0.0%ni, 98.5%id,  0.2%wa,  0.0%hi,  0.1%si,  0.0%st
Mem:   4001440k total,  3558028k used,   443412k free,   258568k buffers
Swap:  4194296k total,      148k used,  4194148k free,  2960760k cached

  PID USER      PR  NI  VIRT  <em>RES</em>  SHR S %CPU %MEM    TIME+  COMMAND
24342 cat       15   0 90224 <em>1864</em> 1096 S  0.0  0.0   0:00.26 sshd
24343 cat       15   0 66096 <em>1580</em> 1232 S  0.0  0.0   0:00.07 bash
25927 cat       15   0 12760 <em>1196</em>  836 R  0.0  0.0   0:00.01 top
30052 cat       16   0 90720 <em>2456</em> 1112 S  0.0  0.1   0:00.69 sshd
30053 cat       18   0 66096 <em>1624</em> 1236 S  0.0  0.0   0:00.37 bash
```

The `top` command (shown here with an option to limit the output to a single user ID) also shows information about running processes, but updates periodically by itself. Type the letter `q` to quit the interactive display. Again, the highlighted `RES` column shows an approximation of memory usage.

For Disk: Determining disk needs may be a bit simpler, because you can check on the size of files that a program is using while it runs. However, it is important to count all files that HTCondor counts to get an accurate size. HTCondor counts **everything** in your job sandbox toward your job’s disk usage:

-   The executable itself
-   All "input" files (anything else that gets transferred TO the job, even if you don't think of it as "input")
-   All files created during the job (broadly defined as "output"), including the captured standard output and error files that you list in the submit file.
-   All temporary files created in the sandbox, even if they get deleted by the executable before it's done.

If you can run your program within a single directory on a local computer (not on the submit server), you should be able to view files and their sizes with the `ls` command.

Determining Resource Needs By Running Test Jobs (BEST)
------------------------------------------------------

Despite the techniques mentioned above, by far the easiest approach to measuring your job’s resource needs is to run one or a small number of sample jobs and have HTCondor itself tell you about the resources used during the runs.

For example, here is a strange Python script that does not do anything useful, but consumes some real resources while running:

``` file
#!/usr/bin/env python
import time
import os
size = 1000000
numbers = []
for i in xrange(size): numbers.append(str(i))
tempfile = open('temp', 'w')
tempfile.write(' '.join(numbers))
tempfile.close()
time.sleep(60)
os.remove('temp')
```

Without trying to figure out what this code does or how many resources it uses, just create a submit file for it, and run it once with HTCondor, starting with somewhat high memory requests ("1GB" for memory and disk is a good starting point, unless you think the job will use far more, and will still match quickly). When it is done, examine the log file. In particular, we care about these lines:

``` file
    Partitionable Resources :    <em>Usage</em>  Request Allocated
       Cpus                 :                 1         1
       Disk (<em>KB</em>)            :     <em>6739</em>  1048576   8022934
       Memory (<em>MB</em>)          :        <em>3</em>     1024      1024
```

So, now we know that the job used 6,739 KB of disk (= about 6.5 MB) and 3 MB of memory!

This is a great technique for determining the real resource needs of your job. If you think resource needs vary from run to run, submit a few sample jobs and look at all the results. And it never hurts to round up your resource requests a little, just in case your job occasionally uses more resources.

Setting Resource Requirements
-----------------------------

Once you know your job’s resource requirements, it is easy to declare them in your submit file. For example, taking our results above as an example, we might slightly increase our requests above what was used, just to be safe:

``` file
request_memory = 4MB  <span style="font-style: italic; color: blue;"># rounded up from 3 MB</span>
request_disk = 7MB   <span style="font-style: italic; color: blue;"># rounded up from 6.5 MB</span>
```

Pay close attention to units:

-   Without explicit units, `request_memory` is in MB (megabytes)
-   Without explicit units, `request_disk` is in KB (kilobytes)
-   Allowable units are `KB` (kilobytes), `MB` (megabytes), `GB` (gigabytes), and `TB` (terabytes)

HTCondor translates these requirements into expressions that become part of the `requirements` expression. However, do not put your CPU, memory, and disk requirements directly into the `requirements` expression; use the `request_<i>XXX</i>` statements instead.

Add these requirements to your submit file for the Python script, rerun the job, and confirm in the log file that your requests were used.


