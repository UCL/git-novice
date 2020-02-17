---
title: Ignoring Things
teaching: 5
exercises: 0
questions:
- "How can I tell Git to ignore files I don't want to track?"
objectives:
- "Configure Git to ignore specific files."
- "Explain why ignoring files can be useful."
keypoints:
- "The `.gitignore` file tells Git what files to ignore."
---

What if we have files that we do not want Git to track for us,
like backup files created by our editor
or intermediate files created during data analysis?
Let's create a few dummy files:

~~~
$ mkdir receipts
$ touch a.png b.png c.png receipts/a.jpg receipts/b.jpg
~~~
{: .language-bash}

and see what Git says:

~~~
$ git status
~~~
{: .language-bash}

~~~
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	a.png
	b.png
	c.png
	receipts/

nothing added to commit but untracked files present (use "git add" to track)
~~~
{: .output}

Putting these files under version control would be a waste of disk space.
What's worse,
having them all listed could distract us from changes that actually matter,
so let's tell Git to ignore them.

We do this by creating a file in the root directory of our project called `.gitignore`:

~~~
$ nano .gitignore
$ cat .gitignore
~~~
{: .language-bash}

~~~
*.png
receipts/
~~~
{: .output}

These patterns tell Git to ignore any file whose name ends in `.png`
and everything in the `receipts` directory.
(If any of these files were already being tracked,
Git would continue to track them.)

Once we have created this file,
the output of `git status` is much cleaner:

~~~
$ git status
~~~
{: .language-bash}

~~~
On branch main
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	.gitignore

nothing added to commit but untracked files present (use "git add" to track)
~~~
{: .output}

The only thing Git notices now is the newly-created `.gitignore` file.
You might think we wouldn't want to track it,
but everyone we're sharing our repository with will probably want to ignore
the same things that we're ignoring.
Let's add and commit `.gitignore`:

~~~
$ git add .gitignore
$ git commit -m "Ignore png files and the receipts folder."
$ git status
~~~
{: .language-bash}

~~~
On branch main
nothing to commit, working directory clean
~~~
{: .output}

As a bonus, using `.gitignore` helps us avoid accidentally adding to the repository files that we don't want to track:

~~~
$ git add a.png
~~~
{: .language-bash}

~~~
The following paths are ignored by one of your .gitignore files:
a.png
Use -f if you really want to add them.
~~~
{: .output}

If we really want to override our ignore settings,
we can use `git add -f` to force Git to add something. For example,
`git add -f a.dat`.
We can also always see the status of ignored files if we want:

~~~
$ git status --ignored
~~~
{: .language-bash}

~~~
On branch main
Ignored files:
 (use "git add -f <file>..." to include in what will be committed)

        a.png
        b.png
        c.png
        receipts/

nothing to commit, working directory clean
~~~
{: .output}

> ## Ignoring Nested Files
>
> Given a directory structure that looks like:
>
> ~~~
> receipts/data
> receipts/plots
> ~~~
> {: .language-bash}
>
> How would you ignore only `receipts/plots` and not `receipts/data`?
>
> > ## Solution
> >
> > If you only want to ignore the contents of
> > `receipts/plots`, you can change your `.gitignore` to ignore
> > only the `/plots/` subfolder by adding the following line to
> > your .gitignore:
> >
> > ~~~
> > receipts/plots/
> > ~~~
> > {: .output}
> >
> > This line will ensure only the contents of `receipts/plots` is ignored, and
> > not the contents of `receipts/data`.
> >
> > As with most programming issues, there
> > are a few alternative ways that one may ensure this ignore rule is followed.
> > The "Ignoring Nested Files: Variation" exercise has a slightly
> > different directory structure
> > that presents an alternative solution.
> > Further, the discussion page has more detail on ignore rules.
> {: .solution}
{: .challenge}

> ## Including Specific Files
>
> How would you ignore all `.png` files in your root directory except for
> `final.png`?
> Hint: Find out what `!` (the exclamation point operator) does
>
> > ## Solution
> >
> > You would add the following two lines to your .gitignore:
> >
> > ~~~
> > *.png           # ignore all png files
> > !final.png      # except final.png
> > ~~~
> > {: .output}
> >
> > The exclamation point operator will include a previously excluded entry.
> >
> > Note also that because you've previously committed `.png` files in this
> > lesson they will not be ignored with this new rule. Only future additions
> > of `.png` files added to the root directory will be ignored.
> {: .solution}
{: .challenge}

> ## Ignoring Nested Files: Variation
>
> Given a directory structure that looks similar to the earlier Nested Files
> exercise, but with a slightly different directory structure:
>
> ~~~
> receipts/data
> receipts/images
> receipts/plots
> receipts/analysis
> ~~~
> {: .language-bash}
>
> How would you ignore all of the contents in the receipts folder, but not `receipts/data`?
>
> Hint: think a bit about how you created an exception with the `!` operator
> before.
>
> > ## Solution
> >
> > If you want to ignore the contents of
> > `receipts/` but not those of `receipts/data/`, you can change your `.gitignore` to ignore
> > the contents of receipts folder, but create an exception for the contents of the
> > `receipts/data` subfolder. Your .gitignore would look like this:
> >
> > ~~~
> > receipts/*               # ignore everything in receipts folder
> > !receipts/data/          # do not ignore receipts/data/ contents
> > ~~~
> > {: .output}
> >
> {: .solution}
{: .challenge}

> ## Ignoring all data Files in a Directory
>
> Assuming you have an empty .gitignore file, and given a directory structure that looks like:
>
> ~~~
> receipts/data/market_position/gps/a.dat
> receipts/data/market_position/gps/b.dat
> receipts/data/market_position/gps/c.dat
> receipts/data/market_position/gps/info.txt
> receipts/plots
> ~~~
> {: .language-bash}
>
> What's the shortest `.gitignore` rule you could write to ignore all `.dat`
> files in `result/data/market_position/gps`? Do not ignore the `info.txt`.
>
> > ## Solution
> >
> > Appending `receipts/data/market_position/gps/*.dat` will match every file in `receipts/data/market_position/gps`
> > that ends with `.dat`.
> > The file `receipts/data/market_position/gps/info.txt` will not be ignored.
> {: .solution}
{: .challenge}

> ## Ignoring all data Files in the repository
>
> Let us assume you have many `.dat` files in different subdirectories of your repository.
> For example, you might have:
> 
> ~~~
> results/a.dat
> data/experiment_1/b.dat
> data/experiment_2/c.dat
> data/experiment_2/variation_1/d.dat
> ~~~
> {: .language-bash}
> 
> How do you ignore all the `.dat` files, without explicitly listing the names of the corresponding folders?
> 
> > ## Solution
> >
> > In the `.gitignore` file, write:
> > 
> > ~~~
> > **/*.dat               
> > ~~~
> > {: .output}
> >
> > This will ignore all the `.dat` files, regardless of their position in the directory tree. 
> > You can still include some specific exception with the exclamation point operator.
> {: .solution}
{: .challenge}

> ## The Order of Rules
>
> Given a `.gitignore` file with the following contents:
>
> ~~~
> *.dat
> !*.dat
> ~~~
> {: .language-bash}
>
> What will be the result?
>
> > ## Solution
> >
> > The `!` modifier will negate an entry from a previously defined ignore pattern.
> > Because the `!*.dat` entry negates all of the previous `.dat` files in the `.gitignore`,
> > none of them will be ignored, and all `.dat` files will be tracked.
> >
> {: .solution}
{: .challenge}

> ## Log Files
>
> You wrote a script that creates many intermediate log-files of the form `log_01`, `log_02`, `log_03`, etc.
> You want to keep them but you do not want to track them through `git`.
>
> 1. Write **one** `.gitignore` entry that excludes files of the form `log_01`, `log_02`, etc.
>
> 2. Test your "ignore pattern" by creating some dummy files of the form `log_01`, etc.
>
> 3. You find that the file `log_01` is very important after all, add it to the tracked files without changing the `.gitignore` again.
>
> 4. Discuss with your neighbor what other types of files could reside in your directory that you do not want to track and thus would exclude via `.gitignore`.
>
> > ## Solution
> >
> > 1. append either `log_*`  or  `log*`  as a new entry in your .gitignore
> > 3. track `log_01` using   `git add -f log_01`
> {: .solution}
{: .challenge}
