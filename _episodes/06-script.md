---
title: "Shell Scripts"
teaching: 25
exercises: 20
questions:
- I chose 'writing shell scripts' because I think that it is an important lesson for setting good reproducibility habits and encourages efficiency.
- At this point in the SC intro to shell, learners have had
  - An intro to basic shell commands like ls, head, cp, cd, rm, ect...
  - Navigating between directories
  - Very basic pipes and know some special characters (like wildcard expansion)
  - An on the fly loop (nested loop even)

objectives:
- "Write a shell script that runs on a fixed set of files."
- "Run a shell script from the command line."
- "Pass their shell script positional arguments"
- "Create pipelines."

keypoints:
- "How to save commands and history to text"
- "Run their code by calling `bash filename`"
- "Expand positional parameters with `$@` and wildcard/glob"
- "`$1`, `$2` to call specific positional parameters."
- "To use quotes to carefully expand variables"
- "Writing cod that is more flexible and more consistent with built-in Unix commands."
---


# INTRO
- Up to this point we've learned how to get around and run basic commands- but we have been limited to the commands built into the bash environment.
- When we want to use many commands and in a specific order, we can add the commands and loops to a text file and ask bash to run everything line by line- which should result in the same outcome every time.
- This is what a basic bash or **shell script** is: multiple lines of bash commands and makes bash a powerful and flexible programming environment.
- We could save save everything together so that we can re-run all those operations again later by typing a single command.

# START

We will start by making a shell script in the folder containing the data that it will be used for.

Change directories to `molecules/` and creating a new file, `middle.sh`.

In the shell-novice folder:

~~~
$ cd molecules
$ vim middle.sh
~~~
{: .language-bash}

If that didn't get you there, try:

```
cd $HOME/shell-novice/data-shell/molecules
```

The command `vim middle.sh` opens the file `middle.sh` within the text editor 'vim'
(which runs within the shell).

- If the file does not exist, vim and nano create it.
- If it does exist, you can edit the file.
- In vim, hit 'i' to begin typing.

We will insert commands that we are familiar with: head and tail.

We will write our shell script to use these commands to output the middle of a file.

~~~
head -n 15 octane.pdb | tail -n 5
~~~
{: .source}

This is a variation on the pipe constructed earlier:
it selects lines 11-15 of the file `octane.pdb`.
Remember, we are *not* running it as a command just yet:
we are putting the commands in a file.

Then we save the file in vim by:
1. hitting escape
2. then `Shift-:` to tell vim that we are passing commands rather than typing,
3. and type `wq` and hitting enter


Check that the directory `molecules` now contains a file called `middle.sh`.

If this didn't go well and you are not in vim, you can use:
echo 'head -n 15 octane.pdb | tail -n 5' > middle.sh

Once we have saved the file,we can ask the shell to execute the commands it contains.
Our shell is called `bash`, so we run the following command:

~~~
$ bash middle.sh
~~~
{: .language-bash}

~~~
ATOM      9  H           1      -4.502   0.681   0.785  1.00  0.00
ATOM     10  H           1      -5.254  -0.243  -0.537  1.00  0.00
ATOM     11  H           1      -4.357   1.252  -0.895  1.00  0.00
ATOM     12  H           1      -3.009  -0.741  -1.467  1.00  0.00
ATOM     13  H           1      -3.172  -1.337   0.206  1.00  0.00
~~~
{: .output}

- New macs might use a more recent version of shell, called zsh. But bash should still work

When you call the `bash` command, terminal is running the script in the first position in a bash environment that knows all of the bash commmands

If we want to select lines from a different,
ee could edit `middle.sh` each time to change the filename,
but that would probably take longer than typing the command out again
in the shell and executing it with a new file name.
Instead, edit `middle.sh` and make it more versatile, and accept a user designated file instead:

~~~
$ vim middle.sh
~~~
{: .language-bash}

Now, within "vim", replace the text `octane.pdb` with the special variable called `$1`:

~~~
head -n 15 "$1" | tail -n 5
~~~
{: .output}

Inside a shell script,
`$1` means 'the first filename (or other argument) on the command line'.
We can now run our script like this:

~~~
$ bash middle.sh octane.pdb
~~~
{: .language-bash}

~~~
ATOM      9  H           1      -4.502   0.681   0.785  1.00  0.00
ATOM     10  H           1      -5.254  -0.243  -0.537  1.00  0.00
ATOM     11  H           1      -4.357   1.252  -0.895  1.00  0.00
ATOM     12  H           1      -3.009  -0.741  -1.467  1.00  0.00
ATOM     13  H           1      -3.172  -1.337   0.206  1.00  0.00
~~~
{: .output}

or on a different file like this:

~~~
$ bash middle.sh pentane.pdb
~~~
{: .language-bash}

~~~
ATOM      9  H           1       1.324   0.350  -1.332  1.00  0.00
ATOM     10  H           1       1.271   1.378   0.122  1.00  0.00
ATOM     11  H           1      -0.074  -0.384   1.288  1.00  0.00
ATOM     12  H           1      -0.048  -1.362  -0.205  1.00  0.00
ATOM     13  H           1      -1.183   0.500  -1.412  1.00  0.00
~~~
{: .output}

> ## Double-Quotes Around Arguments
>
> For the same reason that we put the loop variable inside double-quotes,
> in case the filename happens to contain any spaces,
> we surround `$1` with double-quotes.
{: .callout}

We still need to edit `middle.sh` each time we want to adjust the range of lines,
though.
Let's fix that by using the special variables `$2` and `$3` to stand in for the
number of lines to be passed to `head` and `tail` respectively:

~~~
$ vim middle.sh
~~~
{: .language-bash}

~~~
head -n "$2" "$1" | tail -n "$3"
~~~
{: .output}

We can now run:

~~~
$ bash middle.sh pentane.pdb 15 5
~~~
{: .language-bash}

~~~
ATOM      9  H           1       1.324   0.350  -1.332  1.00  0.00
ATOM     10  H           1       1.271   1.378   0.122  1.00  0.00
ATOM     11  H           1      -0.074  -0.384   1.288  1.00  0.00
ATOM     12  H           1      -0.048  -1.362  -0.205  1.00  0.00
ATOM     13  H           1      -1.183   0.500  -1.412  1.00  0.00
~~~
{: .output}

By changing the arguments to our command we can change our script's
behaviour:

~~~
$ bash middle.sh pentane.pdb 20 5
~~~
{: .language-bash}

~~~
ATOM     14  H           1      -1.259   1.420   0.112  1.00  0.00
ATOM     15  H           1      -2.608  -0.407   1.130  1.00  0.00
ATOM     16  H           1      -2.540  -1.303  -0.404  1.00  0.00
ATOM     17  H           1      -3.393   0.254  -0.321  1.00  0.00
TER      18              1
~~~
{: .output}

This works, but you need to remember which positions are passed to which command

We can improve the utility of our script by adding some **comments** at the top.

~~~
$ vim middle.sh
~~~
{: .language-bash}

~~~
# Selects lines from the middle of a file.
# Usage: bash middle.sh filename end_line num_lines
head -n "$2" "$1" | tail -n "$3"
~~~
{: .output}

A comment starts with a `#` character and runs to the end of the line.
The bash ignores comments,
but they're invaluable for helping people (including your future self) understand and use scripts.
The only caveat is that each time you modify the script,
you should check that the comment is still accurate


~~~
$ wc -l *.pdb | sort -n
~~~
{: .language-bash}

because `wc -l` lists the number of lines in the files
(`wc` stands for 'word count', adding the `-l` option means 'count lines' instead)
and `sort -n` sorts things numerically.
We could put this in a file,
but then it would only ever sort a list of `.pdb` files in the current directory.


If we want it to work on any number of files, we can't use `$1`, `$2`, and so on
because we don't know how many files there are.

So we need a way to get our bash script to look for any number of positional arguments passed to the script.


Instead, we use the special variable `$@`,
which means,
'All of the command-line arguments to the shell script'.

Many built in bash commands were written to any number of positional arguments (to an extent)

And remember, positional arguments are different than options that are flagged such as `ls -a`.

For these commands, we often use a bash special variables, such as astrix, glob, star to expand to matching files in the directory.

To get our shell script aware of the possibility of many arguments, We put `$@` inside double-quotes
to each argument with a space between each. (`"$@"` is special syntax and is equivalent to `"$1"` `"$2"` ...).

Here's an example:

~~~
$ vim sorted.sh
~~~
{: .language-bash}

~~~
# Sort files by their length.
# Usage: bash sorted.sh one_or_more_filenames
wc -l "$@" | sort -n
~~~
{: .output}

~~~
$ bash sorted.sh *.pdb ../creatures/*.dat
~~~
{: .language-bash}

With the above line, each position $1 and $2 is expanded to match each file that it matches.

The order of files is also respected.

~~~
9 methane.pdb
12 ethane.pdb
15 propane.pdb
20 cubane.pdb
21 pentane.pdb
30 octane.pdb
163 ../creatures/basilisk.dat
163 ../creatures/minotaur.dat
163 ../creatures/unicorn.dat
596 total
~~~
{: .output}

And now, calling our own bash scripts is getting more complex. And you might not remember the order of your positional arguments.

Instead of typing them in again (and potentially doing something differently)
we can do this:

~~~
$ history | tail -n 5 > redo-list-3.sh
~~~
{: .language-bash}

The file `redo-list-3.sh` now contains:

~~~
  304  ls
  305  vim middle.sh
  306  echo 'head -n 15 octane.pdb | tail -n 5' > middle.sh
  307  cat middle.sh
  308  bash middle.sh
~~~
{: .source}

After a moment's work in an editor to remove the serial numbers on the commands,
and to remove the final line where we called the `history` command,
we have a completely accurate record of how we created the file.

You might even be able to knock out that first column that prevents the file from being a working bash script

In practice, most people develop shell scripts by running commands at the shell prompt a few times
to make sure they're doing the right thing,
then saving them in a file for re-use, and then debugging it line-by-line if needed


## Pipeline: Creating a Script

Once you have a few shell scripts that are required for an analysis, you'll want to make text files of that code to make it reproducible. The easiest way to capture all the steps is in a script.

First change directories to the gyre data folder:
```
$ cd ../north-pacific-gyre/2012-07-03/
```
{: .language-bash}

Run an editor to write calculating stats for data files:

~~~
# Calculate stats for data files.
for datafile in "$@"
do
    echo $datafile
    bash goostats $datafile stats-$datafile
done
~~~
{: .language-bash}

Save this to a file called `do-stats.sh`
so that everything can be reproduced from the first stage of the analysis by typing:

~~~
$ bash do-stats.sh NENE*[AB].txt
~~~
{: .language-bash}

or this:

~~~
$ bash do-stats.sh NENE*[AB].txt | wc -l
~~~
{: .language-bash}

so that the output is just the number of files processed
rather than the names of the files that were processed.

One thing to note about the script is that
it is usually better when it is portable and adaptable.
IT could have written it as:

~~~
# Calculate stats for Site A and Site B data files.
for datafile in NENE*[AB].txt
do
    echo $datafile
    bash goostats $datafile stats-$datafile
done
~~~
{: .language-bash}

The advantage is that this always selects the right files:
and do not have to remember to exclude the 'Z' files.
The disadvantage is that it *always* selects just those files and it can't run on all files
(including the 'Z' files),without editing the script.

To be even more reusable and portable, the script can be written to check for command-line arguments,
and use `NENE*[AB].txt` if none were provided. (if statement)
Of course, this introduces another tradeoff between flexibility and complexity.

In this lesson, we
- "Wrote a basic shell script that ran on a single file name"
- "We ran that shell script from the command line with the bash command"
- "We then added the ability to pass positional arguments to the script with $1 and $2"
- "Made a script with a flexible number of positional parameters with `$@` and wildcard/glob"
- "We saved commands and history to text"
- "And remember to use quotes to carefully expand variables"

Next would be the final lesson of 'intro to unix'- which is focused on find and grep.
---
