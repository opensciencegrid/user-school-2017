<style type="text/css"> pre em { font-style: normal; background-color: yellow; } pre strong { font-style: normal; font-weight: bold; color: \#008; } </style>

Monday Exercise 2.6: Submit With “queue from”
=============================================

The goal of this exercise is to submit many jobs from a single submit file by using the `queue ... from` syntax to read variable values from a file.

More Job Submission Alternatives
--------------------------------

In the previous exercise, we use the `queue ... matching` syntax, which is primarily useful for collections of files with similar names. But that method has its weaknesses, too. It is less useful when a collection of files does not have a consistent and unique pattern for its names, or when the values for different job conditions are **not** filenames, or both.

Queue Jobs From File Contents
-----------------------------

Suppose we want to modify our word-frequency analysis a little bit so that it outputs only the most common *N* words of a document. Further, we want to experiment with different values of *N*. And finally, even though we downloaded it, we no longer want to analyze the Sherlock Holmes stories in the `TAoSH.txt` file. Clearly, `queue ... matching` will not help us in this case.

First, we need a new version of the word counting program so that in accepts an extra number as a command line argument and outputs only that many of the most common words. Here is the new code:

``` file
#!/usr/bin/env python

import os
import sys
import operator

if len(sys.argv) != 3:
    print 'Usage: %s DATA NUM_WORDS' % (os.path.basename(sys.argv[0]))
    sys.exit(1)
input_filename = sys.argv[1]
num_words = int(sys.argv[2])

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
for word in sorted_words[-num_words:]:
    print '%s %8d' % (word[0], word[1])
```

To submit this program with a collection of two variables values for each run:

1.  In the same directory as the last exercise, save the script as `wordcount-top-n.py`
2.  Copy your submit file from the last exercise to a new name (maybe `wordcount-top.sub`)
3.  Update the `executable` and `log` statements as appropriate
4.  Update other statements to work with two variables, `book` and `n`:\\ <pre class="file">

output = $(book)\_top\_$(n).out error = $(book)\_top\_$(n).err transfer\_input\_files = $(book) arguments = "$(book) $(n)" queue book, n from books\_n.txt </pre>\\ <p>Note especially the changes to the `queue` statement; it now tells HTCondor to read a separate text file of ***pairs*** of values, which will be assigned to `book` and `n` respectively.</p>

1.  Create the separate text file of job variable values and save it as `books_n.txt`: <pre class="file">

AAiW.txt, 10 AAiW.txt, 25 AAiW.txt, 50 PandP.txt, 10 PandP.txt, 25 PandP.txt, 50 </pre>\\ <p>Note that we used 3 different values for *N* for each book, and that we dropped the Sherlock Holmes book, `TAoSH.txt`.</p>

1.  Submit the file
2.  Do a quick sanity check: How many jobs were submitted? How many log, output, and error files were created?

Extra Challenge 1
-----------------

Between this exercise and the previous one, you have explored two of the three primary `queue` statements. How would you use the `queue in ... list` statement to accomplish the same thing(s) as one or both of the exercises?

Extra Challenge 2
-----------------

You may have noticed that the output of these jobs has a messy naming convention. Because our macros resolve to the filenames, including their extension (e.g., `AAiW.txt`), the output filenames contain with multiple extensions (e.g., `AAiW.txt.err`). Although the extra extension is acceptable, it makes the filenames harder to read and possibly organize. Change your submit file for this exercise so that the output filenames do not include the `.txt` extension.

Extra Challenge 3
-----------------

Adjust your `queue...matching` submit file so that those results also don't include the `.txt` extension. (**Hint**: You can use function macros to format variables. Check [the HTCondor Manual](https://research.cs.wisc.edu/htcondor/manual/v8.5/2_5Submitting_Job.html).)

