<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Friday Exercise 1.1: Learn about Joe's Desired Computing Work
=============================================================

In today's set of hands-on exercises, you'll be examining a set of pre-existing job submissions (scripts and submit files) and then coordinating them to run as a single workflow using a DAG. Along the way, you'll need to test some of the already written submit files. The first part of this exercise introduces the overall steps for the workflow and what pieces already exist (that you won't need to write yourself).

Introduction
------------

Joe Biologist studies the influence of genetic differences (genotypes) on the growth traits (phenotypes) of a common crop. He has come to you with some computing work he’d like to automate as a workflow, so that he can scale out his research and run it on a much larger HTC compute system. He has used HTCondor to manually submit individual jobs (never used a DAG), but has only run jobs on a smaller, dedicated HTCondor cluster where he doesn’t have to request CPU, disk, or RAM. He’s not familiar with DAGman. He's getting annoyed by how many manual job submissions and summary scripts he has to run, especially as he's thinking of scaling out his work in a way that would be much more cumbersome - so he's asking you to help him organize all the steps into an optimized DAG workflow.

(As you learn more details below, it might be helpful to draw a diagram that describes the steps of his workflow.)

After asking Joe many questions, you realize the following things:
------------------------------------------------------------------

**A) Joe starts with a file called `input.csv` that was made on his desktop.** When you open this file in excel, Joe shows you that each row corresponds to an individual plant. For each individual, the first three columns are phenotype measurements for each of three different growth *traits* (T240, T300, T360), and the remaining columns contain the genotype (A or B) of that individual across multiple genes (columns).

**B) At present, Joe does the following manual steps (for each of the three traits)** to achieve a meaningful result from the data in `input.csv`:

1.  Joe first submits a job that calculates a large number of hypothetical *permutations* of phenotype-genotype combinations for the trait. This produces a set of files containing all the permutations for that trait. 2. Joe then runs a script for each trait, to create a gzipped tar file of the *permutation* job results for that trait. 3. Joe then submits a second job for each trait, which uses the permutations that have been zipped together to perform a *QTL mapping* calculation. This calculation determines which genes were most important for the phenotype trait, relative to other possible genes exemplified in the random permutations. 4. As a final step for each trait, Joe runs a script that creates a gzipped tar file of all of the *permutation* and *QTL mapping* data for that trait, so that he can take it back to his desktop computer for final processing.

**C) For the *permutation* and *QTL mapping* calculations, Joe runs a special *wrapper* that then uses his main R scripts.** For each type of calculation this perl script, `runR.pl`, first installs R using files from Joe's proxy server and then sets up the R environment before running whatever R script has been indicated as an argument to `runR.pl`. Therefore, the `runR.pl` wrapper script is run as the HTCondor executable with the name of either R program as an argument to the wrapper (along with arguments to the R program, itself).

**D) The two tar steps that happen after the *permutation* and *QTL mapping* steps are simple shell scripts containing a relevant `tar` command,** and can be run on the submit server because they create little CPU load and finish very quickly.

**E) When Joe has previously run the first step, of 10,000 permutations, as one Condor job, the job runs for hours on his own small cluster.** He has cut the permutations of a single Condor job up into multiple, shorter Condor jobs of fewer permutations before, but he doesn’t remember the details of how many jobs and how many permutations per job worked well. Furthermore, Joe would like to scale up to **100,000** permutations per trait this time. Unlike the *permutation* step, the *QTL* step finishes quite quickly; even with 10,000 permutations completed for each trait, the *QTL* step completes within several minutes.

View Joe's files
----------------

**(but don't do anything with them until the next [exercise](/user-school/2017/materials/day5/part1-ex2-plan-workflow.md), where you'll plan necessary developments of a workflow for Joe.)**

Log in to `learn.chtc.wisc.edu` and move to a desired location in your home directory. Enter the following commands to download and decompress/untar Joe’s job ingredients:

``` console
%UCL_PROMPT_SHORT% <strong>wget http://proxy.chtc.wisc.edu/SQUID/osgschool17/WorkflowExercise.tar.gz</strong>
%UCL_PROMPT_SHORT% <strong>tar -xzf WorkflowExercise.tar.gz</strong>
```

You can now navigate into the `WorkflowExercise` directory to view the full ingredients for Joe’s Computing work. Review all of these files based upon the information below and refer back to it as you proceed through the remaining exercises (1.2-1.4). (If you've been drawing a diagram of Joe's work so far, feel free to annotate with some of this information.)

Based upon Joe’s description and submit files you determine the following details for each step:

#### **Permutation Step**

Note that the files below are the necessary files to submit the permutation calculations for **one** trait (in this case, trait 1). There are additional submit files to perform this step for the other traits.

Submit file:

-   `permutation1.submit`

In the submit file:

-   executable: `runR.pl`
-   arguments `1_$(Process) run_perm.R 1 $(Process) 10000`
-   input: `run_perm.R`, `input.csv`, `RLIBS.tar.gz`

Altogether, the job(s) created by this submit file will execute the command:

-   `./runR.pl 1_$(Process) run_perm.R 1 $(Process) 10000`

The arguments mean the following:

-   `1_$(Process)`, used by `runR.pl` to name its log file
-   `run_perm.R`, which R script we want to run
-   `1`, trait column in `input.csv` (i.e. this is 3 for `permutation3.submit`)
-   `$(Process)`, used by `run_perm.R` for naming the permutation output
-   `10000`, indicates that 10,000 permutations should be done for each individual job process

As output, this job will create:

-   a series of .log and .out files, named from the first argument (`1_$(Process)`). This looks like: `runR.1_0.out`, `runR.1_0.log`, `runR.1_0.err`
-   the actual output file, named using the `1` and `$(Process)` arguments, like so: `perm_part.1_0.Rdat`

#### **Combine Permutation Output**

`tarit.sh` is run after each trait's Permutation step (with the column number as an argument) to compress `perm_part.1_0.Rdat` (or potentially multiple such files named according to "perm\_part.1\_\*.Rdat") for the QTL step.

Sample execution: \* `./tarit.sh 1`, where "1" is the trait column number.

#### **QTL Mapping Step**

Again, the files below are for submitting the *QTL* calculations for **one** trait (in this case, trait 1). There are additional submit files to perform this step for the other traits.

Submit file:

-   `qtl1.submit`

In the submit file:

-   executable: `runR.pl`
-   arguments `qtl_1 qtl.R 1`
-   input: `qtl.R`, `input.csv`, `RLIBS.tar.gz`, `perm_combined_1.tar.gz` (created by `tarit.sh`)

Altogether, the job(s) created by this submit file will execute the command:

-   `./runR.pl qtl_1 qtl.R 1`

The arguments mean the following:

-   `qtl_1`: used by `runR.pl` to name its log / output / error files
-   `qtl.R`: which R script we want to run
-   `1`: trait column in `input.csv`, will also be used to name the output files

As output, this job will create:

-   a series of .log and .out files, named from the first argument (`qtl_1`).
-   several output files named for the trait argument `1`: `perm_combined_1.Rdat`, `perm_summary_1.txt`, `sim_geno_results_1.Rdat`, `qtl_1.Rdat`, `refined_qtl_summary_1.txt`, `refined qtl_1.Rdat`, and `fit_qtl_results_1.Rdat`

#### **Combine All Output**

`results_1.tar.gz` is made by running `taritall.sh` (with the column number as an argument) after the QTL step for that trait finishes, where "1" reflects the trait column number in the output above.

Sample execution:

-   `./taritall.sh 1`, where "1" is the trait column number.


