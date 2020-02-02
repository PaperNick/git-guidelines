# Git Rebase FAQ


### What are the benefits of rebasing multiple times a day?

Let’s assume that the `master` branch is really active and it goes through a lot changes all the time. If I’m working on a file which is changed by other colleagues throughout the day and I pull and rebase from `master` a couple a times a day, the probability of `git` handling and resolving a conflict automatically is very high. That’s because the changes are continually integrated into my version of the file. This means that I won’t have to resolve any conflicts manually (best case scenario).

Now let’s picture the opposite situation. Everything so far stays the same but I synchronize my feature branch not as frequently – 1 time in a couple of days for example. When I finally decide to pull and rebase to synchronize my branch, the probability of me being in conflict with the file which is on `master` is a lot higher. That’s because the versions of the file on those 2 branches have significantly diverged. This means that I'll probably have to resolve any possible conflicts manually.

**Tip:** Rebase a couple of times a day to prevent bigger conflicts at the end of the day.


### But I can still integrate changes from `master` in my branch by using the default `merge` strategy. How is `rebase` any different?

By using the default `merge` strategy, you only append changes on top of your existing ones. That’s also why this strategy is not considered destructive. On the contrary side, rebase constructs your current history from a new base (that’s why it’s called rebase) – in our case the latest version of `master`. Git will attempt to reapply each commit on top of the new base - in our case the tip of the `master` branch. The result from both operations in terms of line changes should be identical at the end.

In terms of git history, things are going to be quite different. The branch which used the default `merge` strategy is going to be cluttered with commits which came from the upstream branch - `master`. If you happened to pull and merge to synchronize your branch more than once, this would result in more noise in return - more merge commits containing the changes you integrated. For a bystander looking at your branch, this can be distracting since they want to review only the changes you originally introduced.

Rebase helps reduce the noise from synchronizing by reapplying your changes on top of the latest version of the `master` branch. This means that there won't be any merge commits, and from an observer's perspective this means less noise and more focus on the actual history of changes.


### Smaller git project size

Another benefit for using rebase is that your git project is going to be smaller in size. By using the `merge` strategy, additional commits with irrelevant information are kept into the history of the project. Those additional commits point to their respective git objects which hold their actual content. By eliminating those "sync" commits, we're storing less git objects and saving more space!


### Why is rebase destructive?
Earlier we mentioned that the `merge` strategy is not destructive whereas `rebase` is. What this means that with each rebase you're writing a new history. You can verify this by looking at the commit hashes before and after a rebase.


### When should you use rebase?

A good rule of thumb is to always rebase on your local branches which aren't used by anybody else. If somebody else is using your branch for collaborative work, you should be using the `merge` strategy which is not destructive. That way you won't break the other person's history.


### How do I resolve a conflict that has occurred during rebase?

Refer to the [Git Rebase resolving conflicts](git-rebase-resolving-conflicts.md) wiki page for more details.


### Pushing my rebased branch leads to an error.

It's very likely to receive an error when you try to push your branch in the upstream repository after a successful rebase. You should see a similar error:
```
To https://host/user/repo-name.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'https://host/user/repo-name.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

This error occurs because you've rewritten your history after successfully rebasing. Since your local history is completely different from the history of your branch on the upstream repository, git doesn't know what to do and decides to show this error. When pushing, git assumes that the history can be fast-forwarded, meaning that it expects consecutive changes which can nicely fit on top of one another. But rebasing rewrites everything from scratch and that's why this error is shown (we mentioned that the commit hashes become completely different after rebasing). Git won't assume anything and it will ask you to take action manually. The error message suggests that you pull to reconcile the differences. But if you pull, this will result in an extra merge commit and two sets of commits that contain the same changes (the original ones, and the ones from your rebased branch). Needless to say, this is a very confusing situation which we want to avoid.


### So how do I push my rebased branch?

Instead of pulling using the default `merge` strategy and pushing, you can make git overwrite the changes on the upstream repository. But before we get to that, it's a good habit to make a `git diff origin/branch-name` and ensure that everything is exactly as you expect. After you've verified that everything is fine, use `git push --force` or the short version `git push -f` to overwrite the upstream branch. Keep in mind that `git push --force` is destructive because it discards the commits from branch on the upstream repository.
