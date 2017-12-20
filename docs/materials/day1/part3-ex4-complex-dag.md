a<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Monday Exercise 3.4: A More Complex DAG
=======================================

The objective of this exercise is to run a real set of jobs with DAGMan.

Make Your Job Submission Files
------------------------------

We'll run our `goatbrot` example. If you didn't read about it yet, [please do so now](UserSchool16Mon32Mandelbrot). We are going to make a DAG with four simultaneous jobs (`goatbrot`) and one final node to stitch them together (`montage`). This means we have five jobs. We're going to run `goatbrot` with more iterations (100,000) so each job will take longer to run.

You can create your five jobs. The goatbrot jobs are very similar to each other, but they have slightly different parameters and output files.

### goatbrot1.sub

``` file
executable              = /usr/local/bin/goatbrot
arguments               = -i 100000 -c -0.75,0.75 -w 1.5 -s 500,500 -o tile_0_0.ppm
log                     = goatbrot.log
output                  = goatbrot.out.0.0
error                   = goatbrot.err.0.0
request_memory = 1GB
request_disk       = 1GB
request_cpus      = 1
queue
```

### goatbrot2.sub

``` file
executable              = /usr/local/bin/goatbrot
arguments               = -i 100000 -c 0.75,0.75 -w 1.5 -s 500,500 -o tile_0_1.ppm
log                     = goatbrot.log
output                  = goatbrot.out.0.1
error                   = goatbrot.err.0.1
request_memory = 1GB
request_disk       = 1GB
request_cpus      = 1
queue
```

### goatbrot3.sub

``` file
executable              = /usr/local/bin/goatbrot
arguments               = -i 100000 -c -0.75,-0.75 -w 1.5 -s 500,500 -o tile_1_0.ppm
log                     = goatbrot.log
output                  = goatbrot.out.1.0
error                   = goatbrot.err.1.0
request_memory = 1GB
request_disk       = 1GB
request_cpus      = 1
queue
```

### goatbrot4.sub

``` file
executable              = /usr/local/bin/goatbrot
arguments               = -i 100000 -c 0.75,-0.75 -w 1.5 -s 500,500 -o tile_1_1.ppm
log                     = goatbrot.log
output                  = goatbrot.out.1.1
error                   = goatbrot.err.1.1
request_memory = 1GB
request_disk       = 1GB
request_cpus      = 1
queue
```

### montage.sub

You should notice that the `transfer_input_files` statement refers to the files created by the other jobs.

``` file
executable              = /usr/bin/montage
arguments               = tile_0_0.ppm tile_0_1.ppm tile_1_0.ppm tile_1_1.ppm -mode Concatenate -tile 2x2 mandel-from-dag.jpg
transfer_input_files    = tile_0_0.ppm,tile_0_1.ppm,tile_1_0.ppm,tile_1_1.ppm
output                  = montage.out
error                   = montage.err
log                     = montage.log
request_memory = 1GB
request_disk       = 1GB
request_cpus      = 1
queue
```

Make your DAG
-------------

In a file called `goatbrot.dag`, you have your DAG specification:

``` file
JOB g1 goatbrot1.sub
JOB g2 goatbrot2.sub
JOB g3 goatbrot3.sub
JOB g4 goatbrot4.sub
JOB montage montage.sub
PARENT g1 g2 g3 g4 CHILD montage
```

Ask yourself: do you know how we ensure that all the `goatbrot` commands can run simultaneously and all of them will complete before we run the montage job?

Running the DAG
---------------

Submit your DAG:

``` console
%UCL_PROMPT_SHORT% <strong>condor_submit_dag goatbrot.dag</strong>
-----------------------------------------------------------------------
File for submitting this DAG to Condor           : goatbrot.dag.condor.sub
Log of DAGMan debugging messages                 : goatbrot.dag.dagman.out
Log of Condor library output                     : goatbrot.dag.lib.out
Log of Condor library error messages             : goatbrot.dag.lib.err
Log of the life of condor_dagman itself          : goatbrot.dag.dagman.log

Submitting job(s).
1 job(s) submitted to cluster 71.

-----------------------------------------------------------------------
```

Watch Your DAG
--------------

Let’s follow the progress of the whole DAG:

1.  Use the `watch` command to run `condor_q -nobatch` every 10 seconds: <pre class="screen">

<span class="twiki-macro UCL_PROMPT_SHORT"></span> **watch -n 10 condor\_q -nobatch**

%RED%Here we see DAGMan running:<span class="twiki-macro ENDCOLOR"></span> -- Submitter: learn.chtc.wisc.edu : <128.104.100.55:9618?sock=28867\_10e4\_2> : learn.chtc.wisc.edu ID OWNER SUBMITTED RUN\_TIME ST PRI SIZE CMD 71.0 roy 6/22 17:39 0+00:00:03 R 0 0.3 condor\_dagman

1 jobs; 0 completed, 0 removed, 0 idle, 1 running, 0 held, 0 suspended

%RED%DAGMan has submitted the goatbrot jobs, but they haven't started running yet:<span class="twiki-macro ENDCOLOR"></span> -- Submitter: learn.chtc.wisc.edu : <128.104.100.55:9618?sock=28867\_10e4\_2> : learn.chtc.wisc.edu ID OWNER SUBMITTED RUN\_TIME ST PRI SIZE CMD 71.0 roy 6/22 17:39 0+00:00:17 R 0 0.3 condor\_dagman 72.0 roy 6/22 17:39 0+00:00:00 I 0 0.0 goatbrot -i 100000 73.0 roy 6/22 17:39 0+00:00:00 I 0 0.0 goatbrot -i 100000 74.0 roy 6/22 17:39 0+00:00:00 I 0 0.0 goatbrot -i 100000 75.0 roy 6/22 17:39 0+00:00:00 I 0 0.0 goatbrot -i 100000

5 jobs; 0 completed, 0 removed, 4 idle, 1 running, 0 held, 0 suspended

%RED%They're running%ENDCOLOR% -- Submitter: learn.chtc.wisc.edu : <128.104.100.55:9618?sock=28867\_10e4\_2> : learn.chtc.wisc.edu ID OWNER SUBMITTED RUN\_TIME ST PRI SIZE CMD 71.0 roy 6/22 17:39 0+00:07:15 R 0 0.3 condor\_dagman 72.0 roy 6/22 17:39 0+00:00:03 R 0 0.0 goatbrot -i 100000 73.0 roy 6/22 17:39 0+00:00:03 R 0 0.0 goatbrot -i 100000 74.0 roy 6/22 17:39 0+00:00:03 R 0 0.0 goatbrot -i 100000 75.0 roy 6/22 17:39 0+00:00:03 R 0 0.0 goatbrot -i 100000

5 jobs; 0 completed, 0 removed, 0 idle, 5 running, 0 held, 0 suspended

%RED%Two of the jobs have finished, while the others are still running:<span class="twiki-macro ENDCOLOR"></span> -- Submitter: learn.chtc.wisc.edu : <128.104.100.55:9618?sock=28867\_10e4\_2> : learn.chtc.wisc.edu ID OWNER SUBMITTED RUN\_TIME ST PRI SIZE CMD 71.0 roy 6/22 17:39 0+00:07:51 R 0 0.3 condor\_dagman 72.0 roy 6/22 17:39 0+00:00:39 R 0 0.0 goatbrot -i 100000 74.0 roy 6/22 17:39 0+00:00:39 R 0 0.0 goatbrot -i 100000

3 jobs; 0 completed, 0 removed, 0 idle, 3 running, 0 held, 0 suspended

%RED%They finished, but DAGMan hasn't noticed yet. It only checks periodically:<span class="twiki-macro ENDCOLOR"></span> -- Submitter: learn.chtc.wisc.edu : <128.104.100.55:9618?sock=28867\_10e4\_2> : learn.chtc.wisc.edu ID OWNER SUBMITTED RUN\_TIME ST PRI SIZE CMD 71.0 roy 6/22 17:39 0+00:08:46 R 0 0.3 condor\_dagman

1 jobs; 0 completed, 0 removed, 0 idle, 1 running, 0 held, 0 suspended

%RED%DAGMan submitted and ran the montage job. It ran so fast I didn't capture it running. DAGMan will finish up soon <span class="twiki-macro ENDCOLOR"></span> -- Submitter: learn.chtc.wisc.edu : <128.104.100.55:9618?sock=28867\_10e4\_2> : learn.chtc.wisc.edu ID OWNER SUBMITTED RUN\_TIME ST PRI SIZE CMD 71.0 roy 6/22 17:39 0+00:08:55 R 0 0.3 condor\_dagman

1 jobs; 0 completed, 0 removed, 0 idle, 1 running, 0 held, 0 suspended

%RED%Now it's all done:<span class="twiki-macro ENDCOLOR"></span> -- Submitter: learn.chtc.wisc.edu : <128.104.100.55:9618?sock=28867\_10e4\_2> : learn.chtc.wisc.edu ID OWNER SUBMITTED RUN\_TIME ST PRI SIZE CMD

0 jobs; 0 completed, 0 removed, 0 idle, 0 running, 0 held, 0 suspended </pre>

1.  Examine your results. For some reason, goatbrot prints everything to stderr, not stdout. <pre class="screen">

<span class="twiki-macro UCL_PROMPT_SHORT"></span> **cat goatbrot.err.0.0** Complex image: Center: -0.75 + 0.75i Width: 1.5 Height: 1.5 Upper Left: -1.5 + 1.5i Lower Right: 0 + 0i

Output image: Filename: tile\_0\_0.ppm Width, Height: 500, 500 Theme: beej Antialiased: no

Mandelbrot: Max Iterations: 100000 Continuous: no

Goatbrot: Multithreading: not supported in this build

Completed: 100.0% </pre>

1.  <p>Examine your log files (`goatbrot.log` and `montage.log`) and DAGMan output file (`goatbrot.dag.dagman.out`). Do they look as you expect? Can you see the progress of the DAG in the DAGMan output file?</p>
2.  <p>As you did earlier, copy the resulting `mandel-from-dag.jpg` to your `public_html` directory, then access it from your web browser. Does the image look correct?</p>
3.  <p>Clean up your results. Be careful about deleting the `goatbrot.dag.*` files, you do not want to delete the `goatbrot.dag` file, just `goatbrot.dag.*`.</p> <pre class="screen">

<span class="twiki-macro UCL_PROMPT_SHORT"></span> **rm goatbrot.dag.\*** <span class="twiki-macro UCL_PROMPT_SHORT"></span> **rm goatbrot.out.\*** <span class="twiki-macro UCL_PROMPT_SHORT"></span> **rm goatbrot.err.\*** </pre>

On Your Own
-----------

-   Re-run your DAG. When jobs are running, try `condor_q -nobatch -dag`. What does it do differently?
-   Challenge, if you have time: Make a bigger DAG by making more tiles in the same area.


