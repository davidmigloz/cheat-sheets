# Git

## Index

- [Feature branches](#feature-branches)
- [Release branches](#release-branches)
- [Hotfix branches](#hotfix-branches)
- [Fix .gitignore](#fix-gitignore)
- [Undo commit](#undo-commit)
- [Edit last commit message](#edit-last-commit-message)
- [Checking out pull requests locally](#checking-out-pull-requests-locally)
- [Clone a specific Git branch](#clone-a-specific-git-branch)

![Git model](http://nvie.com/img/git-model@2x.png)

## Feature branches

### Creating a feature branch

```bash
git checkout -b myfeature develop
```

### Incorporating a finished feature on develop

```bash
git checkout develop
git merge --no-ff myfeature
git branch -d myfeature
git push origin develop
```

> The `--no-ff` flag causes the merge to always create a new commit object, even if the merge could be performed with a fast-forward. 
> This avoids losing information about the historical existence of a feature branch and groups together all commits that together added the feature. 

## Release branches

### Creating a release branch

```bash
git checkout -b release-1.2 develop
./bump-version.sh 1.2
git commit -a -m "Bumped version number to 1.2"
```

> `bump-version.sh` is a fictional shell script that changes some files in the working copy to reflect the new version. 

### Finishing a release branch

```bash
git checkout master
git merge --no-ff release-1.2
git tag -a 1.2
git checkout develop
git merge --no-ff release-1.2
git branch -d release-1.2
```

## Hotfix branches

### Creating the hotfix branch

```bash
# Say version 1.2 is the current production release
git checkout -b hotfix-1.2.1 master
./bump-version.sh 1.2.1
git commit -a -m "Bumped version number to 1.2.1"
# Then, fix the bug and commit the fix
git commit -m "Fixed severe production problem"
```

### Finishing a hotfix branch

```bash
git checkout master
git merge --no-ff hotfix-1.2.1
git tag -a 1.2.1
git checkout develop
git merge --no-ff hotfix-1.2.1
git branch -d hotfix-1.2.1
```

> The one exception to the rule here is that, when a release branch currently exists, the hotfix changes need to be merged into that release branch, instead of develop. 
> Back-merging the bugfix into the release branch will eventually result in the bugfix being merged into develop too, when the release branch is finished. 
> (If work in develop immediately requires this bugfix and cannot wait for the release branch to be finished, you may safely merge the bugfix into develop now already as well.)

## Fix .gitignore

```bash
git commit -m "Changes..."
git rm -r --cached .
git add .
git commit -m "Fixed untracked files"
```

## Undo commit

### Undo the commit but keep your changes

```bash
git commit -m "Something terribly misguided" 
git reset HEAD~1
<< edit files as necessary >>
git add -A
git commit -m "The good commit" 
```

Graphically, where C is your HEAD and (F) is the state of your files:

```bash
   (F)          (F)
A-B-C    ==> A-B-C
    ↑          ↑
  master     master
```

### Undo the commit and remove your changes

```bash
git commit -m "Something terribly misguided" 
git reset --hard HEAD~1
<< edit files as necessary >>
git add -A
git commit -m "The good commit" 
```

Graphically:

```bash
   (F)        (F)
A-B-C    ==> A-B
    ↑          ↑
  master     master
```

## Edit last commit message

### Commit has not been pushed online

```bash
git commit -m "Bad commit message"
git commit --amend
<< edit message >>
```

Or directly:

```bash
git commit -m "Bad commit message"
git commit --amend -m "New commit message"
```

Or using interactive rebase:

```bash
# X is the number of commits to the last commit you want to be able to edit
git rebase -i HEAD~X
<< replace pick with reword before each commit message you want to change >>
<< in each resulting commit file, type the new commit message >>
```

> Make sure you don't have any working copy changes staged before doing this or they will get committed too. 
> If so, to unstage any changes you've made since you can do `git reset`.

### Commit has been pushed online

You need to force push the commit:

```bash
git commit -m "Bad commit message"
git commit --amend -m "New commit message"
git push --force example-branch
```

> Warning: force-pushing will overwrite the remote branch with the state of your local one.  
> Warning: be cautious about amending commits that you have already shared with other people.

## Checking out pull requests locally

```bash
git fetch origin pull/PR_ID/head:MYBRANCHNAME
git checkout MYBRANCHNAME
```

It can also be setted in the `.git/config`: [instructions](https://gist.github.com/kennethreitz/3709868).

## Clone a specific Git branch

```bash
git clone -b gh-pages --single-branch https://github.com/davidmigloz/go-bees.git gobees-web
```

# References
- [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)
- [.gitignore not working](http://stackoverflow.com/questions/11451535/gitignore-not-working)
- [How to modify existing, unpushed commits?](http://stackoverflow.com/questions/179123/how-to-modify-existing-unpushed-commits)
- [Changing a commit message](https://help.github.com/articles/changing-a-commit-message/)
- [How to undo last commit(s) in Git?](http://stackoverflow.com/questions/927358/how-to-undo-last-commits-in-git)
- [Checking out pull requests locally](https://help.github.com/articles/checking-out-pull-requests-locally/)
- [github clone from pull request?](http://stackoverflow.com/questions/14947789/github-clone-from-pull-request)
- [How to clone a specific Git branch?](http://stackoverflow.com/questions/1911109/how-to-clone-a-specific-git-branch)
