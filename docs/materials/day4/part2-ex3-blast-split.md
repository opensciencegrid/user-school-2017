Thursday Exercise 2.3: Splitting Large Input for Better Throughput
==================================================================


The objective of this exercise is to prepare for blasting a much larger input query file by splitting the input for greater throughput and lower memory and disk requirements. Splitting the input will also mean that we don't have to rely on additional large-data measures for the input query files.

Setup
-----

-   Make sure you are still logged into `user-training.osgconnect.net`
-   Make sure you are in the directory named `thur-blast-data` under the `stash` filesystem.

### Obtain the large input

We've previously used `blastx` to analyze a relatively small input file of test data, `mouse.fa`, but let's imagine that you now need to blast a much larger dataset for your research. This dataset can be downloaded with the following command:

``` console
%UCL_PROMPT_SHORT% <strong>wget http://proxy.chtc.wisc.edu/SQUID/osgschool17/mouse_rna.tar.gz</strong>
```

After un-tar'ing the file, you should be able to confirm that it's size is roughly 100 MB. Not only is this a bit large for file transfer, but it would take hours to complete a single `blastx` analysis for it. Also, the single output file would be huge. Compare for yourself to the time and output file size for the mouse.fa input file, according to your test job in the last exercise.

### Split the input file

For `blast`, it's scientifically valid to split up the input query file, analyze the pieces, and then put the results back together at the end! (Importantly, blast databases should not be split, because the `blast` output includes a score value for each sequence that is calculated relative to the entire length of the database.)

Because genetic sequence data is used heavily across the life science, there are also tools for splitting up the data into smaller files. One of these is called [genome tools](http://genometools.org/), and you can download a package of precompiled binaries (just like blast) using the following command:

``` console
%UCL_PROMPT_SHORT% <strong>wget http://genometools.org/pub/binary_distributions/gt-1.5.9-Linux_x86_64-64bit-complete.tar.gz</strong>
```

Un-tar the gt package (`tar -xzvf ...`), then run it's sequence file splitter as follows, with the target file size of 1 MB:

``` console
%UCL_PROMPT_SHORT% <strong>./gt-1.5.9-Linux_x86_64-64bit-complete/bin/gt splitfasta -targetsize 1 mouse_rna.fa</strong>
```

You'll notice that the result is a set of 100 files, all about the size of 1 MB, and numbered 1 through 100.

Test a split job
----------------

Now, you'll run a test job to prepare for submitting many jobs, later, where each will use a different input file.

### Modify the submit file

First, you'll create a new submit file that passes the input filename as an argument and use a list of applicable filenames. Follow the below steps:

1. Copy the submit file to a new file called `blast_split.sub` and modify the "queue" line of the submit file to the following:

``` file
queue inputfile matching mouse_rna.fa.1
```

(If this queue line looks like we should be scanning all of the rna files, wait until the next exercise)

2. Replace the `mouse.fa` instances in the submit file with `$(inputfile)`, and rename the output, log, and error files to use the same `inputfile` variable:

``` file
output = $(inputfile).out
error = $(inputfile).err
log = $(inputfile).log
```

3. Add an `arguments` line to the submit file so it will pass the name of the input file to the wrapper script

``` file
arguments = $(inputfile)
```

4. Update the memory and disk requests, since the new input file is larger and will also produce larger output. It may be best to overestimate to something like 1 GB for each. (After completing this test, you'll be able to update them to a more accurate value.)

### Modify the wrapper file

Replace instances of the input file name in the `blast_wrapper.sh` script so that it will insert the first argument in place of the input filename, like so:

``` file
./blastx -db pdbaa -query $1 -out $1.result
```

NOTE: bash shell scripts will use the first argument in place of `$1`, the second argument as `$2`, etc.

### Submit the test job

This job will take a bit longer than the job in the last exercise, since the input file is larger (by about 3-fold). Again, make sure that only the desired `output`, `error`, and `result` files come back at the end of the job.

Update the resource requests
----------------------------

After the job finishes successfully, examine the `log` file for memory and disk usage, and update the requests in the submit file. In [Exercise 3.1](Education.UserSchool17Thu31BlastProxy) (after the next lecture) you'll submit many jobs at once *and* use a different method for handling the `pdbaa_files.tar.gz` file, which is a bit too large to use regular file transfer when submitting many jobs.


