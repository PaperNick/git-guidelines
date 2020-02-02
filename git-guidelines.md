# Git guidelines


### Feature Branch Workflow

We should prefer the simple **Feature Branch Workflow** when applicable. You can find more info about this workflow here:

https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow


### Alternative Gitflow Workflow for more complex scenarios

In case we want to have a finer control over the process what gets merged into production, we should use **Gitflow workflow**:

https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow

### Which workflow to use?

When managing a project, it's recommended to start with the simpler **Feature Branch Workflow**. If the projects grows and requires finer control over the release process, switch to **Gitflow Workflow**.

### Stability of the main branch

The `master` branch should always be in valid state, meaning that at every point in the history of `master`, the project should build, or the tests should be passing.

### Making changes

If you want to apply changes to the main branch (typically `master`), you first need to create a branch out it and make your changes there. Branches created out of the main branch are called **feature branches**.

Switching to the main branch:

```
git switch master
```

For older versions of git:

```
git checkout master
```

Getting the latest changes:

```
git pull
```

Creating a new feature branch off of the main branch:

```
git switch -c create-login
```

For older versions of git:

```
git checkout -b create-login
```

Adding changes, commiting and pushing to the remote repository:

```
git add src/api.ts
git commit
git push origin create-login -u
```

### Convetion for commit messages

The commit messages should briefly describe the changes made in the given commit. They should be in present tense. Try to keep up to 70 characters per line.

Examples:

```
Add instructions for creating a production build from Docker
```

```
Use OpenStreetMap for the address location
```

```
Change the section id because it's hidden by AdBlockers
```

```
Make the live stream iframe section responsive
```

### Atomicity of commits

When making code changes, you want to make commits that are generally smaller and that encompass only one irreducible feature, fix, or improvement. That way, it's easier to find bugs by navigating into the project's history and `git bisect` can be utilized.

The other benefit of atomic commits is that it's easier to roll them back when necessary.

**Example:**

I need to rename one of the members of the enumeration `PaymentType` from `CRYPTO` to `TOKEN`. In order to keep the **atomic commits** rule, I must do this change in **1 commit** only, not 2 or more.

### Single resposibility for commits

Keep each logical change into a separate commit. For example, if you want to fix the login form, and also want to change the layout for links in the navigation, you'd generally do this in 2 separate commits rather than 1.

As a rule of thumb, if you find youself using the word **and** in the commit message, you most likely need to split the changes into 2 separate commits.


### Pull requests (PRs)

When creating a new pull request, it's mandatory to link the task from ticketing system in the description.

If the PR is more complex, you are encouraged to provide a short summary of the changes made.

### Merging Pull requests

When the PR is approved and ready to merge, before integrating it into the main branch (`master`), it's mandatory to rebase it against the latest version of the main branch before merging.

If you're not familiar with `rebase`, you can learn more about it here:

https://www.atlassian.com/git/tutorials/merging-vs-rebasing

##### For example:

Main branch - `master`

Feature branch - `create-login`

1. Switch to `master` and get the latest changes

    ```
    git switch master
    git pull
    ```

2. Switch to the feature branch and rebase it onto the latest version of `master`

    ```
    git checkout create-login
    git rebase master
    ```

3. Verify that the changes after rebase are still intact

    ```
    git diff origin/create-login
    ```

4. After verifying that the rebase was successful, push the branch to remote.

    Here we're passing the `-f` flag which means force push. This will discard all change on the remote branch, and apply your local rebased changes.

    ```
    git push origin create-login -f
    ```

    If your local branch tracks its remote counterpart, you can simply type:

    ```
    git push -f
    ```

5. Now the PR can be merged into `master`


### Keep your feature branches up-to-date

It's important to keep your feature branches up-to-date with the main branch. That way, you ensure that the histories of both branches won't diverge significantly. This means less conflicts to resolve. Use **rebase** when synchronizing the latest changes.

If you're not sure why `rebase` should be used to synchronize changes, instead of `merge`, refer to the Rebase FAQ document in this repository.
