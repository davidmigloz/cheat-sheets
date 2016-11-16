# Git

![Git model](http://nvie.com/img/git-model@2x.png)

## Feature branches

### Creating a feature branch

```bash
$ git checkout -b myfeature develop
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

# References
- [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)
