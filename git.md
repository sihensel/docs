
# How to use Git

Command | Meaning
--- | ---
git clone | clones the repo from the url
git add | adds all the specified files to the local git repo
git add -u | adds all _modified_ files to the local git repo
git commit -m "Commit Message" | Commit the changes with a message
git push | pushes the commits from the local repo to the remote repo 
git pull | pull all changes from the remote repo to the local repo


## Set up SSH with Git

### Generate a keypair

Generate the the ssh keypair (if you don't already have one).

```bash
ssh-keygen -t rsa -b 4096
```
The keys get stored in the `~/.ssh` direcotry.

### Work with your keys

Then, add the keys to the `ssh-agent`.
Make sure, `ssh-agent` is running:

```bash
eval "$(ssh-agent -s)"  # or
ssh-agent -s            # for windows
```

Then add the keys.

```bash
ssh-add ~/.ssh/id_rsa
```

Then, copy your public key (`~/.ssh/id_rsa.pub`) to Github.

```bash
cat ~/.ssh/id_rsa.pub # Linux
clip < ~/.ssh/id_rsa.pub # Windows
```

Test your connection:

```bash
ssh -T git@github.com
```

### Workign with repos

Change your directory to a local repo and run:

```bash
git remote set-url origin git@github.com:username/your-repository.git
```

Or clone a new repo with:

```bash
git clone git@github.com:username/your-repository.git
```

This way, ssh is enabled OOTB and you don't have to enter your password or change your repo any more.


# DEPRECATD - DON'T USE !!!

If you want Git to remember your username and password, use:

```bash
git config --global credential.helper store
```

After the next push, Git will store your credentials.

To disable the credential manager, run:

```bash
git config --global --unset credential.helper
```
