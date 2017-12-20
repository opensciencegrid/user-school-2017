<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Friday Exercise 1.2: Plan Joe's Workflow
========================================

This exercise outlines our goal (creating a production workflow) and the steps needed to achieve it. Before starting this section, make sure to first read [Exercise 1.1](/user-school/2017/materials/day5/part1-ex1-science-intro.md), which has important background information about Joe's intended work and how he has submitted jobs so far.

Your mission
------------

Your goal is to plan out Joe’s workflow based upon small-scale test jobs and, later, to write a full-scale DAG to actually run his workflow in production. In particular, you will need to do the following:

-   **Optimize the submit files for the *permutation* jobs of each trait,** such that each trait takes advantage of high-throughput parallelization with some number of job *processes*. Each process should calculate a portion of the new total of 100,000 permutations per trait (versus the 10,000 permutations per trait that Joe has been working with previously).

<!-- -->

-   **Optimize submit file values for "request\_memory" and "request\_cpus"** for each *permutation* and *QTL mapping* step, for each trait of the three traits.

<!-- -->

-   **Create a single DAG file, with *permutation* and *QTL mapping* jobs for each of the three traits,** including PRE and/or POST scripts for the `tar` scripts that need to be run.

We'll be working on the first two optimization steps during this session, and the second optimization step and DAG creation after the break, in [Exercise 1.3](/user-school/2017/materials/day5/part2-ex1-execute-workflow.md).

### \*NOTE: In what follows, the only files you will need to modify are the submit files (and as specifically instructed). You will not need to modify any of the input files, output files, or scripts/programs. It is advisable that you split up some of the work within your pair or group in order to be time-efficient.

Draw a diagram
--------------

Based upon what you [learned from Joe](UserSchool17Fri11LearnJoe'sWork), **draw the *general workflow* that you would make** for Joe (on paper), keeping in mind that there are 3 traits for which the *permutation* and *QTL mapping* steps need to be completed. (If you started drawing a diagram while reading the previous page, just extend it here). The tar steps will probably need to be PRE or POST scripts (you decide which is best). Think about what Joe's intended workflow means for the shape of the DAG, including PARENT-CHILD dependencies for JOBs in the DAG and the fact that the *permutation* step could be broken up into multiple processes of fewer total permutations, each. We'll come back to this diagram in the [next exercise](UserSchool16Fri13ExecFlow) after the break, when it's time to construct the full DAG.

Optimize job components
-----------------------

Before we assemble the full DAG, we want to optimize each component of the workflow. This will include 3 optimization steps:

### 1. Test the HTC optimization of the *permutation* step.

You will need to determine the number of permutations that should be batched in a single job process, if each process needs to run in ~30 minutes for good HTC scaling. To do this, you will need to run some test jobs, to see how long it takes a single job process to run, say, 10, 100, or 1000 permutations.

To run these test jobs:

1.  Modify one of the permutation submit files to “queue” 10 jobs (so that you can average time between the 10 test jobs)
2.  Add "request\_cpus = 1" according to Joe's indication
3.  Add reasonable first guesses for "request\_memory" and "request\_disk" (say, 1 GB?).
4.  Make a few copies of this submit file so that you can change the last argument (the number of permutations) from "10000" to "10", "100", or "1000". For time's sake, a member of your group should test all three of these variations at the same time!

Getting ready for the next step:

1.  After each set of *permutation* tests finishes, you’ll need to use `tarit.sh` (with the correct argument) before running the test jobs for the QTL step.

### 2. Test each of the *QTL mapping* jobs

Once you have results from your previous *permutation* step testing, you can start optimizing the *QTL mapping* jobs. Submit the three submit files (one for each trait) after adding lines for "request\_memory", "request\_disk", and "request\_cpus". The resource needs (RAM and disk space) and execution time of each *QTL mapping* job will likely increase with the total number of permutations from the previous *permutation* step, though the execution time will likely still be short (according to Joe). You'll test optimized *permutation* and *QTL mapping* steps later on.

### 3. Optimization planning

In order to optimize Joe's overall workflow, we can optimize the following values, based on our test jobs from steps 1 and 2:

**Permutation throughput:** Calculate the number of permutations that should be run *per job process*, such that the runtimes per process will be about 30 minutes (not exact, but closer to 30 than to 5 or 60). You can then calculate the number of processes that should be queued such that 100,000 permutations are calculated for each trait. Essentially, you want *job processes* X *permutations* to equal 100,000 total permutations for each of the three phenotype traits. \*Hint: You can use the "condor\_history" feature (similar to condor\_q, but for completed jobs) to easily view and compare the "RUNTIME" for jobs in a "cluster" (using the cluster value as an argument to `condor_history`.

**Memory and disk for both steps:** Make sure to examine the log files of your *permutation* and *QTL* test jobs, so that you can extrapolate how much memory and disk should be requested in the submit files for the full-scale DAG.

### **When you're done with \#3, move on to [Exercise 1.3](/user-school/2017/materials/day5/part2-ex1-execute-workflow.md)**

