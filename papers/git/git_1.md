## Code editor

These are tools which might help working with the `git` using a particular code editor.

[VSCode](https://code.visualstudio.com/download):

1.  Supports `Git` out-of-the-box.
2.  The GitLens: [https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens).
3.  An SSH key issue: [https://github.com/microsoft/vscode/issues/13680](https://github.com/microsoft/vscode/issues/13680).
4.  The Windows version of Git: [https://gitforwindows.org/](https://gitforwindows.org/).

[VStudio](https://visualstudio.microsoft.com/vs/community/):

1.  `Git Extensions` [program](http://gitextensions.github.io/) (portable/installed) + its [plugin](https://marketplace.visualstudio.com/items?itemName=HenkWesthuis.GitExtensions)
* * *

## Repository

Before attempting to create a remote repository consider asking which protocol will you use for it.  
For example(`linux`), a user `git` which would be avilable through `SSH` and only in `localhost`.  
The `AllowUsers` setting in `sshd_config` is disabled by default, hence, allows any user to connect to SSH server.

1.  `sudo nano /etc/ssh/sshd_config`
    1.  Add at the end of the file `AllowUsers <users with an access>` where a git user would be `git@::1`. Where `::1` means IPv6 loopback which exists when SSH tunneling. Thus, this will allow `git` user to authenticate only locally.
2.  Add a git user `adduser git`. It would be better injail this user.
3.  Generate a key pair for it: `sudo ssh-keygen -f /etc/ssh/<nameofthekey> -b 4096`.
4.  Get a private key to local machine
    - For unknown reason, Windows git shows warning if no public key exists, thus, you might need to get it to.
5.  Create a symlink to repositories' folder inside its home folder: `ln -s /path/to/repos Git`
6.  Add/append a public key to `authorized_keys` file
    - If password used for private key and VSCode used: check above link for `an SSH key issue`.
    - If Windows used:
        1.  Copy private(and public if GitforWindows used) key(s) to `%user%\.ssh`
        2.  Create a config file inside `%user%\.ssh` with a contents:```  
            Match User git  
            IdentityFile ~/.ssh/
7.  The remote URL would be `ssh://git@localhost:port/home/git/Git/RemoteRepo`

* * *

To create a new empty(or not) repository:

1.	`cd <repo>` or `mkdir <repo> && cd <repo>`, where the `<repo>` is a desired name of a repository.
	* Add a `.git` suffix to a directory name(i.e. `<repo>.git` directory) if this repository will be a `bare repository`(a centralized, shared or accessed remotely i.e. through `git server`). This suffix is not really necessary, but will indicate that this is a `bare` repo.
2.	`git init` or `git init --bare` if it is a bare repository.
    - If **no** `--bare` is argument specified then the `git` will create a hidden `.git` directory in repository's `worktree` which will contain all git control and administrative files of repository(i.e. history, branches etc.).
    	- A `worktree` is the files that the developer directly modifies to `commit` later.
    - If a `--bare` argument exists then this indicates this repository is bare, thus, it does **not** have `checked out` files outside of it. A `checked out` files are files with which the developer currently works with in a `worktree` of current branch. If you'd try to push into **non** `bare` repository you might get and error like `error: refusing to update checked out branch: refs/heads/master` meaning `Hey, this repository over there, it has a branch checked out, and you're trying to push to it. I'm not gonna let you do that`.  
    This option basically does not create a hidden `.git` directory in repository, but instead acts like it. All repository control and administrative files which would be put into a hidden `.git` directory are directly put into root of this repository. Thus, not `worktree` available.  
    It's worth to notice that a `bare` repository contains indicators in its `config` file which might prevent running some commits that require a worktree.
    - From `man git-init` (if `git init --bare` used):
    	> Create a bare repository. If the `$GIT_DIR` environment variable is not set, it is set to the current working directory.
    - From `man git-clone` (if `git clone --bare <url> <path>` used):
    	> Make a bare Git repository. That is, instead of creating `<directory>` and placing the administrative files in `<directory>/.git`, make the `<directory>` itself the `$GIT_DIR`. This obviously implies the `-n` or `--no-checkout` because there is nowhere to `check out` the working tree. Also the `branch heads at the remote` are copied directly to corresponding `local branch heads`, without mapping them to `refs/remotes/origin/`. When this option is used, neither remote-tracking branches nor the related configuration variables are created.

If a `<repo>` directory is not empty:

3.  `git add -A` to add all files into repository which exist in `<repo>` directory, `git add <filepath>` to add a specific one or `git add <filepath>/\*` to add everything from specific directory(i.e. `<filepath>/\*.cpp` would add all C++ source files) etc.
    - The `git add .` only adds new files that are on the current path. I.e. if you have a new directory `../foo`, `git add -A` will stage it, `git add .` will not.
    - The `git add -u <filepath>` adds only files which were `deleted/modified` to the index and not those which created.
    - The `git add <filepath> --ignore-removal` ignores file deletions
4.  `git commit -m "Message of commit"` to create a `commit` with changes.

* * *

== Remote

First of all, a remote repository is not necessary. You are able to work with the `git` absolutely locally, alone. Just initialize a `non bare`(more below) repository locally and work with it: `commit`, `check out`, create new `branches`, `merge` etc.  
The `git` does **not** need the Internet or any other server access if used only on local machine.  
A locally initialized repository has exactly the same functionality and features which we would have using a `git server` by default. `By default` means that a remote `git server` might have some additional security(i.e. an IP filter, bandwidth limits etc.).  
For example, we are able to create both `worktree` and `bare` repositories on local machine and work with them: `commit` to `worktree` repository and `push` into a `bare` one and it give it to a friend using a pen drive.  
Although, it might be simpler to share your `bare` repository using a `git server` of you own or any other like `GitHub`, `Bitbucket` etc. There are even such `git` `repository managers` like the `GitLab` which is possible to install on your own local or remote `web server` and it might help manage repositories easier. It even has an automatic build, check systems and such by default which might help automate the process.

To link a `local` repository with `remote`(bare) to have an ability to `push/fetch/merge` etc.

1.  `cd <repo>`
2.  `git remote add <remotename> <url>(.git)`
    - `remotename` is some desired name of a server you add. Usually, the default name of remote `git server` is `origin`.
    - `<url>` is a path to remote server. I.e. `ssh://user@localhost:port/absolute_path_to_repo.git` or `ssh://user@localhost:port/absolute_path_to_repo` if the directory of `bare` repository has no `.git` suffix(rare, but happens).
        - Some `git servers` ommit this suffix, so they don't care about it. All in all, better use a full name every time.
    - The `Git` supports different protocols: `git://` Git(9418 port), `ssh://` SSH, HTTP `http://`, HTTPS `https://`, Local `file://` etc.
3.  Ensure that everything is commited
4.  `git push -u <remotename> <branch>`
    - The `-u` flag establishes a tracking connection between that newly created branch on the remote and our local "contact-form" branch.
    - Where `<branch>` might be `master` or any other, i.e. a currently active one.

* * *

## Definitions

1.  `Pull` = `LocalRepo <- RemoteRepo` the same as `git fetch <remote> && git merge <remote>/<branch>`.
2.  `Push` = `LocalRepo -> RemoteRepo`.
3.  `bare repository` \- A bare repository is normally an appropriately named `<directory>` with a `.git` suffix that does not have a locally checked-out copy of any of the files under revision control. That is, all of the Git administrative and control files that would normally be present in the hidden `.git sub-directory` are directly present in the repository.git directory instead, and no other files are present and checked out. Usually publishers of public repositories make bare repositories available.
4.  `<directory>` \- The name of a new directory to clone into. The "humanish" part of the source repository is used if no directory is explicitly given (`repo` for `/path/to/repo.git` and `foo` for `host.xz:foo/.git`). Cloning into an existing directory is only allowed if the directory is empty.
5.  `working tree`/`worktree` \- The tree of actual checked out files. The working tree normally contains the contents of the `HEAD` commit's tree, plus any local changes that you have made but not yet committed.
6.  `check out` \- The action of updating all or part of the working tree with a tree object or blob from the object database, and updating the index and `HEAD` if the whole working tree has been pointed at a new branch. To completely set your `worktree` to a branch's contents.
7.  `HEAD` \- The current branch. In more detail: Your working tree is normally derived from the state of the tree referred to by `HEAD`. `HEAD` is a reference to one of the heads in your repository, except when using a detached `HEAD`, in which case it directly references an arbitrary commit.

* * *

## Links:

1.  [The entire Pro Git book](https://git-scm.com/book)
2.  [Reference](https://git-scm.com/docs)
3.  [gitglossary(7) Manual Page](https://gitirc.eu/gitglossary.html)
4.  [Git Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)
5.  [Half an hour Git: A Beginner's Guide](https://translate.google.com/translate?hl=&sl=ru&tl=en&u=https%3A%2F%2Fproglib.io%2Fp%2Fgit-for-half-an-hour)(Google translated)
6.  [git-clone(1) Manual Page](https://gitirc.eu/git-clone.html)
