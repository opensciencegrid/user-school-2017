<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Wednesday Exercise 1.6: Matlab
==============================

The goal of this exercise is to compile Matlab code and run it. This exercise will draw on the idea of writing a wrapper script to install and run code, first introduced in [Exercise 1.3](/user-school/2017/materials/day3/part1-ex3-wrapper.md) and should take 25-30 minutes.

Background
----------

Matlab is licensed; however, unlike most licensed software, it has the ability to be compiled and the compiled code can be run without a license. We will be compiling Matlab `.m` files into a binary file and running that binary using a set of files called the Matlab runtime.

Matlab Code
-----------

1.  Log in to the CHTC submit server (`learn.chtc.wisc.edu`).
2.  Create a directory for this exercise and `cd` into it . 2. Copy the following code into a file called `matrix.m` \\

\\

``` file
A = randi(100,4,4)
b = randi(100,4,1);
x = A\b
save results.txt x -ascii
```

\\

Compiling Matlab Code
---------------------

The first step in making Matlab portable is compiling our Matlab script. To compile this code, we need to access the machines with the Matlab compiler installed. For this exercise, we will use the compilers installed on special CHTC build machines. In the CHTC pool, you can't use `ssh` to directly connect to these machines. Instead, you must submit an interactive job (like in [Exercise 1.4](UserSchool16Wed14package)) that specifically requests these build machines.

1.   Create a file called `compile.submit` with the lines below: \\

\\

``` file
universe = vanilla

output = compile.out
error = compile.err
log = compile.log

should_transfer_files = YES
when_to_transfer_output = ON_EXIT
transfer_input_files = matrix.m

+IsBuildJob = true
requirements = (OpSysMajorVer == 6) && ( IsBuildSlot == true )
request_memory = 1GB
request_disk = 100MB 

queue
```

\\

1.  You can initiate the interactive job by using `condor_submit` 's `-i` option. Enter the following command: \\

\\

``` console
%UCL_PROMPT_SHORT% <strong>condor_submit -i compile.submit</strong>
```

\\ Make sure you've submitted this command from `learn.chtc.wisc.edu`! Once the job starts, continue with the following instructions.

1.  Since you are a guest user on our system, you will need to set your `HOME` directory by running this command: \\

``` console
%UCL_PROMPT_SHORT% <strong>export HOME=$PWD</strong> 
```

\\

1.  Compile your Matlab code with version 2015b. \\

``` console
%UCL_PROMPT_SHORT% <strong>/usr/local/MATLAB/R2015b/bin/mcc -m -R -singleCompThread -R -nodisplay -R -nojvm matrix.m</strong> 
```

\\ The extra arguments to the `mcc` command are very important here. Matlab, by default, will run on as many CPUs as it can find. This can be a big problem \\ when running on someone else's computers, because your Matlab code might interfere with what the owner wants. The `-singleCompThread` option \\ compiles the code to run on a single CPU, avoiding this problem. In addition, the `-nodisplay` and `-nojvm` options turn off the display (which won't exist \\ where the code runs). \\

1.  To exit the interactive session, type `exit`
2.  Now that you're back on the submit server, look at the files that were created by the Matlab compiler. Which one is the compiled binary?

Matlab Runtime
--------------

The newly compiled binary will require the 2015b Matlab runtime to run. You can download the runtime from the Mathworks website and build it yourself, but to save time, for this exercise you can download the pre-built runtimes hosted by CHTC.

1.  Download the 2015b Matlab runtime hosted by CHTC: \\

``` console
%UCL_PROMPT_SHORT% <strong>wget http://proxy.chtc.wisc.edu/SQUID/r2015b.tar.gz </strong>
```

\\

Wrapper Script
--------------

Like the [OpenBUGS](/user-school/2017/materials/day3/part1-ex4-prepackaged.md) example from earlier this morning, we will need a wrapper script to open the Matlab runtime and then run our compiled Matlab code. Our wrapper script will need to accomplish the following steps:

-   Unpack the transferred runtime
-   Set the environment variables
-   Run our compiled matlab code

Fortunately, the Matlab compiler has pre-written most of this wrapper script for us!

1.  Take a look at `run_matrix.sh`. Which of the above steps do we need to add? Once you have an idea, move to the next step.\\

2. We'll need to add commands to unpack the runtime (which will have been transferred with the job). Add this line to the beginning of the `run_matrix.sh` file, after `#!/bin/bash` but before `exe_name=$0` : \\

``` file
tar -xzf r2015b.tar.gz
```

\\

4. Look at `readme.txt` to determine what arguments our wrapper script requires. Once you have an idea, move to the next step.\\

5. The name of the Matlab runtime directory is a required argument to the wrapper script `run_matrix.sh`. We'll have to do a little extra work to find out the name of that directory. 2. Untar the runtime tarball we downloaded to the submit server. 3. What directory has been created? This is the name of the runtime directory, and the argument you should pass to `run_matrix.sh`.

Submitting the Job
------------------

1.  Copy an existing submit file into your current directory. The submit file we used for the [Open Bugs](/user-school/2017/materials/day3/part1-ex4-prepackaged.md) example would be a good candidate, as that example also used a wrapper script. 2. Modify your submit file for this job. 3. Check your changes against the list below.
    -   The `executable` for this job is going to be our wrapper script `run_matrix.sh`. \\

``` file
executable = run_matrix.sh
```

-   You need to transfer the compiled binary `matrix`, as well as the runtime `.tar.gz` file, using `transfer_input_files`. \\

``` file
transfer_input_files = matrix, r2015b.tar.gz
```

-   The argument for the executable (`run_matrix.sh`) is "v90", as that is the name of the un-tarred runtime directory. \\

``` file
arguments = v90
```

-   Look at the size of the untarred runtime directory by running: \\

``` console
%UCL_PROMPT_SHORT% <strong>du -sh v90/</strong>
```

\\ We need to request *at least* this much \\ disk space in our submit file: \\

``` file
request_disk = 2GB
```

\\

3. Submit the job using `condor_submit`. 4. After it completes, the job should have produced a file called `results.txt`

