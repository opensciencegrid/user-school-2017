Thursday Exercise 1.4: Try Singularity on the OSG (Optional)
============================================================


In this tutorial, we see how to submit a \[tensorflow\](<https://www.tensorflow.org/>) job on the OSG through \[Singularity containers\](<https://support.opensciencegrid.org/solution/articles/12000024676-singularity-containers>). We currently offer CPU and GPU containers for tensorflow (both based on Ubuntu). Here, we focus on CPU container.

Setup
-----

-   Make sure you are logged into `user-training.osgconnect.net` (the OSG Connect submit server for this workshop).

Get the example files and understand the job requirements.
----------------------------------------------------------

Let us utilize the `tutorial` command. In the command prompt, type

``` console
%UCL_PROMPT_SHORT% <strong>tutorial tf-matmul  </strong>
```

This creates a directory `tutorial-tf-matmul`. Go inside the directory and see what is inside.

``` console
%UCL_PROMPT_SHORT% <strong>cd tutorial-tf-matmul</strong>
%UCL_PROMPT_SHORT% <strong>ls -F</strong>
```

You will see the following files

``` file
    tf_matmul.py            (Python program to multiply two matrices using tensorflow package)
    tf_matmul.submit        (HTCondor Job description file)
    tf_matmul_wrapper.sh    (Job wrapper shell script that executes the python program)
    tf_matmul_gpu.submit    (HTCondor Job description file targeting gpus)
```

NOTE: The file `tf_matmul_gpu.submit` is for gpus, but we will not focus on gpus in this exercise. You are welcome to take a look.

The python script \`tf\_matmul.py\` uses tensorflow to perform the matrix multiplication of a \`2x2\` matrix. Indeed, this is not the best use case of tensorflow. For our purpose of showing how to submit the tensorflow job on the OSG this example is just fine.

We want to run the program on a remote worker machine on the OSG that supports the singularity container. So we set the requirement in our HTCondor description.

``` console
Requirements = HAS_SINGULARITY == True
```

In addition, we also provide the full path of the image via the keyword `+SingularityImage`.

``` console
    +SingularityImage = "/cvmfs/singularity.opensciencegrid.org/tensorflow/tensorflow:latest"
```

The image is distributed to the remote worker machines through \`cvmfs\`. Although there are multiple ways to obtain the image file for a job on the OSG machine, the image distributed through \`cvmfs\` is preferred.

Submit the tensorflow example job
---------------------------------

Submit the job.

``` console
%UCL_PROMPT_SHORT% <strong>condor_submit tf_matmul.submit </strong>
```

The job will look for a machine on the OSG that has singularity installed, creates the singularity container with the image `/cvmfs/singularity.opensciencegrid.org/tensorflow/tensorflow:latest` and executes the program `tf_matmul.py`.

After the job is completed, you will see the output file `tf_matmul.output`.


