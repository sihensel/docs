# Git

## Git Commands

Command | Meaning
--- | ---
git clone https://github.com/user/repo.git | clones the repo using https
git clone git@github.com:user/repo..git | clones the repo using ssh
git add | adds all the specified files to the local git repo
git add -u | adds all _modified_ files to the local git repo
git commit -m "Commit Message" | Commit the changes (a message is necessary)
git push | pushes the commits from the local repo to the remote repo 
git pull | pull all changes from the remote repo to the local repo


## Set up SSH with Git

### Generate a keypair

Generate the the ssh keypair (if you don't already have one).

```sh
ssh-keygen -t rsa -b 4096
```
The keys are located in the `~/.ssh` direcotry.

### Work with your keys

Add the keys to the `ssh-agent`.
Make sure, `ssh-agent` is running:

```sh
eval "$(ssh-agent -s)"  # Linux/Mac
ssh-agent -s            # for windows
```

Then add the keys.

```sh
ssh-add ~/.ssh/id_rsa
```

__Note__: This only works within the current shell. To make this setting persitent, add your ssh keys to the keyring.

Then, copy your public key (`~/.ssh/id_rsa.pub`) to Github.

```sh
cat ~/.ssh/id_rsa.pub # Linux
clip < ~/.ssh/id_rsa.pub # Windows
```

Test your connection:

```sh
ssh -T git@github.com
```

### Workign with repos

Change your directory to a local repo and run:

```sh
git remote set-url origin git@github.com:username/your-repository.git
```

Or clone a new repo with:

```sh
git clone git@github.com:username/your-repository.git
```

This way, ssh is enabled OOTB and you don't have to enter your password or change your repo any more.  
Alternatively, Github Tokens can also be used for authentication.

# DEPRECATD - DON'T USE !!!

If you want Git to remember your username and password, use:

```sh
git config --global credential.helper store
```

<<<<<<< HEAD
After the next push, Git will store your credentials.
=======
After the next push, Git will store your credentials.

To disable the credential manager, run:

```bash
git config --global --unset credential.helper
```
>>>>>>> f07381e3d2e46be54ed1d11bd497b2beea0564ad
