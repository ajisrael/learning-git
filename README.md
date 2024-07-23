# Learning Git

## Commands

### Basics

Covers about 80% of what you need.

#### Add

```bash
git add <path-to-file | pattern>
```

Adds zero or more files to index (staging area)

#### Commit

```bash
git commit -m '<message>'
```

Commits changes present in index

#### Status

```bash
git status
```

Describes the state of the repo. Inclueds tracked, staged, and untracked changes

### Config

Configuration is just a file. (Located at `.git/config` for local config)

#### Add a key value to git config

```bash
git config --add --global <key> "<value>"
```

Example:

```bash
git config --add --global user.name "Alex Israels"
```

#### View any value of git config

```bash
git config --get <key>
```

Example:

```bash
git config --get user.name
```

#### List config values

```bash
git config --list
```

#### Filter config values based on expression

```bash
git config --get-regexp <expression>
```

#### Remove (Unset) a config key value pair

##### Unset matching key

```bash
git config --unset <key>
```

Note: You cannot unset keys with multiple values with this command

##### Unset all matching keys

```bash
git config --unset-all <key>
```

#### Remove Namespace

```bash
git config --remove-section <key>
```

### Commits and SHAs

#### Log

```bash
git log --graph --decorate
```

Shows commit history with SHAs

Note: `--decorate` is only required when running `git log` and piping to a file to maintain the pretty syntax

```bash
git log --oneline
```

Really simplifies the logs to show you commits on a branch

```bash
git log --oneline <branch-name>
```

You can also append a branch name if you want to see the logs of a different branch than the one you're currently on.

```bash
git log -S "search"
```

You can also use the `-S` parameter to search for specific logs

#### Cat-File

Way to inspect files within git's data store

```bash
git cat-file -p <some-sha>
```

Echo out contents of sha

You can follow the trail from the commit, to the tree, to the blob, to get the file contents

### Repository

#### Initialize repository

```bash
git init
```

Creates a new `.git` folder for your git repo inside the current directory

#### Create new repo on command line

The following is from github itself not the FEM course

```bash
echo "# REPO_NAME" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:ajisrael/REPO_NAME.git
git push -u origin main
```

### Branching

#### Create a new branch

```bash
git branch <branch-name>
```

or 

```bash
git chechout -b <branch-name> 
```

#### Switch to another branch

```bash
git switch <branch-name>
git checkout <branch-name>
```

`checkout` is supposedly more versitile, and more commonly used.

#### Delete a branch

```bash
git branch -d <branch-name>
```

You can change `-d` to `-D` to --delete --force.

#### List all branches

```bash
git branch -a
```

#### Merge branches

Attempting to combine two histories that may or may not have diverged. There is a common commit piont between the two, referred to as the best common ancestor (or "merge base" as defined in the docs).

Git merges the sets of commits on to the merge base and creates a new commit at the tip of the branch that is being merged on with all the changes combined into one commit.

There is a `target` branch, which is the one that you are on, and a `source` branch that you want to merge into your current branch.

```bash
git merge <source-branch-name>
```

Run the following command to see the changes done by the `merge` command in more detail

```bash
git log --oneline --decorate --graph --parents
```

Example Output:

```
*   7df8e11 d10ee81 67100a8 (HEAD -> trunk-merge-foo) Merge branch 'foo' into trunk-merge-foo
|\  
| * 67100a8 f8b02df (foo) C
| * f8b02df 07dc767 B
* | d10ee81 08bee70 (trunk) E
* | 08bee70 07dc767 D
|/  
* 07dc767 A
```

The merge commit has two parents (d10ee81 67100a8 in the example) one from the head of each branch from the merge. This only happens if both branches have diverged. This is called a 3 way merge.

The following example shows what happens when the tip of the `trunk` branch is the base of the deveating branch `bar` (in this case). Notice there is no commit message for the merge. This is called a Fast-forward merge.

```
* 241c2a0 e768479 (HEAD -> trunk, bar) Y
* e768479 d10ee81 X
* d10ee81 08bee70 E
* 08bee70 07dc767 D
* 07dc767 A
```

#### Rebase

Example setup:

```
   B --- C                  foo
 /
A --- D --- E --- X --- Y   trunk
```

Rebase allows us to convert that setup to this in a single command:

```
                            B --- C foo
                          /
A --- D --- E --- X --- Y           trunk
```

So we change branch `foo` to point to `Y` instead of `A`

That is all that rebase does here. It updates the commit where the branch originally points to. This also allows us to do a fast forward merge when we decide to merge foo onto trunk.

What `rebase` does (basic steps):
1. execute `git rebase <targetbranch>` from `<currentbranch>`
2. checkout the latest commit on `<targetbranch>`
3. play one commit at a time from `<currentbranch>`
4. once finished, will update `<currentbranch>` to the the latest commit of `<targetbranch>`

Note: This does create "new" commits, in that their shas do change as shown below.

Commits before rebase
```
67100a8 C
f8b02df B
07dc767 A
```

Commits after rebase
```
4a9080f C
8cc2582 B
241c2a0 Y
e768479 X
d10ee81 E
08bee70 D
07dc767 A
```

### Commit Management

#### Reflog

See terms for definition.

Example use case, file recovery:

We create a new branch off of trunk called baz and add a commit that contains a new file called `baz.md`

```bash
git checkout trunk
git checkout -b baz
echo "baz" >> baz.md
git add .
git commit -m "baz"
```

We then go back to trunk and delete the baz branch

```bash
git checkout trunk
git branch -D baz
```

Using `reflog` we can recover our missing file from that branch.

```bash
git reflog -5 # last 5 logs
```

This command outputs the following:

```bash
241c2a0 HEAD@{0}: checkout: moving from baz to trunk
4a5671b HEAD@{1}: commit: baz
241c2a0 HEAD@{2}: checkout: moving from trunk to baz
241c2a0 HEAD@{3}: checkout: moving from foo to trunk
67100a8 HEAD@{4}: checkout: moving from trunk to foo
```

So we have the hash `4a5671b` that we can use `git cat-file -p` to follow down to the blob of our missing file.

```bash
git cat-file -p 4a5671b
```

Will output:

```bash
tree c6468b4bafe23dd40eaabda3dcccc6793d015736
parent 241c2a00972029883d79da5b3229f854f5e8db8c
author Alex Israels <43039187+ajisrael@users.noreply.github.com> 1721399277 -0400
committer Alex Israels <43039187+ajisrael@users.noreply.github.com> 1721399277 -0400

baz
```

So we take the sha from the tree to get to the blobs:

```bash
git cat-file -p c6468b4bafe23dd40eaabda3dcccc6793d015736
```

Outputs:

```bash
100644 blob 2339e24260ba1b42505c8e48636bb4ba2ed52b68    README.md
100644 blob 16858db7afb62f3e027d8f9379085d3567bcac62    bar.md
100644 blob 76018072e09c5d31c8c6e3113b8aa0fe625195ca    baz.md
```

And finally,

```bash
git cat-file -p 76018072e09c5d31c8c6e3113b8aa0fe625195ca
```

Gives us the contents of `baz.md`

```bash
baz
```

OR you can just merge the sha of the commit

```bash
git merge 4a5671b
```

NOTE: This will merge all history and diversions if there are commits between the latest of trunk and the commit you are merging. To just bring in the commit, may want to try cherry picking.

#### Cherry Pick

```bash
git cherry-pick <commit-sha>
```

### Remote Git

#### Remote

Git allows for distributed version control. We can add a remote to a repo with the following command.

Note: It is considered best practice to name the authoritative remote repository `origin`
      The `uri` can be another repo on your machine, doesn't necessarily have to be a url or ssh

```bash
git remote add origin <uri>
```

You can see your remotes and their paths with the following command:

```bash
git remote -v
```

#### Fetch

Allows you to fetch the state from the remote repository.

```bash
git fetch
```

This updates your refs in `.git/refs/remotes/origin` (or whatever you named your remote) but doesn't merge changes to your local branch.
You can see this by running `git log` after fetching, to see you don't have commits that are on the remote.

If you do want to see the log of a fetched remote branch you can with the following command:

```bash
git log <remote-name>/<branch-name>
```

An example of this would be:

```bash
git log origin/trunk
```

To get the changes from the remote, just merge them with a local branch. So for example if you were on your local `trunk` branch you could run the following to update it with the commits from the remote `trunk` branch:

```bash
git merge origin/trunk
```

#### Pull

Pull is a convenience that allows you to fetch and merge changes from a remote branch to a local branch. 

```bash
git pull <remote> <branch>
```

If you don't specify a remote and local branch it will just use the current branch you're on and it's remote branch it's tracking if it is setup.

To setup tracking run the following command:

```bash
git branch --set-upstream-to=origin/trunk trunk
```

Also if you prefer to use rebase instead of merge on your pull you can add the `--rebase` flag to your pull command or change your config:

```bash
# add flag
git pull --rebase
```

```bash
# update config
git config --add --global pull.rebase true
```

#### Push

Allows you to update commits/changes from your local branch to a tracked remote branch.

Note: Using `origin` git will automap these for you when you checkout a local branch after setting a remote.

```bash
git push
```

Under the hood this is just doing a `fetch` and then a `merge` on the remote repo. The opposite of `pull`.

### Conflict Resolution

#### Stash

Stash changes with following command

```bash
git stash
```

You can also add a message

```bash
git stash -m "message"
```

You can list stashes

```bash
git stash list
git stash show [--index <index>]
```

Pop changes from stash stack with:

```bash
git stash pop
```

Or at an index

```bash
git stash pop --index <index>
```

#### Merge Conflicts

Merge conflicts occur when there are the same line has diverged two ways in commits.

A new commit needs to be created after resolving the merge conflict.

When you create a merge conflict resolving commit, you will need to push that up to your remote repo to prevent having to deal with merge conflicts in the future.

Example History for when merge conflict resolutions aren't commited:

```bash
*   9ff2f04 Merge branch 'trunk' of ../hello-git into trunk
|\  
| * 5f5215b a change to bar
* | 68ac697 merged in upstream
|\| 
| * 7b0d4a0 A + 1
* | d308dbe A + 2
|/  
* 05c6c28 greatest change
* 5385c3c upstream change 2
* 28443b8 upstream change
* daea46e A remote change
* 399f77b baz
* 241c2a0 Y
* e768479 X
* d10ee81 E
* 08bee70 D
* 07dc767 A
```

#### Rebase Conflicts

Reminder, to pull with rebase use the following command:

```bash
git pull --rebase
```

Here is an example conflict:

```bash
<<<<<<< HEAD
A + 3
=======
A + 4
>>>>>>> 2132f47 (change from remote for a wonderful rebase conflict)
```

Notice that the HEAD is pointing to the upstream change. This is because `rebase` pulls the changes from the remote down and then plays the commits over the new HEAD one at a time.
So the conflict was introduced by our local commit `2132f47` being played over the new HEAD pointer.

After resolving the conflict we need to add our changes to the index and then continue through the rebase process onto the next commit if there is one:

```bash
git add .
git rebase --continue
```

In this example we chose the upstream's change to resolve the conflict.
As such, there is no additional change from the perspective of the upstream repo on the file so `git status` doesn't list the previously conflicting file anymore.

Simple history change though:

```bash
* 4ee5d17 change from remote for a wonderful rebase conflict
* fbcc586 a change from hello-git for rebase conflict. enjoy your day
*   9ff2f04 Merge branch 'trunk' of ../hello-git into trunk
|\  
| * 5f5215b a change to bar
* | 68ac697 merged in upstream
|\| 
| * 7b0d4a0 A + 1
* | d308dbe A + 2
|/  
* 05c6c28 greatest change
* 5385c3c upstream change 2
* 28443b8 upstream change
* daea46e A remote change
* 399f77b baz
* 241c2a0 Y
* e768479 X
* d10ee81 E
* 08bee70 D
* 07dc767 A
```

You will notice though that the `2132f47` commit is no longer a part of our history. You can still find it in the reflogs, but in resolving the conflict, that change was "lost".

If we did decide to take the local change (not HEAD when doing rebase) then a change would be present and there would be a new commit as shown below:

```bash
* 7eddc31 A + 6
* ade24b9 change from remote for a wonderful rebase conflict
* b9cee18 A + 5
* fbcc586 a change from hello-git for rebase conflict. enjoy your day
*   9ff2f04 Merge branch 'trunk' of ../hello-git into trunk
|\  
| * 5f5215b a change to bar
* | 68ac697 merged in upstream
|\| 
| * 7b0d4a0 A + 1
* | d308dbe A + 2
|/  
* 05c6c28 greatest change
* 5385c3c upstream change 2
* 28443b8 upstream change
* daea46e A remote change
* 399f77b baz
* 241c2a0 Y
* e768479 X
* d10ee81 E
* 08bee70 D
* 07dc767 A
```

#### Reuse Recorded Resolutions

Because `rebase` will replay commits, if another change happened on remote and we rebased that again we will have to deal with the same conflict once again.

```bash
# Original config: C and E conflict
  B --- D --- E   # local
 /
A --- C           # remote

# git pull --rebase, F resolves conflict (took local change)
        B --- D --- E --- F   # local
       /
A --- C                       # remote

# New commit G on remote in same conflicting file as CE conflict
        B --- D --- E --- F   # local
       /
A --- C --- G                 # remote

# git pull --rebase, old conflict between C and E needs to be addressed when E is replayed
              B --- D --- E --- F   # local
             /
A --- C --- G                       # remote
```

If only we could reuse recorded resolutions to avoid having to resolve this again.... introducing `rerere` (REuse REcorded REsolutions)

To enable `rerere` run the following config command:

```bash
git config --add rerere.enabled true
```

Now the resolution that was recorded will continue to play and you don't need to resolve each time.

#### Ours vs Theirs

Ours is the change of the current branch 

Theirs is the change of the incomming branch 

Note: Remember that `rebase` checks out the branch with which you are pulling and then plays commits on top of them. So, ours is the remote branch and theirs is the local branch in that instance

To select theirs or ours (not have to manually resolve changes in each and every conflicting file) you can use the following checkout command:

```bash
git checkout --ours README.md #use "ours" change
git checkout --theirs README.md #use "theirs" change
```

It's an all or nothing selection though, so if another file was changed on `theirs`, and you selected ours, you would lose that change.

## Terms

### Commit

A point in time reprsenting the project in its entirety

The sha that represents a commit, 40 hexadecimal characters 0-f, calculated from contents of change, author, time, and more

### Index

Interchangable with staging area. This is the area in which you hold the changes you intend to commit.

### Squash

To take multiple commits and "squash" them into a single commit

### Work Tree

The git repo, there are some ways to track multiple branches (trees) of a repo by cloning a bare repo

### Remote

A copy of the repo on another machine that you can pull and push to.

### Keys

Keys are in the format: `<section>.<key>`
`--global` is for all future uses of git and repos
`user.name` and `user.email` are keys used to create commit tied to author

### Tree

A directory that contains a set of files or directories

### Blob

A file

### Parent

The commit or commits that bore the current commit

### HEAD

Just a referrence to a commit for the head of the currently checked out branch.

See `.git/HEAD` holds a path to a `ref` that points to a commit

### Reflog

The log of where your HEAD has moved. Logs are stored in `.git/logs/HEAD`

### Cherry Pick

From `man git-cherry-pick`

Given one or more existing commits, apply the change each one introduces, recording a new commit for each. This requires your working tree to be clean (no modifications from the HEAD commit).

### Fetch

Get latest changes and update remote branches

### Pull

Get latest changes, update remote branch in local repo, and merge those changes with current (or selected) branch if tracked

### Push

Put latest changes from local branch to tracked remote branch and merge them.

### Stash

Stack of temporary changes, used mostly to keep working tree clean as needed.


## Tidbits

### See all files created in `.git` from `git init`

```bash
find .git
```

## Notes

Git stores the entire state of the repo in the repository not just the difference between states.
It is an asyclic graph that happens to be linear.

Git allows you to store multiples of the same key with different values. This allows for global and local configs without conflict. Resolution depends on first occurance in a file at specific levels (local > global)

When you create a new branch you are creating it at the moment of your current branch. AKA it is part of the current commit, you can see this in `.git/refs/<branch-name>` as the contents of the file of your branch name are the commit from which the branch was created.
