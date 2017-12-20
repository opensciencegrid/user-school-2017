<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Monday Exercise 1.3: Run Jobs!
==============================

The goal of this exercise is to submit jobs to HTCondor and have them run on the local pool (CHTC). This is a huge step in learning to use an HTC system!

This exercise will take longer than the first two, short ones. It is the essential part of this exercise time. If you are having any problems getting the jobs to run, please ask the instructors! It is very important that you know how to run simple jobs.

Running a Simple Command Using a Submit File
--------------------------------------------

Nearly all of the time, when you want to run an HTCondor job, you first write an HTCondor submit file for it. In this section, you will run the same `hostname` command as in the last exercise, but using a submit file.

Here is a simple submit file for the `hostname` command:

``` file
universe = vanilla
executable = /bin/hostname

output = simple.out
error = simple.err
log = simple.log

request_cpus = 1
request_memory = 1MB
request_disk = 1MB

queue
```

Write those lines of text in a file named `simple.sub`.

**Note:** There is nothing magic about the name of an HTCondor submit file. It can be any filename you want. It's a good practice to always include the `.sub` extension, but it is not required. Ultimately, a submit file is a text file

The lines of the submit file have the following meanings:

|              |                                                                                                                                                                            |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `universe`   | The type of job this is. The default `vanilla` universe is a normal job. Later on, we will discuss other, special universes.                                               |
| `executable` | The name of the program to run (relative to the directory from which you submit).                                                                                          |
| `output`     | The filename where HTCondor will write the standard output from your job.                                                                                                  |
| `error`      | The filename where HTCondor will write the standard error from your job. This particular job is not likely to have any, but it is best to include this line for every job. |
| `log`        | The filename where HTCondor will write information about your job run. Technically not required, it is a **really** good idea to have a log file for every job.            |
| `request_*`  | Tells HTCondor how many `cpus` and how much `memory` and `disk` we want, which is not much, because the 'hostname' executable is pretty simple                             |
| `queue`      | Tells HTCondor to run your job with the settings above.                                                                                                                    |

Note that we are not using the `arguments` lines or `transfer_input_files` because the `hostname` program is all that needs to be transferred from the submit server, and we want to run it without any additional options.

Double-check your submit file, so that it matches the text above. Then, tell HTCondor to run your job:

``` console
%UCL_PROMPT_SHORT% <strong>condor_submit simple.sub</strong>
Submitting job(s).
1 job(s) submitted to cluster <em>NNNN</em>.
```

The actual cluster number will be shown instead of `NNNN`.

If, instead of the text above, there are error messages, read them carefully and then try to correct your submit file.

Notice that `condor_submit` returns back to the shell prompt right away. It does **not** wait for your job to run. Instead, as soon as it has finished submitting your job into the queue, the submit command finishes.

Now, use `condor_q` and `condor_q -nobatch` to watch for your job in the queue. You probably may not even catch the job in the `R` running state, because the `hostname` command runs very quickly. When the job itself is finished, it will no longer be listed in the `condor_q` output.

The output from your job is written to the filename given in the `output` line of your submit file. Thus, after the job finishes, you should be able to see the `hostname` output in `simple.out`, since this information is usually printed to the terminal by the `hostname` program, and not to a special file of it's own.

``` console
%UCL_PROMPT_SHORT% <strong>cat simple.out</strong>
e171.chtc.wisc.edu
```

The `simple.err` file should be empty, unless there were issues running the `hostname` executable after it was transferred to the slot. The `simple.log` is more complex and will be the focus of a later exercise.

### Running a Job With Arguments

Very often, when you run a command on the command line, it includes arguments after the command name itself:

``` console
%UCL_PROMPT_SHORT% <strong>cat <em>simple.out</em></strong>
%UCL_PROMPT_SHORT% <strong>sleep <em>60</em></strong>
%UCL_PROMPT_SHORT% <strong>dc <em>-e '6 7 * p'</em></strong>
```

In an HTCondor submit file, the command (or "program") name itself goes in the `executable` statement and **all remaining arguments** go into an `arguments` statement. For example, if the full command is:

``` console
%UCL_PROMPT_SHORT% <strong>sleep <em>60</em></strong>
```

Then in the submit file, we put:

``` file
executable = /bin/sleep
arguments = "60"
```

**Note:** Put the entire list of arguments inside one pair of double-quotes.

For the command-line command:

``` console
%UCL_PROMPT_SHORT% <strong>dc <em>-e '6 7 * p'</em></strong>
```

Then in the submit file, we put:

``` file
executable = /usr/bin/dc
arguments = "-e '6 7 * p'"
```

Let’s try a job submission with arguments. We will use the `sleep` command shown above, which simply does nothing for the specified number of seconds, then exits normally. It is convenient for simulating a job that takes a while to run.

Create a new submit file (you name it this time) and save the following text in it.

``` file
universe = vanilla
executable = <em>/bin/sleep</em>
<em>arguments = "60"</em>

output = sleep.out
error = sleep.err
log = sleep.log

request_cpus = 1
request_memory = 1MB
request_disk = 1MB

queue
```

Except for changing a few filenames, this submit file is nearly identical to the last one. But, see the extra `arguments` line?

Submit this new job. Again, watch for it to run using `condor_q` and `condor_q -nobatch=; check once every 15 seconds or so. Once the job starts running, it will take about 1 minute to run (because of the =sleep` command, right?), so you should be able to see it running for a bit. When the job finishes, it will disappear from the queue, but there will be no output in the output or error files, because `sleep` does not produce any output.

Running a Script Job From the Submit Directory
----------------------------------------------

So far, we have been running programs (executables) that come with the standard Linux system. But you are not limited to standard programs. In this example, you will write a simple shell script (command line) executable in the submit directory, then write a submit file to run it.

1.  Put the following contents into a file named `test-script.sh`:\\ <pre class="file">

\#/bin/sh

echo 'Date: ' \`date\` echo 'Host: ' \`hostname\` echo 'System: ' \`uname -spo\` echo "Program: $0" echo "Args: $\*" </pre>

1.  Make the file itself executable:\\ <pre class="screen">

<span class="twiki-macro UCL_PROMPT_SHORT"></span> **chmod +x test-script.sh** </pre>

1.  Test your script from the command line:\\ <pre class="screen">

<span class="twiki-macro UCL_PROMPT_SHORT"></span> **./test-script.sh hello 42** Date: Mon Jul 17 10:02:20 CDT 2017 Host: learn.chtc.wisc.edu System: Linux x86\_64 GNU/Linux Program: ./test-script.sh Args: hello 42 </pre>\\ <p>This step is **really** important! If you cannot run your executable from the command-line, HTCondor probably cannot run it on another machine, either. And debugging simple problems like this one is surprisingly difficult. So, if possible, test your `executable` and `arguments` as a command at the command-line first.</p>

1.  Write the submit file (this should be getting easier by now):\\ <pre class="file">

universe = vanilla executable = *test-script.sh* arguments = "foo bar baz" output = script.out error = script.err log = script.log request\_cpus = 1 request\_memory = 1 request\_disk = 1 queue </pre>\\ <p>**Note:** As this example shows, blank lines and spaces around the = sign do not matter to HTCondor. Use whitespace to make things clear to **you**. What format do you prefer to read?</p>

1.  Submit the job, wait for it to finish, check the output. (Are you surprised by the `Program:` line in the output? Why is it like that? Google for it, or ask an instructor if you are curious, although the answer is not that exciting.)

In this example, the `executable` that was named in the submit file did **not** start with a `/`, so the location of the file is relative to the submit directory itself. In other words, in this format the executable must be in the same directory as the submit file.

### Extra Challenge 1

Below is a simple Python script that does something similar to the shell script above. Run this Python script using HTCondor.

``` file
#!/usr/bin/env python

"""Extra Challenge for OSG User School 2015
Monday, 27 July 2015
Written by Tim Cartwright
Submitted to CHTC by #YOUR_NAME#
"""

import getpass
import os
import platform
import socket
import sys
import time

arguments = None
if len(sys.argv) > 1:
    arguments = '"' + ' '.join(sys.argv[1:]) + '"'

print >> sys.stderr, __doc__
print 'Time    :', time.strftime('%Y-%m-%d (%a) %H:%M:%S %Z')
print 'Host    :', getpass.getuser(), '@', socket.gethostname()
uname = platform.uname()
print "System  :", uname[0], uname[2], uname[4]
print "Version :", platform.python_version()
print "Program :", sys.executable
print 'Script  :', os.path.abspath(__file__)
print 'Args    :', arguments
```

**Note:** For the Python script, above, you'll want to increase the memory request. We will talk about tuning resource requests, later, but you should be able to set the following to have the job run:

``` file
request_memory = 64 MB
```

### Extra Challenge 1

Do you have any software of your own that

-   Is written in a common scripting language (e.g., shell, Perl, Python, Ruby) or is it a compiled binary?
-   Does not require other files (e.g., input files, libraries)
-   Runs on common Linux systems (our machines are mostly compatible with Red Hat Enterprise Linux 6)?

If so, try to run your program with HTCondor now, as you'll only need the same/similar submit file lines that we've covered so far.

