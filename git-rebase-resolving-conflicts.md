## How do I resolve a conflict that has occurred during rebase?

#### Pulling the latest changes and initiating a rebase

Assuming that since we've started working on our feature branch, other changes have been introduced into `master`.

To integrate those changes, we need to switch to `master`, pull the upstream changes locally, then switch to our `feature-branch` and rebase it on top of `master`:

```
git switch master
git pull
git switch feature-branch
git rebase master
```

#### Conflict occurs during rebase

After doing that, we receive a message that a conflict has occured:

```
First, rewinding head to replay your work on top of it...
Applying: Feature branch change
Using index info to reconstruct a base tree...
M       README.md
Falling back to patching base and 3-way merge...
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
error: Failed to merge in the changes.
Patch failed at 0001 Feature branch change
Use 'git am --show-current-patch' to see the failed patch

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

If we read into the error message, `git` gives us some tips on how to proceed resolving this conflict.

This is what the conflict looks like:

```
user@somepc:~/workspace/project (feature-branch|REBASE 1/1)$ git diff
diff --cc README.md
index 90002d7,96f5078..0000000
mode 100644,100644..100755
--- a/README.md
+++ b/README.md
@@@ -1,2 -1,2 +1,6 @@@
  Okay
++<<<<<<< HEAD
 +Master change
++=======
+ Feature branch change
++>>>>>>> Feature branch commit message
```

#### Resolving the conflict

As we can see, we have a conflict because we have changed the same line from `master` and `feature-branch`. We're going to resolve this conflict by combining the information from both versions:

```
user@somepc:~/workspace/project (feature-branch|REBASE 1/1)$ vim README.md
- Master change
- Feature branch change
++ Master and feature change combined
```

After resolving the conflict, we need to stage the conflicted file and tell `git` to continue the rebase:

```
git add README.md
git rebase --continue
```

If other conflicts occur during the rebase process, we need to resolve them in the same manner.

And voila! We've successfully rebased our feature branch and resolved the conflicts!

## Why do I have to resolve the same conflict multiple times during rebase?

There are cases when you might run into a conflict during rebase. And after you resolve this conflict, you proceed with the rebase process using `git rebase --continue`. Then git goes on to applying the next commit only to get paused by a rebase conflict once again. You open your editor and find a similar looking conflict to the one you just resolved. But why does this happen?

You can run into this situation if the history of your feature branch has significantly diverged from the history of your main branch.

#### Sample scenario

Assume the following history exists and the current feature branch is "feature":

```
      A---B---C feature
    /
D---E---F---G master
```

As you can see, in this example you have created your branch from point `E` in the history and started working on your feature - `A`, `B`, `C`.

#### Similarity index threshold

Let assume that you worked mainly on the file `src/app.js` in your feature branch and the history of `A`, `B` and `C` revolves around this file. In those three commits, you have introduced changes in `src/app.js` that made the similarity index higher. You might have seen a similar message after committing:

```
[feature 2394v0f] Apply SRP principle to app.js
rewrite src/app.js (81%)
```

This means that you've exceeded the similarity threshold and git now thinks your file has been "`rewritten`".

While you've been working on those huge changes on `src/app.js` in your feature branch, someone has introduced new changes in `master` which are depicted as commits `F` and `G` in this graphic. As you can see, those changes stem from `E` - the original version of the file, before the huge rewrite in `feature`. Let's assume that `F` and `G` also contain changes in `src/app.js`.

Now when you try to rebase `feature` onto `master`, git will start reapplying the commits in `feature` one by one starting with `A` (think `cherry-pick`ing each commit in `feature` and applying it on top of the latest version of `master`).

```
              A' feature
            /
D---E---F---G master
```

After initiating the rebase, git stops on the first commit `A` because of a conflict. `src/app.js` has significantly diverged from the original version `E` and git cannot resolve the differences automatically. You resolve the conflict taking into account the new changes from `master`. Then you proceed with the rebase using `git rebase --continue`, only to witness similar looking conflict to the one you just resolved.

Now if we stop and look at both graphics at this point, we'll understand why a similar conflict has appeared once again.

#### The reason

`B` originally emerges after `A`, but now we're trying to apply it on top of `A'`. `A'` is way different than `A` because we've incorporated the latest changes from `master`. Since we're applying `B` on top of `A'` which now has a new base - `G`, we see a similar looking conflict again. In the end, it boils down to `E` versus `G` and the similarity index.

This is how the final version of your feature branch should look like after resolving all conflicts:

```
              A'--B'--C' feature
            /
D---E---F---G master
```
