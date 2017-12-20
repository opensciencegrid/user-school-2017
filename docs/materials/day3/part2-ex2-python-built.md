<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Wednesday Exercise 1.7: Using Python, Pre-Built
===============================================

In this exercise, you will install Python, package your installation, and then use it to run jobs. It should take about 20 minutes.

Background
----------

We chose Python as the language for this example because: a) it is a common language used for scientific computing and b) it has a straightforward installation process and is fairly portable.

Running any Python script requires an installation of the Python interpreter. The Python interpreter is what we're using when we type `python` at the command line. In order to run Python jobs on a distributed system, you will need to install the Python interpreter (what we often refer to as just "installing Python"), within the job, then run your Python script.

There are two installation approaches. The approach we will cover in this exercise is that of "pre-building" the installation (much like we did with Open BUGS [thir morning](/user-school/2017/materials/day3/part1-ex4-prepackaged.md)). We will install Python to a specific directory, and then create a tarball of that installation directory. We can then use our tarball within jobs to run Python scripts.

Interactive Job for Pre-Building
--------------------------------

The first step in our job process is building a Python installation that we can package up.

1.  Create a directory for this exercise on `learn.chtc.wisc.edu` and `cd` into it.
2.  Download the Python source code from <https://www.python.org/>. \\

``` console
%UCL_PROMPT_SHORT% <strong>wget https://www.python.org/ftp/python/3.6.1/Python-3.6.1.tgz</strong>
```

1.  Of our options - submit server, interactive job, personal computer - which should we use for this installation/packaging process? Once you have a guess, move to the next step.
2.  Due to the number of people on our submit server, we shouldn't use the submit server. Your own computer probably doesn't have the right operating system. \\

The best place to install will be an interactive job. For this job, we can use the same interactive submit file as Exercise 1.4, with one change. What is it?

1.  Make a copy of the interactive submit file from [Exercise 1.4](/user-school/2017/materials/day3/part1-ex4-prepackaged.md) and change the `transfer_input_files` line to the Python tarball you just downloaded. Then submit it using the `-i` flag. \\

``` console
%UCL_PROMPT_SHORT% <strong>condor_submit -i build.submit</strong>
```

1.   Once the interactive job begins, we can start our installation process. First, we have to determine how to install Python to a specific location in our working directory.
    1.  Untar the Python source tarball and look at the `README` in the `Python-3.6.1` directory. What will the main installation steps be? Where will Python be installed by default? Once you've tried to answer these questions, move to the next step.
    2.  There are some basic installation \\

instructions near the top of the `README`. Based on that short introduction, we can see the main steps of installation will be: \\

    ./configure
    make
    make install

\\ \\ This looks a lot like the Open BUGS installation from earlier today! It turns out that this three-stage process (configure, make, make install) is a common \\ way to install many software packages. \\ \\ Also like the Open BUGS installation, the default installation \\ location for Python requires `sudo` (administrative privileges) to install. However, we'd like to install to a specific location in the working directory \\ so that we can compress that installation directory into a tarball. How did we do this with Open BUGS? \\

1.   With Open BUGS we used the `-prefix` option with the `configure` script. Let's see if the Python \\

`configure` script has this option by using the "help" option. \\ \\

``` console
%UCL_PROMPT_SHORT% <strong>./configure --help</strong>
```

\\ \\ Sure enough, there's a list of all the different options that can be passed to the `configure` script, which includes `-prefix`. Therefore, we can use the \\ `$(pwd)` command in order to set the path correctly, just as we did earlier today.

1.  Now let's actually install Python!
    1.  **From the job's main working directory**, create a directory to hold the installation. \\

``` console
%UCL_PROMPT_SHORT% <strong>cd $_CONDOR_SCRATCH_DIR</strong>
%UCL_PROMPT_SHORT% <strong>mkdir python</strong>
```

1.  Move into the `Python 3.6.1` directory and run the installation commands. These may take a few minutes each. \\

``` console
%UCL_PROMPT_SHORT% <strong>cd Python-3.6.1</strong>
%UCL_PROMPT_SHORT% <strong>./configure --prefix=$(pwd)/../python</strong>
%UCL_PROMPT_SHORT% <strong>make</strong>
%UCL_PROMPT_SHORT% <strong>make install</strong>
```

1.  If I move back to the main job working directory, and look in the `python` subdirectory, I should see a Python installation. \\

``` console
%UCL_PROMPT_SHORT% <strong>cd ..</strong>
%UCL_PROMPT_SHORT% <strong>ls python/</strong>
bin  include  lib  share
```

1.  I have successfully created a self-contained Python installation. Now it just needs to be tarred up! \\

``` console
%UCL_PROMPT_SHORT% <strong>tar -czf prebuilt_python.tar.gz python/</strong>
```

1.  Before exiting, we might want to know how we installed Python for later reference. \\

Enter the following commands to save our history to a file: \\

``` console
%UCL_PROMPT_SHORT% <strong>history > python_install.txt</strong>
```

1.  Exit the interactive job. \\

``` console
%UCL_PROMPT_SHORT% <strong>exit</strong>
```

Python Script
-------------

1.  Create a script with the following lines called `fib.py`. \\

``` file
import sys
import os

if len(sys.argv) != 2:
    print('Usage: %s MAXIMUM' % (os.path.basename(sys.argv[0])))
    sys.exit(1)
maximum = int(sys.argv[1])
n1 = n2 = 1
while n2 <= maximum:
    n1, n2 = n2, n1 + n2
print('The greatest Fibonacci number up to %d is %d' % (maximum, n1))
```

2. What command line arguments does this script take? Try running it on the submit server.

Wrapper Script
--------------

We now have our Python installation and our Python script - we just need to write a wrapper script to run them.

1.  What steps do you think the wrapper script needs to perform? Create a file called `run_fib.sh` and write them out in plain English before moving to the next step.
2.  Our script will need to
    1.  untar our `prebuilt_python.tar.gz` file
    2.  access the `python` command from our installation to run our `fib.py` script
3.  Try turning your plain English steps into commands that the computer can run.
4.  Your final `run_fib.sh` script should look something like this: \\ <pre class="file">

\#/bin/bash

tar xzf prebuilt\_python.tar.gz python/bin/python3 fib.py 5 </pre>\\ or\\ <pre class="file"> \#/bin/bash

tar xzf prebuilt\_python.tar.gz export PATH=$(pwd)/python/bin:$PATH python3 fib.py 5 </pre>\\

1.  Make sure your `run_fib.sh` script is executable.

Submit File
-----------

1.  Make a copy of a previous submit file in your local directory (the Open BUGS submit file could be a good starting point). What changes need to be made to run this Python job? 2. Modify your submit file, then make sure you've included the key lines below: \\

``` file
executable = run_fib.sh
transfer_input_files = fib.py, prebuilt_python.tar.gz
```

1.  Because we pre-built our Python installation on a machine running Scientific Linux, version 6.something, we should request machines with similar characteristics. Add the following line to your submit file as well: \\

``` file
requirements = (OpSys == "LINUX" && OpSysMajorVer == 6 )
```

3. Submit the job using `condor_submit`. 4. Check the `.out` file to see if the job completed.

