<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Wednesday Exercise 1.9: Using Docker
====================================

In this exercise, you will install run the same Python script as the previous exercises, but using a Docker container.

Background
----------

Containers are another way to manage software installations. We don't have the time to go fully into the details of building and using containers, but can use a publicly available Python container to run our Python job.

One caveat for using containers: not all systems will support them. HTCondor has built-in features for using Docker and many Open Science Grid resources have Singularity installed, but they are still not in widespread use.

Submit File Changes
-------------------

1.  Make a copy of your submit file from the [previous exercise](/user-school/2017/materials/day3/part2-ex3-python-install.md).
2.  Add the following three lines to the submit file or modify existing lines to match the lines below: \\

``` file
universe = docker
docker_image = python
requirements = (OpSysMajorVer == 7)
```

\\ Here we are requesting HTCondor's Docker universe and using a pre-built python image that, by default, will be pulled from a public website of Docker images called DockerHub. \\ The requirements line will ensure that we run on computers whose operating system can support Docker.

1.  Adjust the executable and arguments lines. The executable can now be the Python script itself, with the appropriate arguments: \\

``` file
executable = fib.py
arguments = 5
```

1.  Finally, we no longer need to transfer a Python tarball (whether source code or pre-built) or our Python script. You can remove both from the `transfer_input_files` \\

line of the submit file.

Python Script
-------------

1.  Open the Python script and add the following line at the top: \\

``` file
#!/usr/bin/env python
```

\\ This will ensure that the script uses the version of Python that comes in the Docker container.

Once these steps are done, submit the job.

