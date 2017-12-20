Simpler DAGs with variable substitutions
========================================

The objective of this exercise is to help you write simpler DAGs by using variable substitutions in your submit files.

If you look at the DAG we made, you might find it a bit tedious because each goatbrot job has a separate HTCondor submit file. They are nearly identical except for a couple of parameters. Can we make it simpler?

Yes, we can!

Declare Your Variables
----------------------

First you need to declare your variables in your submit file. Make one submit file for all of your goatbrot jobs. Here's what it looks like. Call it goatbrot.sub:

``` console
executable              = /usr/local/bin/goatbrot
arguments               = -i 100000 -c $(CENTERX),$(CENTERY) -w 1.5 -s 500,500 -o tile_$(TILEY)_$(TILEX).ppm
log                     = goatbrot.log
output                  = goatbrot.out.$(TILEY).$(TILEX)
error                   = goatbrot.err.$(TILEY).$(TILEX)
request_memory = 1G
request_disk       = 1G
request_cpus      = 1
queue
```

Then you need to change your DAG to use VARS for variable substitution. Here is what one of the jobs would look like:

``` console
JOB g1 goatbrot.sub
VARS g1 CENTERX="-0.75"
VARS g1 CENTERY="0.75"
VARS g1 TILEX="0"
VARS g1 TILEY="0"
```

Edit your DAG similarly for all of your goatbrot jobs. If you need help, check the Condor manual for [for a description of how to use VARS](http://www.cs.wisc.edu/condor/manual/current/2_10DAGMan_Applications.html). What happens?

