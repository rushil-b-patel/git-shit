<h1 align="center">
  Oh shit Git
</h1>
<h2 align="center">  
  A Guide to Fixing Common Git Mistakes
</h2>

 ## Oh shit, I did something terribly wrong, please tell me git has a magic time machine...!

```git
git reflog
# you will see a list of every thing you've
# done in git, across all branches!
# each one has an index HEAD@{index}
# find the one before you broke everything
git reset HEAD@{index}
# magic time machine
```
You can use this to get back stuff you accidentally deleted, or just to remove some stuff you tried that broke the repo, or to recover after a bad merge, or just to go back to a time when things actually worked. I use reflog A LOT. Mega hat tip to the many many many many many people who suggested adding it!

## Oh shit, I committed and immediately realized I need to make one small change...!

```git
# make your change
git add . # or add individual files
git commit --amend --no-edit
# now your last commit contains that change!
# WARNING: never amend public commits
```

This usually happens to me if I commit, then run tests/linters... and FML, I didn't put a space after an equals sign. You could also make the change as a new commit and then do rebase -i in order to squash them both together, but this is about a million times faster.

*Warning: You should never amend commits that have been pushed up to a public/shared branch! Only amend commits that only exist in your local copy or you're gonna have a bad time.*

## Oh shit, I need to change the message on my last commit...!
```git
git commit --amend
# follow prompts to change the commit message
```
Stupid commit message formatting requirements.

## Oh shit, I accidentally committed something to master that should have been on a brand new branch...!
```git
# create a new branch from the current state of master
git branch some-new-branch-name
# remove the last commit from the master branch
git reset HEAD~ --hard
git checkout some-new-branch-name
# your commit lives in this branch now :)
```
Note: this doesn't work if you've already pushed the commit to a public/shared branch, and if you tried other things first, you might need to `git reset HEAD@{number-of-commits-back}` instead of `HEAD~`. Infinite sadness. Also, many many many people suggested an awesome way to make this shorter that I didn't know myself. Thank you all!

## Oh shit, I accidentally committed to the wrong branch...!
```git
# undo the last commit, but leave the changes available
git reset HEAD~ --soft
git stash
# move to the correct branch
git checkout name-of-the-correct-branch
git stash pop
git add . # or add individual files
git commit -m "your message here";
# now your changes are on the correct branch
```
A lot of people have suggested using `cherry-pick` for this situation too, so take your pick on whatever one makes the most sense to you...!
```git
git checkout name-of-the-correct-branch
# grab the last commit to master
git cherry-pick master
# delete it from master
git checkout master
git reset HEAD~ --hard
```

## Oh shit, I tried to run a diff but nothing happened...!
If you know that you made changes to files, but `diff` is empty, you probably `add`-ed your files to staging and you need to use a special flag.
```git
git diff --staged
```
File under ¯\_(ツ)_/¯ (yes, I know this is a feature, not a bug, but it's fucking baffling and non-obvious the first time it happens to you!)
## Oh shit, I need to undo a commit from like 5 commits ago...!
```git
# find the commit you need to undo
git log
# use the arrow keys to scroll up and down in history
# once you've found your commit, save the hash
git revert [saved hash]
# git will create a new commit that undoes that commit
# follow prompts to edit the commit message
# or just save and commit
``````git
# find the commit you need to undo
git log
# use the arrow keys to scroll up and down in history
# once you've found your commit, save the hash
git revert [saved hash]
# git will create a new commit that undoes that commit
# follow prompts to edit the commit message
# or just save and commit
```
Turns out you don't have to track down and copy-paste the old file contents into the existing file in order to undo changes! If you committed a bug, you can undo the commit all in one go with  `revert`.

You can also revert a single file instead of a full commit! But of course, in true git fashion, it's a completely different set of fucking commands...

## Oh shit, I need to undo my changes to a file...!
```git
# find a hash for a commit before the file was changed
git log
# use the arrow keys to scroll up and down in history
# once you've found your commit, save the hash
git checkout [saved hash] -- path/to/file
# the old version of the file will be in your index
git commit -m "Wow, you don't have to copy-paste to undo"
```
When I finally figured this out it was HUGE. HUGE. H-U-G-E. But seriously though, on what fucking planet does `checkout --` make sense as the best way to undo a file? :shakes-fist-at-linus-torvalds:

## Clean up a fork and restart it from the upstream
```git
git remote add upstream /url-to-main-repo
git fetch upstream
git checkout master or main
git reset --hard upstream/master or main  
git push origin master/main --force 
```

## Fuck this noise, I give up.
```git
cd ..
sudo rm -r fucking-git-repo-dir
git clone https://some.github.url/fucking-git-repo-dir.git
cd fucking-git-repo-dir
```
For real though, if your branch is sooo borked that you need to reset the state of your repo to be the same as the remote repo in a "git-approved" way, try this, but beware these are destructive and unrecoverable actions!
```git
# get the lastest state of origin
git fetch origin
git checkout master
git reset --hard origin/master
# delete untracked files and directories
git clean -d --force
# repeat checkout/reset/clean for each borked branch
```
