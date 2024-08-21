## Saving changes in Git
### Git init
https://www.atlassian.com/git/tutorials/setting-up-a-repository/git-init 

### Git clone
```
    git clone <https://name-of-the-repository-link>
```
https://www.atlassian.com/git/tutorials/setting-up-a-repository/git-clone

### Git commit
https://www.atlassian.com/git/tutorials/saving-changes/git-commit

### Git branch
**Creating a new branch:**
```
   git branch <branch-name>
```
**Viewing branches:**
```
  git branch 
  git branch --list
```
**Deleting a branches:**
```
    git branch -d <branch-name>
```
### Git checkout
We use git checkout mostly for switching from one branch to another. We can also use it for checking out files and commits.
```
    git checkout <name-of-your-branch>
```
**There is also a shortcut command that allows you to create and switch to a branch at the same time:**
```
    git checkout -b <name-of-your-branch>
```
### Git status
```
git status
```

### Git Add
To add a single file:

```
git add <file>
```
To add everything at once:

```
git add -A
```

### Git commit
Git commit is like setting a checkpoint in the development process which you can go back to later if needed.

We also need to write a short message to explain what we have developed or changed in the source code.

```
git commit -m "commit message"
```
**Important: Git commit saves your changes only locally.**

### Git push
After committing your changes, the next thing you want to do is send your changes to the remote server. Git push uploads your commits to the remote repository.
```
git push <remote> <branch-name>
```
However, if your branch is newly created, then you also need to upload the branch with the following command:
```
git push --set-upstream <remote> <name-of-your-branch>
```
__OR__
```
git push -u origin <branch_name>
```
**Important: Git push only uploads changes that are committed.**
### Git pull
```
git pull <remote>
```
### Git revert
Sometimes we need to undo the changes that we've made. There are various ways to undo our changes locally or remotely (depends on what we need), but we must carefully use these commands to avoid unwanted deletions.

A safer way that we can undo our commits is by using **git revert**. To see our commit history, first we need to use ```git log -- oneline:```

Then we just need to specify the hash code next to our commit that we would like to undo:
```
git revert hash code
```
### Git merge
When you've completed development in your branch and everything works fine, the final step is merging the branch with the parent branch (dev or master). This is done with the git merge command.

Git merge basically integrates your feature branch with all of its commits back to the dev (or master) branch. It's important to remember that you first need to be on the specific branch that you want to merge with your feature branch.

For example, when you want to merge your feature branch into the dev branch:

**First you should switch to the dev branch:**

```
git checkout dev
```
**Before merging, you should update your local dev branch:**

```
git fetch
```
**Finally, you can merge your feature branch into dev:**

git merge <branch-name>
Hint: Make sure your dev branch has the latest version before you merge your branches, otherwise you may face conflicts or other unwanted problems.

For More:
https://git-scm.com/docs 