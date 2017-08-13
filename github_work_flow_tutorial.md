# github work flow 总结

## create a new repo in ACCOUNT1
create a new repo in github website.


## clone to local (https)
**You can [cache your GitHub password in Git](https://help.github.com/articles/caching-your-github-password-in-git)** to avoid input username and password everytime
```
$ git clone https://github.com/debuggy/git_workflow_tutorial.git
(no require for any authorization)
Cloning into 'git_workflow_tutorial'...
```

If you use ssh link to clone, **you should [add your public ssh key in this ACCOUNT1](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)**. 

```
$ git clone git@github.com:debuggy/git_workflow_tutorial.git
Cloning into 'git_workflow_tutorial'...
Warning: Permanently added the RSA host key for IP address '192.30.255.112' to the list of known hosts.
Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

## add ssh url to remote repo (not recommanded)
##### 1. Check the remote repo. It will show https url because we clone with https.

```
$ (master) > git remote -v
origin	https://github.com/debuggy/git_workflow_tutorial.git (fetch)
origin	https://github.com/debuggy/git_workflow_tutorial.git (push)
```

##### 2. [check for existing SSH keys](https://help.github.com/articles/checking-for-existing-ssh-keys) and [generated a new SSH key](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#generating-a-new-ssh-key), then [Add your SSH key to the ssh-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#generating-a-new-ssh-key)

Here I already have a ssh key in ~/.ssh, so I just add following codes to config file:

```
Host *
 AddKeysToAgent yes
 UseKeychain yes
 IdentityFile ~/.ssh/id_rsa
```

##### 3. add ssh url to remote repo

```
$ (master) > git remote add sshOrigin git@github.com:debuggy/git_workflow_tutorial.git
```


## change something and create a new local branch

##### 1. **You should create a new branch first to prevent the change of master branch**
```
$ (master) > git checkout -b dev
```
##### 2. add some lines in README.md, add a new file dev.txt.**uncommit change in master and commit it in new branch**

* first do some changes in master branch

```
$ (master) > vim README.md  ## do some changes in master
------
$ (master) > echo 'This is a test for dev branch.' > dev.txt ## create a new file
------
$ (master) > git status     ## check the status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)

dev.txt

no changes added to commit (use "git add" and/or "git commit -a")
------
~/xx (master) > git commit -a -m 'first commit'  ## git commit command cannot commit untracked files
------
~/xx (master) > git status 
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
Untracked files:
  (use "git add <file>..." to include in what will be committed)

dev.txt

nothing added to commit but untracked files present (use "git add" to track)
---
$ (master) > git add dev.txt ## add untracked file
---
$ (master) > git commit -m 'second commit' ## commit
```

* then you decide to uncommit these changes and move it to new branch dev

```
$ (master) > git lg  ##check the commit log
* e5e48ed - (HEAD -> master) second commit (5 seconds ago) 
* 0c155a5 - first commit (10 minutes ago) 
* 41876c8 - (origin/master, origin/HEAD) Initial commit (3 hours ago) 
------
$ (master) > git reset HEAD~2 ##move 2 commits before HEAD and leave the changes unstaged (If you add --soft, the changes will be staged but uncommited. If you add --hard, the changes will be discarded.)
------
$ (master) > git checkout dev ##bring the changes to new branch, and keep master commit history clean
```

##### 3. commit the changes in new branch

```
$ (dev) > git commit -a  ## **this command only commit the changes in README.md and cannot commit untracked file dev.txt**
$ (dev) > git add README.md
```

## push local branch dev to remote server (HTTPS)

##### 1. push local branch to remote

```
$ (dev) > git push origin dev # this command will push local branch dev to create a new branch in remote server
```

##### 2. set up-stream-to origin/dev

use command ```git fetch origin``` to update the remote branches and then set local dev to track remote dev or create a new branch track dev

```
$ (dev) > git fetch origin
$ (dev) > git branch --set-upstream-to=origin/dev dev ## set up stream
$ (master) > git checkout --track origin/dev ## create a new branch dev to track origin/dev (a local dev branch must not exist)
$ (dev) > git checkout -b new_dev origin/dev ## create a new branch new_dev to track origin/dev
$ (dev) > git branch -u origin/dev ## make current branch track origin/dev
```

##### 3. update the branch dev using git pull. 

**It is highly recommanded create a new branch anytime when you want to make some changes and use PR to merge it to dev**

```
$ (dev) > git pull
$ (dev) > git checkout -b feature1
... (add new feature1)
$ (feature1) > git commit -a -m 'add new feature 1
```

##### 4. locally merge feature1 to dev

There are 3 types of merge: fast-forward merge, three-way merge and merge conflict. Here is fast-forward merge.

```
$ (feature1) > git checkout dev
$ (dev) > git merge feature1
```

##### 5. push dev to remote or submit a PR in github (latter is prefered)

Push dev to remote directly. **Be careful when push some changes to an existing remote branches! It's hard to reset and prone to mess the commit history!**
```
$ (dev) > git push origin dev
```

Submit a PR: First push feature1 to remote then add a new PR to dev. **This is highly recommended compared with push from local**

```
$ (dev) > git push origin feature1
```
For the contributor: First pull this feature and merge to dev locally and make some tests, then make decide whether make a merge or not. (squash and merge is prefered to keep history clean)

## fork this repo in ACCOUNT2 and keep synced
##### 1. sign in ACCOUNT2 on github.com and fork this repo

##### 2. clone this repo to local 

```
$ git clone https://github.com/zyddora/git_workflow_tutorial.git
```
##### 3. add upstream to keep synced

```
$ (master) > git remote add upstream https://github.com/debuggy/git_workflow_tutorial.git
$ (master) > git remote -v
origin	https://github.com/zyddora/git_workflow_tutorial.git (fetch)
origin	https://github.com/zyddora/git_workflow_tutorial.git (push)
upstream	https://github.com/debuggy/git_workflow_tutorial.git (fetch)
upstream	https://github.com/debuggy/git_workflow_tutorial.git (push)
$ (master) > git fetch upstream
$ (master) > git branch -va
* master                    41876c8 Initial commit
  remotes/origin/HEAD       -> origin/master
  remotes/origin/master     41876c8 Initial commit
  remotes/upstream/dev      60bc6bb add feature2 (#2)
  remotes/upstream/feature1 0f6953d some revise for feature1
  remotes/upstream/master   41876c8 Initial commit
$ (master) > git checkout -b dev
$ (dev) > git merge upstream/dev
$ (dev) > git push origin dev
```
##### 4. add another feature by ACCOUNT2 and push to remote origin
```
$ (dev) > git fetch upstream
$ (dev) > git merge upstream/dev
$ (dev) > git checkout -b feature3
$ (feature3) > ...(do some changes)
$ (feature3) > git commit -m 'add feature3'
$ (feature3) > git checkout dev
$ (dev) > git fetch upstream
$ (dev) > git merge upstream/dev
$ (dev) > git merge feature3
$ (dev) > ...(do some checks)
$ (dev) > git push origin feature3

```
##### 5. add a pull request in github
base: ACCOUNT1/dev  compare: ACCOUNT2/feature3

## key chain
If you [cache your GitHub password in Git with Keychain Access](https://help.github.com/articles/caching-your-github-password-in-git). **You must [delete the github password in Keychain Access](https://help.github.com/articles/updating-credentials-from-the-osx-keychain) when you want to use  another github account.**

