Tuesday Exercise 1.2: Log in to the OSG Submit Machine
======================================================

The goal of this exercise is to log on to a different submit host so that you can start submitting jobs into the OSG instead of the local cluster here at UW-Madison. Additionally, you will learn about the `tar` and `scp` commands, which will allow you to efficiently copy files between the two submit nodes.

If you have trouble getting `ssh` access to the submit machine, ask the instructors right away! Gaining access is critical for all remaining exercises.

Log in to the OSG submit machine
--------------------------------

For some of the remaining exercises today, you will be using a machine named `osg-learn.chtc.wisc.edu`. The username and password are listed on your 'Accounts' paper that you received yesterday. If you no longer have it, please ask the instructors for help.

Once you have your account details, `ssh` in to the machine and take a look around.

Preparing files for transfer
----------------------------

When transferring files between computers, it's best to limit the number of files as well as their size. Smaller files transfer more quickly and if your network connection drops, restarting the transfer is less painful than it would be if you were transferring large files.

Archiving tools (WinZip, 7zip, Archive Utility, etc.) can compress the size of your files and place them into a single, smaller archive file. The `tar` command is a one-stop shop for creating, extracting, and viewing the contents of `tar` archives (called tarballs) whose usage is as follows:

-   To **create** a tarball named `<archive filename>` containing `<archive contents>`, use the following command: \\

``` console
%UCL_PROMPT_SHORT% <strong>tar -czvf <archive filename> <archive contents></strong>
```

\\ <p> Where `<archive filename>` should end in `.tar.gz` and `<archive contents>` can be a list of any number of files and/or folders, separated by spaces.</p>

-   To **extract** the files from a tarball into the current directory: \\

``` console
%UCL_PROMPT_SHORT% <strong>tar -xzvf <archive filename></strong>
```

-   To **list** the files within a tarball: \\

``` console
%UCL_PROMPT_SHORT% <strong>tar -tzvf <archive filename></strong>
```

Using the above knowledge, log into `learn.chtc.wisc.edu`, create a tarball that contains Monday's exercise 2.4 directory, and verify that it contains all the proper files.

### Comparing compressed sizes

You can adjust the level of compression of `tar` by prepending your command with `GZIP=--<span style="background-color: #FFCCFF;"><COMPRESSION></span>`, where `<span style="background-color: #FFCCFF;"><COMPRESSION></span>` can be either `fast` for the least compression, or `best` for the most compression (the default compression is between `best` and `fast`).

1.  Use `wget` to download the following files from our web server:
    1.  Text file: <http://proxy.chtc.wisc.edu/SQUID/osgschool17/random_text>
    2.  Archive: <http://proxy.chtc.wisc.edu/SQUID/osgschool17/pdbaa.tar.gz>
    3.  Image: <http://proxy.chtc.wisc.edu/SQUID/osgschool17/obligatory_cat.jpg>
2.  Use `tar` on each file and compare the sizes of the original file and the compressed version.

Which files were compressed the least? Why?

Transferring files
------------------

### Using secure copy

[Secure copy](https://en.wikipedia.org/wiki/Secure_copy) (`scp`) is a command based on `SSH` that lets you securely copy files between two different hosts. It takes similar arguments to the `cp` command that you are familiar with but also takes additional host information:

``` console
%UCL_PROMPT_SHORT% <strong>scp <source 1> <source 2>...<source N> <remote host>:<remote path> 
</strong>
```

For example, if I were logged in to `learn.chtc.wisc.edu` and wanted to copy the file `foo` from my current directory to my home directory on `osg-learn.chtc.wisc.edu`, the command would look like this:

``` console
%UCL_PROMPT_SHORT% <strong>scp foo osg-learn.chtc.wisc.edu:~
</strong>
```

Additionally, I could also pull files from `osg-learn.chtc.wisc.edu` to `learn.chtc.wisc.edu`. The following command copies `bar` from my home directory on `osg-learn.chtc.wisc.edu` to my current directory on `learn.chtc.wisc.edu`:

``` console
%UCL_PROMPT_SHORT% <strong>scp osg-learn.chtc.wisc.edu:~/bar .
</strong>
```

You can also copy folders between hosts using the `-r` option. If I kept all my files from Monday's exercise 1.3 in a folder named `monday-1.3` on `learn.chtc.wisc.edu`, I could use the following command to copy them to my home directory on `osg-learn.chtc.wisc.edu`:

``` console
%UCL_PROMPT_SHORT% <strong>scp -r monday-1.3 osg-learn.chtc.wisc.edu:~
</strong>
```

Try copying the tarball you created earlier in this exercise on `learn.chtc.wisc.edu` to `osg-learn.chtc.wisc.edu`.

### Secure copy from your laptop

During your research, you may need to retrieve output files from your submit host to inspect them on your personal machine, which can also be done with `scp`! To use `scp` on your laptop, follow the instructions relevant to your machine's operating system:

#### Mac and Linux users

`scp` should be included by default and available via the terminal on both Mac and Linux operating systems. Open a terminal window on your laptop and try copying the tarball containing Monday's 2.4 exercise from `osg-learn.chtc.wisc.edu` to your laptop.

#### Windows users

WinSCP is an `scp` client for Windows operating systems.

1.  Install WinSCP from <https://winscp.net/eng/index.php>
2.  Start WinSCP and enter your SSH credentials for `osg-learn.chtc.wisc.edu`
3.  Copy the tarball containing Monday's 2.4 exercise exercise to your laptop

### Extra challenge: Using rsync

`scp` is a great, ubiquitous tool for one-time transfers but if you find yourself transferring the same set of files to the same location repeatedly, there are better tools to use. Another common tool available on many linux machines is `rsync`, which is like a beefed-up version of `scp`. The invocation is similar to `scp`: you can transfer files and/or folders, but the options are different and when transferring folders, make sure they don't have a trailing slash (`/`, this means to copy all the files within the folder instead of the folder itself):

``` console
%UCL_PROMPT_SHORT% <strong>rsync -Pavz <source 1> <source 2>...<source N> <remote host>:<remote path> 
</strong>
```

`rsync` has many benefits over `scp` but two of its biggest features are built-in compression (so you don't have to create a tarball) and the ability to only transfer files that have changed. Both of these feature are helpful when you're having connectivity issues so that you don't have to restart the transfer from scratch every time your connection fails.

1.  Use `rsync` to transfer the folder containing today's exercise 1.1 to `osg-learn.chtc.wisc.edu`
2.  Create a new file in your exercise 1.1 folder on `learn.chtc.wisc.edu` with the `touch` command: \\ <pre class="screen"><span class="twiki-macro UCL_PROMPT_SHORT"></span> **touch <filename>**</pre>
3.  Use the same `rsync` command to transfer the folder with the new file you just created. How many files were transferred the first time? How many files were transferred if you run the same rsync command again?

Next exercise
-------------

Once completed, move onto the next exercise: [Running jobs in the OSG](/user-school/2017/materials/day2/part1-ex3-submit-osg.md)

