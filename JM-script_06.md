---
title: "Shell Scripts"

Learners have had:
- An intro to basic shell commands like ls, head, cp, cd, rm, ect...
- Navigating between directories
- Very basic pipes and know some special characters (like wildcard expansion)
- An on the fly loop (nested loop even)
  - IT the logical progression is
questions:
- "How save loops and write flexible code that can be reused?"
- "How can I save and re-use commands?"
objectives:
- "Write a shell script that runs a command or series of commands for a fixed set of files."
- "Run a shell script from the command line."
- "Write a shell script that operates on a set of files defined by the user on the command line."
- "Create pipelines that include shell scripts you, and others, have written."
keypoints:
- "Save commands in files (usually called shell scripts) for re-use."
- "`bash filename` runs the commands saved in a file."
- "`$@` refers to all of a shell script's command-line arguments."
- "`$1`, `$2`, etc., refer to the first command-line argument, the second command-line argument, etc."
- "Place variables in quotes if the values might have spaces in them."
- "Letting users decide what files to process is more flexible and more consistent with built-in Unix commands."
---

I chose 'writing shell scripts' because it is often skipped but is important
to set good habits that encourages reproducibility and reuse of code.

We are finally ready to see what makes the shell such a powerful programming environment.
We are going to take the commands we repeat frequently and save them in files
so that we can re-run all those operations again later by typing a single command.



For historical reasons,
a bunch of commands saved in a file is usually called a **shell script**,
but make no mistake:
these are actually small programs.

Let's start by going back to `molecules/` and creating a new file, `middle.sh` which will
become our shell script:

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
(which runs within the shell). In vim, hit 'i' to begin typing.
If the file does not exist, vim and nano create it. If it does exist, you can edit the file.
We can use the text editor to directly edit the file -- we'll simply insert head and tail
commands that will output the middle of a file:

~~~
head -n 15 octane.pdb | tail -n 5
~~~
{: .source}

This is a variation on the pipe constructed earlier:
it selects lines 11-15 of the file `octane.pdb`.
Remember, we are *not* running it as a command just yet:
we are putting the commands in a file.

Then we save the file by
1. hitting escape
2. then `Shift-:` to tell vim that we are passing commands rather than typing,
3. and type `wq` and hitting enter
Check that the directory `molecules` now contains a file called `middle.sh`.

If this didn't go well, you can use:
echo 'head -n 15 octane.pdb | tail -n 5' > middle.sh

Once we have saved the file,
we can ask the shell to execute the commands it contains.
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

Sure enough,
our script's output is exactly what we would get if we ran that pipeline directly.



What if we want to select lines from a different file?
We could edit `middle.sh` each time to change the filename,
but that would probably take longer than typing the command out again
in the shell and executing it with a new file name.
Instead, edit `middle.sh` and make it more versatile:

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
Let's fix that by using the special variables `$2` and `$3` for the
number of lines to be passed to `head` and `tail` respectively:

~~~
$ nano middle.sh
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

This works,
but it may take the next person who reads `middle.sh` a moment to figure out what it does.
We can improve our script by adding some **comments** at the top:

~~~
$ vim middle.sh
~~~
{: .language-bash}

~~~
# Select lines from the middle of a file.
# Usage: bash middle.sh filename end_line num_lines
head -n "$2" "$1" | tail -n "$3"
~~~
{: .output}

A comment starts with a `#` character and runs to the end of the line.
The computer ignores comments,
but they're invaluable for helping people (including your future self) understand and use scripts.
The only caveat is that each time you modify the script,
you should check that the comment is still accurate:
an explanation that sends the reader in the wrong direction is worse than none at all.

What if we want to process many files in a single pipeline?
For example, if we want to sort our `.pdb` files by length, we would type:

~~~
$ wc -l *.pdb | sort -n
~~~
{: .language-bash}

because `wc -l` lists the number of lines in the files
(recall that `wc` stands for 'word count', adding the `-l` option means 'count lines' instead)
and `sort -n` sorts things numerically.
We could put this in a file,
but then it would only ever sort a list of `.pdb` files in the current directory.
If we want to be able to get a sorted list of other kinds of files,
we need a way to get all those names into the script.
We can't use `$1`, `$2`, and so on
because we don't know how many files there are.
Instead, we use the special variable `$@`,
which means,
'All of the command-line arguments to the shell script'.
We also should put `$@` inside double-quotes
to handle the case of arguments containing spaces
(`"$@"` is special syntax and is equivalent to `"$1"` `"$2"` ...).

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


Generating files or figures often require a couple lines of commands, and maybe your figure looks great, but you can't remember how it happened. You can make a bash script from the series of commands that did something useful --- for example, to save the commands in a file.
Instead of typing them in again
(and potentially getting them wrong)
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

In practice, most people develop shell scripts by running commands at the shell prompt a few times
to make sure they're doing the right thing,
then saving them in a file for re-use.
This style of work allows people to recycle
what they discover about their data and their workflow with one call to `history`
and a bit of editing to clean up the output
and save it as a shell script.

## Pipeline: Creating a Script

When your data analysis pipeline requires some lines of bash code, you want to make text files of that code to make it reproducible. The easiest way to capture all the steps is in a script.

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
she doesn't have to remember to exclude the 'Z' files.
The disadvantage is that it *always* selects just those files and it can't run on all files
(including the 'Z' files),
without editing the script.
To be even more reusable and portable, the script can be written to check for command-line arguments,
and use `NENE*[AB].txt` if none were provided.
Of course, this introduces another tradeoff between flexibility and complexity.
