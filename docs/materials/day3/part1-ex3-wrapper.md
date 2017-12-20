<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Wednesday Exercise 1.3: Using Wrapper Scripts to Submit Jobs
============================================================

In this exercise, you will create a wrapper script to run the same program (`blastx`) as \\ the [previous exercise](/user-school/2017/materials/day3/part1-ex2-precompiled.md).

Background
----------

Wrapper scripts are a useful tool for running software that can't be \\ compiled into one piece, needs to be installed with every job, or just for running extra steps. \\ A wrapper script can either install the software from the source code, or use an already existing software (as in this exercise). Not only does this portability technique work with almost any kind of software that can be locally installed, it also allows for a great deal of control and flexibility for what happens within your job. Once you can write a script to handle your software (and often your data as well), you can submit a large variety of workflows to a distributed computing system like the Open Science Grid.

Wrapper Script, part 1
----------------------

Our wrapper script will be a bash script that runs several commands.

1.  In the same directory as the last exercise (still logged into `osg-learn.chtc.wisc.edu`) make a file called `run_blast.sh`. \\

<!-- -->

1.  The first line we'll place in the script is the basic command for running blast. Based on our previous submit file, what command needs to go into the \\

script? Once you have an idea, check against the example below: \\ <pre class="file"> \#/bin/bash

ncbi-blast-2.6.0+/bin/blastx -db pdbaa/pdbaa -query mouse.fa -out results.txt </pre>\\ (**Note**: the "header" of `#!/bin/bash` will tell the computer that this is a bash shell script and can be run in the same way that \\ you would run individual commands on the command line.) \\

Submit File Changes
-------------------

We now need to make some changes to our submit file.

1.  Make a copy of your previous submit file and open it. \\

<!-- -->

1.  Since we are now using a wrapper script, that will be our job's executable. Replace the original `blastx` exeuctable with the name \\

of our wrapper script and comment out the arguments line. \\ <pre class="file"> executable = run\_blast.sh \#arguments = </pre>\\

1.  Note that since the `blastx` program is no longer listed as the executable, it will be need to be included in `transfer_input_files`. Instead of \\

transferring just that program, we will transfer the original downloaded `tar.gz` file. \\ <pre class="file"> transfer\_input\_files = pdbaa, mouse.fa, **ncbi-blast-2.6.0+-x64-linux.tar.gz** </pre>\\

1.  To achieve efficiency, we'll also transfer the pdbaa database as the original `tar.gz` file instead of as the unzipped folder: \\ <pre class="file">

transfer\_input\_files = **pdbaa.tar.gz**, mouse.fa, ncbi-blast-2.6.0+-x64-linux.tar.gz </pre>\\

Wrapper Script, part 2
----------------------

Now that our database and BLAST software are being transferred to the job as `tar.gz` files, our script needs to accommodate.

1.  Opening your `run_blast.sh` script, add two commands at the start to un-tar the BLAST and pdbaa `tar.gz` files. See the \\

previous exercise if you're not sure what this command looks like. \\

1.  In order to distinguish this job from our previous job, change the output file name to something besides `results.txt`. \\

<!-- -->

1.  The completed script `run_blast.sh` should look like this: \\ <pre class="file">

\#/bin/bash

tar -xzf ncbi-blast-2.6.0+-x64-linux.tar.gz tar -xzf pdbaa.tar.gz

ncbi-blast-2.6.0+/bin/blastx -db pdbaa/pdbaa -query mouse.fa -out results2.txt </pre>\\

1.  While not strictly necessary, it's a good idea to enable executable permissions on the wrapper script, like so: \\

``` console
%UCL_PROMPT_SHORT% <strong>chmod u+x run_blast.sh</strong>
```

Your job is now ready to submit. Submit it using `condor_submit` and monitor using `condor_q`.

