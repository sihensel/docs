# Git
A collection of Git commands and features that can be used as a quick reference.

## Git Commands

Command | Action
--- | ---
`git clone https://github.com/user/repo.git` | clones the repo using https
`git clone git@github.com:user/repo.git` | clones the repo using ssh
`git add` | adds all the specified files to the local git repo
`git add -u` | adds all _modified_ files to the local git repo
`git commit -m "Commit Message"` | Commit the changes
`git push` | pushes the commits from the local repo to the remote repo
`git push -u` | push the local branch and set it as upstream
`git pull` | pull all changes from the remote repo to the local repo
`git pull --rebase origin master` | rebases the local branch to the remote master
`git rebase -i HEAD~3` | start interactive rebase for the last 3 commits
`git rebase -i <commit hash>` | start interactive rebase starting __after__ the stated commit
`git log` | show the Git log and who made which commits
`git diff` | show all unstaged changes
`git remote -v` | shows all remote connections with their respective URL
`git remote add <name> <URL>` | adds a remote URL with \<name> to the branch

## Working with Branches

Set up a new local branch with `git switch -c branchName`. This will automatically switch to the new branch.  
To verify your current branch, use `git branch`, to switch use `git switch branchName`.  
Make your changes and commit them as usual. Push your commits with `git push origin branchName`.  
To pull down a branch from Github, use `git fetch origin branchName`.  
To delete a branch, use `git branch -d branchName` for local and `git push origin --delete branchName` for remote.  
`git branch -r` lists all remote branches.

Command | Action
--- | ---
`git switch -c new_feature` | Creates a branch called 'new_feature' and switches to it
`git branch` | shows the current branch
`git branch -a` | shows the local list of all known branches (run `git fetch` before)
`git remote update origin --prune` | update the local list of remote branches
`git push origin new_feature` | pushes the local branch to remote
`git fetch origin new_feature` | pulls down the branch 'new_feature' without merging it

## Amending commits
Sometimes it is necessary to edit a commit, either beacuse the message is incomplete or the author is incorrect.  
`git commit --amend` lets you edit the last commit message (this also works while rebasing).  
`git commit --amend --author="Author Name <email@address.com>" --no-edit` changes the author of the last commit message.  
It is also possible to `squash` or `fixup` commits with a rebase.

## Useful settings

`git config --global core.editor nvim`  
`git config --global commit.verbose true`

## Set up SSH with Git

### Generate a keypair
Generate the the ssh keypair (if you don't already have one).

```sh
ssh-keygen -t rsa -b 4096
```

Copy your public key (`~/.ssh/id_rsa.pub`) to Github.

### Workign with repos
Change your directory to a local repo and run:

```sh
git remote set-url origin git@github.com:username/your-repository.git
```

Or clone a new repo with:
```sh
git clone git@github.com:username/your-repository.git
```

This way, ssh is enabled out of the box and you don't have to enter your password or change your repo any more.  
Alternatively, Github Tokens can also be used for authentication.

## Cleanup Git Repo
If your repo contains a lot of deleted binary files (e.g. images, compiled objects, etc.), your `.git` directory can become quite big, since Git still keeps track of them. These objects can be cleaned from git with the help of [BFG](https://rtyley.github.io/bfg-repo-cleaner/). This short guide follows [this post](https://www.tikalk.com/posts/2017/04/19/delete-binaries-from-git-repository/).

To find the biggest files in your repo, use the [git-tools](https://github.com/ivantikal/git-tools) scripts.
This step is optional, but can provide good insights.

```sh
git clone https://github.com/ivantikal/git-tools.git
```

To get the 50 biggest files, run
```sh
./git-tools/clean-binaries/get_biggest_files_in_history.sh -r ./repo_with_binaries/ -n 50
```

This script will write the output to a `.txt` file.
Afterwards, we can use `BFG` to clean our repo. To run this script, install the `jre-openjdk-headless` package (on Arch).  
Download the file (either via the website or with `curl`)

```sh
curl -LO https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar
```

Clone your repo, using the `--mirror` prefix, which loads all refs from Github.

```
git clone --mirror git@github.com:user/repo.github
```

This will create a directory with the string `.git` at the end. This is our target for `BFG`.
To delete all refs for files bigger that 50 MB, run

```sh
java -jar bfg-1.14.0.jar --strip-blobs-bigger-than 50M your_repo.git
```

Now that Git realizes that it keeps track of unnecessary files, run
```sh
cd your_repo.git
git reflog expire --expire=now --all && git gc --prune=now --aggressive
```

As a last step, perform a `git push` to update the remote repo.
Make sure to delete all old local copies of the repo and a do fresh clone, to avoid reintroducing old binaries into the git history.
