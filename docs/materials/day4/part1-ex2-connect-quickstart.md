--+ !Thursday Exercise 1.2: Do the OSG Connect Quickstart

For this exercise, you can also follow the online guide from the OSG Connect helpdesk that will acquaint you with submission on the OSG Connect submit server. Please use the submit host user-training.osgconnect.net instead of login.osgconnect.net for the workshop.

Setup
-----

-   SSH into `user-training.osgconnect.net` (the OSG Connect submit server for this workshop).

Get the example files for the tutorial "OSG Connect Quickstart" via `tutorial` command
--------------------------------------------------------------------------------------

We will get the example files using the `tutorial` command.

``` console
%UCL_PROMPT_SHORT% <strong>tutorial quickstart</strong>
```

This creates a directory `tutorial-quickstart`. Go inside the directory and see what is inside.

``` console
%UCL_PROMPT_SHORT% <strong>cd tutorial-quickstart</strong>
%UCL_PROMPT_SHORT% <strong>ls -F </strong>
```

You will see the following contents:

``` file
Images/  osg-template-job.submit  short.sh  tutorial02.submit log/     README.md   tutorial01.submit  tutorial03.submit
```

We will focus our attention on `short.sh` (execution file) and `tutorial01.submit`. We will not worry about other files in this exercise. Feel free to take a look at other files if you are interested.

Job Execution File
------------------

Take a look at the job execution file `short.sh`.

``` console
%UCL_PROMPT_SHORT% <strong>cat short.sh </strong>
```

This is a shell script, quite ordinary . Run this shell script locally to see what it does.

``` console
%UCL_PROMPT_SHORT% <strong>chmod + short.sh </strong>
%UCL_PROMPT_SHORT% <strong>./short.sh </strong>
```

Submitting the job on the OSG
-----------------------------

The job description file `tutorial01.submit` executes the shell script `short.sh` as a vanilla universe job. Take a look at the job description file.

``` console
%UCL_PROMPT_SHORT% <strong>cat tutorial01.submit </strong>
```

Now run this job on the OSG.

``` console
%UCL_PROMPT_SHORT% <strong>condor_submit tutorial01.submit </strong>
```

Once your job has finished, you can look at the files that HTCondor has returned to the working directory. If everything was successful, it should have returned:

``` file
job.output (An output file for each job's output)
job.error: (An error file for each job's errors)
job.log: (A log file for each job's log)
```

Read the output file.

``` console
%UCL_PROMPT_SHORT% <strong>cat job.output </strong>
```

Observe the difference between the outputs from running the job on the OSG and running locally. (Hint: Check the username, id, work directory etc.)

