Composing Larger DAGs From Simpler DAGs With SPLICE
===================================================

The objective of this exercise is to show how to build up sophisticated DAGs built from smaller DAG files without changing the original files.

The basis of a lot of computer programming is to work up a small example, get it correct, and reuse it a building block for more sophisticated programs. The same is true for DAG files, and SPLICE is one way to do so.

We are going to use the Mandelbrot DAG from our previous example, and use that as a “black box”, so that we can add on to it, without changing that DAG at all.

Name your SPLICE subroutine
---------------------------

Our previously created DAG file is now going to be a SPLICE, or subroutine. We first need to create a master DAG file, which will call this as a splice, and name it. Let's call the splice MONTAGE. We will simple compute the size of the montage file we created in the previous DAG, with a new node which we will tack on to the last node in the previous file. We'll run the wc (word count) command with the -c argument to do so. The new submit file should look something like this;

``` console
executable              = /usr/bin/wc
arguments               = -c montage.out
log                     = wc.log
output                  = wc.out
error                   = wc.err
transfer_input_files = montage.out
request_memory = 1G
request_disk       = 1G
request_cpus      = 1
queue
```

By now, you should be able to create a simple dag file whose parent is the previous DAG (as a splice), and whose child is just this one node.. If you need help, check the HTCondor manual for [for a description of how to use SPLICE](http://www.cs.wisc.edu/condor/manual/current/2_10DAGMan_Applications.html).

