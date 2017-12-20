<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Monday Exercise 2.5: Submit With “queue matching”
=================================================

In this exercise and the next one, you will explore more ways to use a single submit file to submit many jobs. The focus of this exercise is to submit one job per filename that matches a given pattern.

In all cases of submitting many jobs from a single submit file, the key questions are:

-   What makes each job unique? In other words, there is one job per \_\_\_\_*\_*?
-   What tells HTCondor how to distinguish each job?

For `queue <i>N</i>`, jobs are distinguished simply by number and HTCondor assigns the numbers itself. But with the remaining `queue` forms, you help HTCondor distinguish jobs by other, more meaningful means.

Counting Words in Files
-----------------------

Suppose you have a collection of books, and you want to analyze how words vary from book to book or author to author. As mentioned in the lecture, HTCondor provides many ways to do this task. You could create a separate submit file for each book, and submit all of the files manually. Or you could be a bit more clever and create one submit file for all of the books: (DON'T ACTUALLY CREATE THIS SUBMIT FILE)

``` file
universe                = vanilla
executable              = freq.py
request_memory          = 20MB
request_disk          = 20MB
should_transfer_files   = YES
when_to_transfer_output = ON_EXIT

transfer_input_files = AAiW.txt
arguments            = &quot;AAiW.txt&quot;
output               = AAiW.out
error                = AAiW.err
log                  = AAiW.log
queue

transfer_input_files = PandP.txt
arguments            = &quot;PandP.txt&quot;
output               = PandP.out
error                = PandP.err
log                  = PandP.log
queue

transfer_input_files = TAoSH.txt
arguments            = &quot;TAoSH.txt&quot;
output               = TAoSH.out
error                = TAoSH.err
log                  = TAoSH.log
queue
```

If you use many `queue` statements in one submit file, HTCondor remembers and carries over values for attributes, such as `universe`, `executable`, `request_memory`, and so on in this example.

But even then, this approach results in a long, repetitive submit file. And if you add more books, you must add five more lines to the submit file for each book. Therefore, this is not a recommended approach to submitting many jobs with one submit file. Fortunately, HTCondor has many `queue` features to make this kind of job submission process easy!

Queue Jobs By Matching Filenames
--------------------------------

For our analysis, we will have a new version of the word-frequency counting script. It takes a single command-line argument, which is the name of the input file containing the text of a book, and it outputs the frequency of each word from least to most common. There will be several book files, and the filename for each book ends with `.txt`.

This is an example of a common scenario: We want to run one job per file, where the filenames match a certain consistent pattern. The `queue ... matching` statement is made for this scenario.

Let’s see this in action. First, here is the new version of the script:

``` file
#!/usr/bin/env python

import os
import sys
import operator

if len(sys.argv) != 2:
    print 'Usage: %s DATA' % (os.path.basename(sys.argv[0]))
    sys.exit(1)
input_filename = sys.argv[1]

words = {}

my_file = open(input_filename, 'r')
for line in my_file:
    line_words = line.split()
    for word in line_words:
        if word in words:
            words[word] += 1
        else:
            words[word] = 1
my_file.close()

sorted_words = sorted(words.items(), key=operator.itemgetter(1))
for word in sorted_words:
    print '%s %8d' % (word[0], word[1])
```

To use the script:

1.  Save it as `wordcount.py`
2.  Download and unpack some books from Project Gutenberg:\\ <pre class="screen">

<span class="twiki-macro UCL_PROMPT_SHORT"></span> **wget <http://proxy.chtc.wisc.edu/SQUID/osgschool17/books.zip>** <span class="twiki-macro UCL_PROMPT_SHORT"></span> **unzip books.zip** </pre>

1.  Verify the script by running it on one book manually
2.  Create a submit file to submit one file (pick one), including memory and disk requests of 20 MB; submit it, if you like
3.  Modify the following submit file statements to work for all books:\\ <pre class="file">

transfer\_input\_files = $(BOOK) arguments = $(book) output = $(book).out error = $(book).err queue book matching \*.txt </pre>\\ <p>Note, as always, the order of statements in a submit file does not matter, except that the `queue` statement should be last. Also note that any submit file variable name (here, `book`, but true for `process` and all others) may be used in any mixture of upper- and lowercase letters.</p>

1.  Submit the jobs

HTCondor uses the `queue ... matching` statement to look for files in the submit directory that match the given pattern, then queues one job per match. For each job, the given variable (e.g., `book` here) is assigned the name of the matching file, so that it can be used in `output`, `error`, and other statements.

The result is the same as if we had written out a much longer submit file:

``` file
...

transfer_input_files = AAiW.txt
arguments = &quot;AAiW.txt&quot;
output = AAiW.txt.out
error = AAiW.txt.err
queue

transfer_input_files = PandP.txt
arguments = &quot;PandP.txt&quot;
output = PandP.txt.out
error = PandP.txt.err
queue

transfer_input_files = TAoSH.txt
arguments = &quot;TAoSH.txt&quot;
output = TAoSH.txt.out
error = TAoSH.txt.err
queue
```

Here is some sample `condor_q -nobatch` output:

``` console
 ID      OWNER            SUBMITTED     RUN_TIME ST PRI SIZE CMD
  89.0   iaross          7/17 11:41   0+00:00:00 I  0    0.0 wordcount.py AAiW.txt
  89.1   iaross          7/17 11:41   0+00:00:00 I  0    0.0 wordcount.py PandP.txt
  89.2   iaross          7/17 11:41   0+00:00:00 I  0    0.0 wordcount.py TAoSH.txt
```

All three jobs were part of cluster 89. The first filename that was matched in the queue statement resulted in a process ID of 0, the second match has a process ID of 1, and the third has a process ID of 2.

When the three jobs finish, carefully look at the resulting files. Do they match your expectations? There should be a single log file, but three separate output files and three separate (and hopefully empty) error files, one for each job.

Extra Challenge 1
-----------------

In the example above, you used a single log file for all three jobs. HTCondor handles this situation with no problem; each job writes its events into the log file without getting in the way of other events and other jobs. But as you may have seen, it may be difficult for a person to understand the events for any particular job in the combined log file.

Create a new submit file that works just like the one above, except that each job writes its own log file.

