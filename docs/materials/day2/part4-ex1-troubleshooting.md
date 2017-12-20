Tuesday Exercise 3.1: Troubleshooting a DAG
===========================================

The goal of this exercise is to troubleshoot some common problems that you may encounter when submitting a DAG using HTCondor. This exercise will likely take you longer than the allotted one hour of time; don't fret, there is an answer key provided, so work at your own pace.

Part 1: Acquiring the materials
-------------------------------

The materials for this exercise are located on our web server.

1.  Log into `learn.chtc.wisc.edu`
2.  Use `wget` to retrieve the materials from the web server:\\ <pre class="screen"><span class="twiki-macro UCL_PROMPT_SHORT"></span> **wget <http://proxy.chtc.wisc.edu/SQUID/osgschool17/tues_31.tar.gz>**</pre>
3.  Extract the tarball using the commands you learned earlier today
4.  Change into the directory extracted from the tarball and explore its contents

`anagram.dag` is the main DAG that you will be submitting and although it may look like a simple, linear DAG, it's actually more like the DAG from Monday's [exercise 4.2](/user-school/2017/materials/day1/part3-ex3-simple-dag.md) because of the [SUBDAG](http://research.cs.wisc.edu/htcondor/manual/v8.4/2_10DAGMan_Applications.html#SECTION003108900000000000000) within it! There is only one `.dag` file in the extracted folder, so where does the SUBDAG come from?

Part 2: Finding anagramic squares
---------------------------------

The contents of the tarball that you've extracted contain a DAG that is designed to solve [Project Euler problem 98](https://projecteuler.net/problem=98):

``` file
By replacing each of the letters in the word CARE with 1, 2, 9, and 6 respectively,
we form a square number: 1296 = 36<sup>2</sup>. What is remarkable is that, by using the
same digital substitutions, the anagram, RACE, also forms a square number:
9216 = 96<sup>2</sup>. We shall call CARE (and RACE) a square anagram word pair and specify
further that leading zeroes are not permitted, neither may a different letter have
the same digital value as another letter.

Using p098_words.txt, a 16K text file containing nearly two-thousand common English
words, find all the square anagram word pairs (a palindromic word is NOT considered
to be an anagram of itself).

What is the largest square number formed by any member of such a pair?

NOTE: All anagrams formed must be contained in the given text file.
```

Unfortunately, there are many issues with the DAG and its submit files that you will have to work through before you can you can obtain the solution to the problem (the code itself should be bug-free)! Submit the DAG:

``` console
 %UCL_PROMPT_SHORT% condor_submit_dag anagrams.dag
```

Then use your newfound HTCondor troubleshooting knowledge to find the answer to the Euler problem that ends up in `result.out` when you've successfully completed this exercise.

### Answer key

There is also a working solution on our web server that can be retrieved with

``` console
%UCL_PROMPT_SHORT% <strong>wget http://proxy.chtc.wisc.edu/SQUID/osgschool17/tues_31_answer.tar.gz</strong>
```

It contains comments labeled `SOLUTION` that you can consult in case you get stuck. Like any answer key, it's mainly useful as a verification tool, so try to only use it as a last resort or for additional illumination.

