---
title: "Shell Scripts"
teaching: 30
exercises: NA
questions:

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


### BACKGROUND
- I chose 'writing shell scripts' because I think that it is an important lesson for setting good reproducibility habits and encourages efficiency- and for submitting bash jobs to the scheduler

- Up to this point the learners would have been exposed to navigating directories, running basic commands, a little looping, piping, and wildcard/variable expansion.

- BUT we've been limited to running builtin commands ran directly on the command line.

- The idea with this lesson is to introduce the concept of shell scripts being executed line-by-line in a bash environment.

- We start by building their first shell script.

### START
- Now that we have a few commands and for loops in our toolkit, we can start using these tools to start processing and analyzing data.

- And rather than relying on other people to figure how we did our analyses(and to spare them the time that you had to trouble shoot)   

- We can put together text file of the commands (and the order)- that when executed line-by-line- recreates the steps that you went through.

- And this is what basic bash or **shell script** are: multiple lines of commands that call bash commands or other programs  

- They can be shared with other researchers- or with a little bit of extra instructions, be executed on powerful supercomputer clusters like mesabi.

- This is a part of why bash is a powerful and flexible programming environment to learn.

- It's fast and everywhere. Ultimately, we could write shell scripts that re-run all the operations again later by typing a single command on different machines.

- We will start by making our first shell script in the folder containing the data that it will be used for.

- Change directories to the `molecules/` folder and creating a new file, `middle.sh`.

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

- The command `vim middle.sh` opens the file `middle.sh` within the text editor 'vim' (which runs within our shell environment).
- If the file does not exist, vim and nano create it.
- If it does exist, you can edit the file.

- In vim, hit 'i' to begin typing.

- We will insert commands that we are familiar with: head and tail.

- We are going to write our shell script to use these head and tail to output lines 11-15 of the file `octane.pdb`

~~~
head -n 15 octane.pdb | tail -n 5
~~~
{: .source}

- We are *not* running it as a command by writing it to a file: we are putting the commands to make a shell script.

Once we've typed it, save the file in vim by:
1. hitting escape
2. then `Shift-:` to tell vim that we are passing commands rather than typing,
3. and type `wq` and hitting enter

- Check that the directory `molecules` now contains a file called `middle.sh`.

- If this didn't go well and you don't see it, you can use:
```
echo 'head -n 15 octane.pdb | tail -n 5' > middle.sh
```

- Once we have saved the file,we can ask the shell to execute the commands it contains.
- Our shell is called `bash`, so we run the following command:

Execute the script in a bash environment by typing:
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

- When you call the `bash` command, terminal is running the script in the first position in a bash environment that knows all of the bash commands.

-To be more flexible:

- If we want to select lines from a different,we could edit `middle.sh` each time to change the filename.
That would probably take longer than typing the command out again.

- Instead, let's edit `middle.sh` to make it more versatile, and accept a user designated file instead:

~~~
$ vim middle.sh
~~~
{: .language-bash}

- Now, within "vim", replace the text `octane.pdb` with the special variable called `$1`:

~~~
head -n 15 "$1" | tail -n 5
~~~
{: .output}

- Inside a shell script,`$1` means 'the first filename (or other argument) on the command line'.

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

- HOWEVER- we still need to edit `middle.sh` each time we want to adjust the range of lines

- Let's fix that by using the special variables `$2` and `$3` to stand in for the
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

We can improve the portability of our script by adding some **comments** at the top.

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
The only caveat is that each time you modify the script, you should check that the comment is still accurate

What if we want to process MANY files in a single pipeline?

For example, if we want to sort our .pdb files by length, we would type:

~~~
$ wc -l *.pdb | sort -n
~~~
{: .language-bash}

because `wc -l` lists the number of lines in the files (`wc` stands for 'word count', adding the `-l` option means 'count lines' instead) and `sort -n` sorts things numerically.

We could put this in a file, but then it would only ever sort a list of all `.pdb` files in the current directory.

If we want it to work on any number of files, we can't use `$1`, `$2`, because we don't know how many files there are.

So we need a way to get our bash script to look for any number of positional arguments passed to the script.

To do this, we use the special variable `$@`,which means, 'All of the command-line arguments passed to the shell script'.

Many built in bash commands were written to look for any number of positional arguments (to an extent)

And remember, positional arguments are different than options that are flagged such as `ls -a`.

For these commands, we often use a bash special variables, such as glob, star to expand to matching files in the directory.

To make our shell script aware of the possibility of many arguments, We put `$@` inside double-quotes
to expand to a long line of arguments with a space between each of them. (`"$@"` is special syntax and is equivalent to `"$1"` `"$2"` ...).

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

With this command, each arugument at the $1 and $2 positions are expanded to include each file names that the pattern matches.

The order of these files is also respected- all pdb files are listed first, then all the creature files

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

And with that, our bash scripting is getting more complex. And you might not remember the order of your positional arguments.

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
then saving them in a file for re-use, and then debugging it line-by-line if needed.

And if the script needs to run on many files at the same time, or needs lots of memory to run, it is submitted to the msi job scheduler

## Pipeline: Creating a Script

If you have lots of files, and dont need the in-between files, you can write your script
so the output can be piped to the next command. Lets make one of these in the 'gyres'

First change directories to the gyre data folder:
```
$ cd ../north-pacific-gyre/2012-07-03/
```
{: .language-bash}

Lets write a loop for calculating stats for these data files:

`vim do-stats.sh`

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
IT could have written more specifically as:

~~~
# Calculate stats for Site A and Site B data files.
for datafile in NENE*[AB].txt
do
    echo $datafile
    bash goostats $datafile stats-$datafile
done
~~~
{: .language-bash}

The advantage of one is that this always selects the right files:
and do not have to remember to exclude the 'Z' files.
The disadvantage is that it *always* selects just those files and it can't run on all files
(including the 'Z' files),without editing the script.

To be even more reusable and portable, the script can be written to check for command-line arguments,
and use `NENE*[AB].txt` if none were provided. (if statement)
Of course, this introduces another tradeoff between flexibility and complexity.

For this lesson, we
- "Wrote a basic shell script that ran on a single file name"
- "We ran that shell script from the command line with the bash command"
- "We then added the ability to pass positional arguments to the script with $1 and $2"
- "Made a script with a flexible number of positional parameters with `$@` and wildcard/glob"
- "We saved commands and history to text"
- "And remember to use quotes to carefully expand variables"

Next would be the final lesson of 'intro to unix'- which is focused on find and grep.


---
