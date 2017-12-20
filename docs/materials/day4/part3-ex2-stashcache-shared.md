Thursday Exercise 3.2: Using StashCache for Large Shared Data
=============================================================


This exercise will use a [BLAST](http://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastHome) workflow to demonstrate the functionality of StashCache for transferring input files to jobs on OSG.

Because our individual blast jobs from [Exercise 3.1](/user-school/2017/materials/day4/part3-ex1-blast-proxy.md) would take a bit longer with a larger database (too long for an workable exercise), we'll imagine for this exercise that our `pdbaa_files.tar.gz` file is too large for a web proxy (larger than ~1 GB). For this exercise, we will use the input from Exercise 3.1, but instead of using the web proxy for the `pdbaa` database, we will place it in StashCache via the OSG Connect server.

StashCache is a distributed set of caches spread across the U.S. They are connected with high bandwidth connections to each other, and to the data origin servers, where your data is originally placed.

<img src="%ATTACHURLPATH%/StashCacheMap.png" alt="StashCacheMap.png" width='680' height='358' />

There are two methods of pulling data from StashCache, within jobs. We are in the middle of a transition from one to another. They are:

1.  **stashcp** (old): A command line program to copy files from StashCache with **`cp`** like syntax. 2. **StashCache-over-CVMFS** (new): A much more intuitive and fault tolerant method for accessing StashCache files. But only available on a few resources, for now.

In the below tutorials, we will use **stashcp**, which will continue to be supported even after **StashCache-over-CVMFS** is fully functional.

Setup
-----

-   Make sure you're logged in to `user-training.osgconnect.net`
-   Transfer the following files from [Exercise 3.1](/user-school/2017/materials/day4/part3-ex1-blast-proxy.md) to a new directory called `thur-data-3.2`: `blast_wrapper.sh`, `mouse_rna.fa.1`, `mouse_rna.fa.2`, `mouse_rna.fa.3`, and the most recent submit file.

Place the Database in StashCache
--------------------------------

### Copy to your `public` space on OSG Connect

StashCache provides a public space for you to store data which can be accessed through the caching servers. First, you need to move your blast database into this public directory. If you remember the "public" directory in your home directory on the OSG Connect server `user-training.osgconnect.net`, it's this location where you will place files that need to end up in the StashCache data origin.

You have already placed files in the `~/stash/public` directory in the previous exercise in order for it to be accessible to the HTTP proxies. The same directory is accessible to StashCache.

As the `public` directory name indicates \*your files placed in the `public` directory will be accessible to anyone's jobs if they know how to use `stashcp`, though no one else will be able to edit the files, since only you can *place* or *change* files in your `public` space. For your own work in the future, make sure that you never put any sensitive data in such locations.

### Check the file on OSG Connect

Next, you can check for the file and test the command that we'll use in jobs on the OSG Connect login node:

``` console
%UCL_PROMPT_SHORT% <strong>ls public</strong>
```

Now, load the `stashcp` module, which will allow you to test a copy of the file from StashCache into your home directory on `login.osgconnect.net`:

``` console
%UCL_PROMPT_SHORT% <strong>module load stashcp</strong>
%UCL_PROMPT_SHORT% <strong>module load xrootd</strong>
%UCL_PROMPT_SHORT% <strong>stashcp /user/%RED%username%ENDCOLOR%/public/pdbaa_files.tar.gz ./</strong>
```

You should now see the `pdbaa_files.tar.gz` file in your current directory. Notice that we had to include the **`/user`** and **`username`** in the file path for `stashcp`, which make sure you're copying from **your** `public` space.

Modify the Submit File and Wrapper
----------------------------------

Return to your `thur-data-stash` directory on `user-training.osgconnect.net` (you may already be connected) where you will modify the files as described below:

1. At the top of the wrapper script (after `#!/bin/bash`), add the line to activate OSG Connect modules, followed by the above two lines that load the `stashcp` module and to copy the `pdbaa_files.tar.gz` file into the current directory of the job:

``` file
module load xrootd
module load stashcp
stashcp /user/%RED%username%ENDCOLOR%/public/pdbaa_files.tar.gz ./
```

2. Since HTCondor will no longer transfer or download the file for you, make sure to add the following line (or modify your existing `rm` command, if you're confident) to make sure the `pdbaa_files.tar.gz` file is also deleted and not copied back as perceived output.

``` file
rm pdbaa_files.tar.gz
```

3. Delete the `wget` line from the job wrapper script `blast_wrapper.sh`

4. Add the following line to the submit file and update the "requirements" statement to require servers with OSG Connect modules (for accessing the `stashcp` module), somewhere before the word `queue`.

``` file
+WantsStashCache = true
requirements = (OpSys == "LINUX") && (HAS_MODULES =?= true)
```

5. Confirm that your queue statement is correct for the current directory. It should be something like:

``` file
queue inputfile matching mouse_rna.fa.*
```

And that mouse\_rna.fa.\* files exist in the current directory (you should have copied them from the previous exercise directory.

Submit the Job
--------------

Now submit and monitor the job! If your 100 jobs from the previous exercise haven't started running yet, this job will not yet start. However, after it has been running for ~2 minutes, you're safe to continue to the next exercise!

Note: Keeping StashCache 'Clean'
--------------------------------

Just as for data on a web proxy, it is VERY important to remove old files from StashCache when you no longer need them, especially so that you'll have plenty of space for such files in the future. For example, you would delete (`rm`) files from `public` on `user-training.osgconnect.net` when you don't need them there anymore, but only after all jobs have finished. The next time you use StashCache after the school, remember to first check for old files that you can delete.


