# Git

## Git Commands

Command | Action
--- | ---
git clone https://github.com/user/repo.git | clones the repo using https
git clone git@github.com:user/repo.git | clones the repo using ssh
git add | adds all the specified files to the local git repo
git add -u | adds all _modified_ files to the local git repo
git commit -m "Commit Message" | Commit the changes
git push | pushes the commits from the local repo to the remote repo
git push -u | push the local branch and set it as upsteam
git pull | pull all changes from the remote repo to the local repo
git pull --rebase origin master | rebases the local branch to the remote master
git rebase -i HEAD~3 | start interactive rebase for the last 3 commits
git rebase -i \<commit hash> | start interactive rebase starting __after__ the stated commit
git log | show the Git log and who made which commits
git diff | show all unstaged changes
git remote -v | shows all remote connections with their respective URL
git remote -v add \<name> \<URL> | adds a remote URL with \<name> to the branch

## Working with Branches

Set up a new local branch with `git checkout -b brachName`. This will automatically switch to the new branch.  
To verify your current branch, use `git branch`. To switch branches use `git checkout branchName`.  
Make your changes and commit them as usual.  
Push your commits with `git push origin branchName`.  
The branch can now be merged via pull request on Github and the branch can be deleted.  
To pull down a branch from Github, use `git fetch origin branchName`. Now you can start working on that branch.

Command | Action
--- | ---
git switch -c new_feature | Creates a branch called 'new_feature' and switches to it
git branch | shows the current branch
git switch main | switches to the branch 'main'
git push origin new_feature | pushes the local branch to remote
git fetch origin new_feature | pulls down the branch 'new_feature' without merging it

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
