<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Friday Exercise 1.3: Execute a Production Workflow
==================================================

In this exercise, you will:

-   finish testing workflow steps,
-   write the DAG for the workflow you planned, and
-   submit this workflow on `learn.chtc.wisc.edu`

There are bonus tasks in [Exercise 1.4](/user-school/2017/materials/day5/part2-ex2-workflow-tuning.md), if you get through this part quickly, including running the workflow on the Open Science Grid.

Steps to Take
-------------

Note: While one person in your group works on step 1, someone else can work on step 2.

### 1. Finish optimizing job components

In the [previous exercise](/user-school/2017/materials/day5/part1-ex2-plan-workflow.md) we ran some tests to find the optimal values for the *permutation* and *qtl* job steps. Now we want to implement these values and confirm that they work. The steps to take are as follows:

1.  Based on your previous tests, modify the *permutation* submit file for each trait according to:
    -   the optimal number of *job processes* (how many jobs to `queue`)
    -   number of permutations-per-process (one of the `arguments` to the `run_perm.R` script)
    -   requests for memory and disk
2.  Submit one of these newly modified *permutation* submit files in preparation for an optimized QTL test. If your estimate of the necessary permutations per process (for a ~30-minute run time) was not close enough, modify and test the *permutation* jobs again.
3.  Once the *permutation* jobs complete successfully, finish preparing for an optimized QTL step by using `tarit.sh` to package the output from the test *permutation* step just above. Then run the *QTL* job. As above, you will only need to submit one of the QTL submit files to confirm the approximate memory and disk needed. Make sure all of the desired output files for the QTL step are created to confirm success.
4.  Don't forget to test `taritall.sh` after a successful QTL job, to make sure it works as expected.

**After the optimized *permutation* and *QTL* tests, copy all output, error, and log files to a new directory to prepare for the production workflow.**

### 2. Create a DAG

Write a single DAG file for the workflow, including:

-   `JOB` lines for each submit file
-   `PARENT x CHILD y` lines as necessary
-   `SCRIPT PRE` and/or `SCRIPT POST` lines for the tar steps

**Note:** You may need to think about how each `tar` step works for deciding on "PRE" or "POST" scripts for each.

If you need a refresher on what a DAG looks like, see [this exercise from Monday](/user-school/2017/materials/day1/part3-ex4-complex-dag.md) or the [HTCondor manual](http://research.cs.wisc.edu/htcondor/manual/current/2_10DAGMan_Applications.htmll)

To **quickly** check that you've got the details of the DAG correct, modify your *permutation* submit files to submit only a few jobs, and with each job performing a much smaller number of permutations (say, ~10?).

### 3. Run a production workflow

Once you have run a quick test of the DAG and you know all the steps are working together, you can submit a full-scale run of the DAG! To do so, **modify the *permutation* submit files to the values you chose when optimizing the *permutation* step** (remember, this should be about 100,000 total permutations for each trait) and then submit the DAG. If you have any issues, consult the log and out files for the DAG and jobs, and modify your approach at any of the previous steps. While the full-scale DAG is running, you may wish to further detail your drawn workflow, including information regarding resource usage. Share all submit and DAG files with one another so everyone has a copy.

If you have time (even while step 3 is running smoothly), move on to the Bonus Tasks in [Exercise 1.4](/user-school/2017/materials/day5/part2-ex2-workflow-tuning.md)

