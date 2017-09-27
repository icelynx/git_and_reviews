# Git as version control and review tool -demo repository

Disclaimer: This repository is meant to be used as a demo repository for showing how to effectively use git as version control and review tool. Is's **not** an example but a playground to demonstrate different ways to use version control. This way of using git may not fit everyone and it's not the only way, just something that happens to work for me in my current project.

Below you can find a summary of what sort of stuff is demonstrated in this repository.

## Version control

So how do you use version control - in this case git - effectively? There are few topics to go through: committing changes, combining your changes to others', rewriting history(!), prettifying, stashing and publishing everything to the world.

### Committing

A commit should only contain one change. Everything in the commit has to somehow relate to that change. If you, or someone else, has to revert some functionality, they can simply revert that single commit without losing any other work. A commit may also break stuff, it doesn't have to be a complete change. However, you should always avoid breaking stuff if possible. Try to keep commits small and commit often. Think of them as save points in your code that you can go back to anytime you want. That way you can try out the next step anyway you like and if it doesn't work, go back to the working point in time.

Commit message is often overlooked thing in commits and usually just quickly typed whatever. This is wrong! Commit message should always tell what the commit does and why. They are written in present tense (because the commit does something, it didn't do anything). This gets you far but if you want make your commit messages next level, use the 50/72 rule. To summarize, write a 50 character summary of the commit, no more and (preferably) not less. Add an empty line and write the description of commit in more detail in the following lines that have maximum length of 72 characters. Git can recognize the first 50 characters as a summary, so when you use `git shortlog` you can see full summaries of every commit. When using `git log`, the full commit message is printed with 4 character right padding. So when you use line length of 72, full commit message is nicely readable from the standard 80-column terminal.

Further reading:
https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c

### Combining

You have a bunch of changes that you would like to publish but someone else has also made changes and published them before you. You now have to combine their changes and yours. You have two options: merge and rebase. I won't go into details here since the git pages (see below) explain the concepts very well.

In short merge finds the differences between two points of code, merges the changes if there are no overlaps, otherwise produces merge conflict of overlaps that the user has to resolve before continuing the merge. Merge combines commits in the order they have been made.

Rebase makes a patch of changes and applies it on top of changes where you are rebasing to, so it does not care about the order of commits. Rebase may also cause merge conflicts when applying the patch. They have to be resolved to complete the rebase. **Do not rebase published changes!** When you rebase, you are rewriting the commit history. If you are handling unpublished changes, no worries, it's just your commit history that is being rewritten. But if you rebase published changes, you will be rewriting public commit history which may cause commits made by others to disappear. **Do not rebase published changes!**

Rebase sounds dangerous, shouldn't we always use merge?! Well, no. Rebase is a very handy tool for keeping our developer environment up to date and skipping branching whenever someone else publishes changes on top of ours. Branching is a tool to take a separate path from the branch you are developing in. You can then work on your new branch while others work on some other branch and later combine them with merge. The problem with branching is you often would like to share published code as fast as possible in order to get that new utility everywhere and more importantly, to prevent merge conflicts that can be a hell to resolve. When working in a branch, you would have to merge the other branch into yours to get their code and vice versa. This is such a big drawback that branching should only be used when there are no good alternatives. For example, to create git pull requests (see Reviews).

Further reading:
https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging
https://git-scm.com/book/en/v2/Git-Branching-Rebasing

### Prettifying

Everyone makes mistakes every now and then. It can't be avoided. They are embarrassing but luckily there are tools to make them disappear! Note that these rely on rebase so we are rewriting history! Same rule applies: **do not rebase published changes**. One way do this is *amend* which adds current changes to the previous commit. Who hasn't forgotten to add tests or to remove debug logging or to add new files? If you have made a lot of commits that actually all relate to the same thing, you can combine them with *squash*. *pick* provides a way to rearrange unpublished commits and *reword* a way to rewrite commit message. All of these tools can be accessed through interactive rebase.

### Stashing

Ever wanted to try out something radical and not yet ready to commit but feared that you would destroy hours of work? Ever had to drop whatever you are doing to do that quick hotfix and you are still far from committing anything? Stashing provides a way to stash all your uncommitted changes into a container that can be applied on top of anything. So just stash your changes, reapply them without deleting the stash and try your radical new stuff! Or stash your changes, complete that hotfix and unstash your saved work. You can even use this in place of rebasing when you want to get the others' changes in your current branch and save yourself from having to rewrite history.

Please note that stashing isn't the same as committing your changes. Repository's commit history provides a clear way to read what has changed and why and also gives a way to revert some of those changes if need arises. Stashes only let you save your local development state for awhile, it won't make nice complete changes for everyone.

### Publishing

When you think you are ready publish your changes, make sure you are actually pushing everything and the code you are pushing works (run tests). Check that your commits are easy to understand, they are in right order and that there are no useless commits. Prettify your local commit history if necessary. Then just push the changes into repository/repositories and move onto getting your changes reviewed.

## Reviews

Disclaimer: In the context of this repository, we are talking of peer code reviews done on every piece of code pushed into master/release/production branch.

One way to handle reviews with git is to divide them in two cases:

1. Tiny or small changes that only contain one or few files or something very specific
  * Changes in configuration, hotfixes, fine tuning styles, change in how some function works, using already implemented features...
  * Everything should fit sensibly into one commit
2. Everything else
  * New feature, multiple files, refactors, complex changes in some functions...
  * You may have multiple commits

Whichever the case, if you are using project management software, one task should only contain one review! Otherwise the reviewer will have hard time understanding the big picture of what has actually been done and why.

### Tiny or small changes

1. Do a single commit (or squash/amend your commits into a single commit)
2. Rebase all the changes from master so you are on the top
3. Push changes
4. Give a link to the commit (you can find it from your git repository) to reviewer
  * Reviewer should review changes as soon as possible, preferably in the next few hours but at least during the same day

### Everything else

1. Commit your changes as you work, rebasing changes from master so you are always on the top
2. Finished? Make sure that you are on top of master
3. Create a new branch that starts from top of the master so that all your changes are included in the branch
4. Reset master branch to the commit **before** your first own commit so that master hasn't changed and all your commits go into the branch
  * You may, however, have done some changes that should go into master and not into the branch you are creating. Just make sure they are the first commits not pushed into master (for example, by using interactive rebasing) and reset master branch to the latest of those commits and not the last pushed commit on master
5. Push changes to the new branch (this will create the branch and push your commits into it)
  * If you also made some changes into master, push them as well
6. Go to your git repository and create a pull request
7. Give a link to the pull request to reviewer
  * Reviewer should review changes as soon as possible, preferably in the next few hours but at least during the same day. This is even more important in pull requests since the risk of merge conflicts increases the longer the changes are in another branch
8. After reviewer has given their review, address the comments and squash/merge your changes into master
