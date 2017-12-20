<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Monday Exercise 1.1: Log In and Look Around
===========================================

The goal of this first exercise is simply to log in to the local submit machine and look around a little bit.

This exercise should take only a few minutes. If you have trouble getting `ssh` access to the submit machine, ask the instructors right away! Gaining access is critical for all remaining exercises.

Logging In
----------

Today, you will use the machine named `learn.chtc.wisc.edu` for all of your exercises.

To log in, use a [Secure Shell](http://en.wikipedia.org/wiki/Secure_Shell) (SSH) client.

-   On OS X, run the Terminal app and use the `ssh` command, like so:

``` console
%UCL_PROMPT_SHORT% <strong>ssh username@learn.chtc.wisc.edu</strong>
```

-   On Windows, we recommend a free client called [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/), but any SSH client should be fine
-   On Linux, open a terminal window and use the `ssh` command, as exemplified above for Mac.

If you need help finding or using an SSH client, ask the instructors for help right away!

Once you have an SSH client, use it to log in to the submit machine. Some tips:

-   The hostname of the submit machine is `learn.chtc.wisc.edu`
-   Your username and initial password are located on the Accounts sheet of paper that you received this morning
-   While the `passwd` command, your initial password will be automatically reset for you on an hourly basis. (So you probably don't want to change your password, in the first place, and definitely want to keep your sheet of paper or memorize the password).

Running Commands
----------------

In the exercises, we will show commands that you are supposed to type or copy into the command line, like this:

``` console
%UCL_PROMPT_SHORT% <strong>hostname</strong>
learn.chtc.wisc.edu
```

**Note:** In the first line of the example above, the `%UCL_PROMPT_SHORT%` part is meant to show the Linux command-line prompt. You do not type this part! Further, your actual prompt probably is a bit different, and that is expected. So in the example above, the command that you type at your own prompt is just the eight characters `hostname`. The second line of the example, without the prompt, shows the output of the command; you do not type this part, either.

Here are a few other commands that you can try, to learn a little bit about this machine (the examples below do not show the output from each command):

``` console
%UCL_PROMPT_SHORT% <strong>date</strong>
%UCL_PROMPT_SHORT% <strong>uname -a</strong>
```

A suggestion for the day: Try typing into the command line as many of the commands as you can. Copy-and-paste is fine, of course, but you WILL learn more if you take the time to type each command, yourself.

Organizing Your Workspace
-------------------------

You will be doing many different exercises over the next few days, many of them on this submit machine. Each exercise may use many files, once finished. To avoid confusion, it may be useful to create a separate directory for each exercise.

For instance, for the rest of this exercise, you may wish to create and use a directory named `monday-1.1-login`, or something like that.

``` console
%UCL_PROMPT_SHORT% <strong>mkdir monday-1.1-login</strong>
%UCL_PROMPT_SHORT% <strong>cd monday-1.1-login</strong>
```

Showing the Version of HTCondor
-------------------------------

HTCondor is installed on this machine. But what version? You can ask HTCondor itself:

``` console
%UCL_PROMPT_SHORT% <strong>condor_version</strong>
$CondorVersion: 8.7.2 Jun 02 2017 BuildID: 407060 $
$CondorPlatform: x86_64_RedHat6 $
```

As you can see from the output, we are using HTCondor 8.7.2, which is the most recently released development version.

### Background information about HTCondor version numbers

HTCondor always has two types of releases at one time: stable and development. HTCondor 8.4.x and 8.6.x are considered *stable* releases, and you can know they are stable because the second digits (e.g., 4 or 6 in these cases) are even numbers. Within one stable series, all versions have the same features (for example 8.4.0 and 8.4.8 have the same set of features) and differ only in bug and security fixes.

HTCondor 8.7.2 is the current *development* release series of HTCondor. You know that it is a development release because the second digit (i.e., 7) is an odd number. Typically, the current development series (i.e., 8.7.x) has greater version numbers than the current stable series (i.e., 8.6.x), but that is not always true. In any case, development releases add new features and are more likely to have problems. For that reason, we typically do not use development releases for the School, but in this case, 8.7.2 is considered stable enough.

References
----------

Here are a few links to reference materials that might be interesting now or later.

-   [HTCondor home page](http://research.cs.wisc.edu/htcondor/)
-   [HTCondor manuals](http://research.cs.wisc.edu/htcondor/manual/); it is probably best to read the manual corresponding to the version of HTCondor that you use (8.7.2 for today)
-   [Center for High Throughput Computing](http://chtc.cs.wisc.edu/), our local research computing organization


