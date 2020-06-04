VSCode:

1.  Supports `Git` ootb
2.  The GitLens: [https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
3.  An SSH key issue: [https://github.com/microsoft/vscode/issues/13680](https://github.com/microsoft/vscode/issues/13680)
4.  The Windows version of Git: [https://gitforwindows.org/](https://gitforwindows.org/)

VStudio:

1.  `Git Extensions` [program](http://gitextensions.github.io/) (portable/installed) + its [plugin](https://marketplace.visualstudio.com/items?itemName=HenkWesthuis.GitExtensions)

* * *

Before attempting to create a remote repository consider asking which protocol will you use for it.  
For example, a user `git` which would be avilable through `SSH` and only in `localhost`.  
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

To create a new empty remote repository:

1.  `mkdir RemoteRepo`
2.  `cd RemoteRepo`
3.  `git init --bare`
    - The `--bare` argument indicates this repository is bare, thus, does has `checked out` files outside of it. A `checked out` files are files with which you current work in current branch. If you'd try to push into `bare` repository as to remote server you might get and error like `remote: error: refusing to update checked out branch: refs/heads/master` meaning `Hey, this repository over there, it has a branch checked out, and you're trying to push to it. I'm not gonna let you do that`.

To create a new empty or not local repository:

1.  `cd LocalRep` or `mkdir LocalRep`
2.  `git init`
3.  `git add -A`
    - The `git add .` only adds new files that are on the current path. I.e. if you have a new directory `../foo`, `git add -A` will stage it, `git add .` will not.
    - Also, the `git add -u` adds only files which were `deleted/modified` to the index and not those which created.
4.  `git commit -m "Message of commit"`

To link local repository to remote

1.  `cd LocalRep`
2.  `git add remote <remotename> <url>`
    - The `remotename` is some name you might like
    - The `<url>` is a path to remote server. I.e. `ssh://user@localhost:port/absolutepathtoRemoteRepo`
    - Git support different protocols: `Git(9418 port), SSH, HTTP(S), HTTP, Local` etc.
3.  Ensure that everything is commited
4.  `git push -u <remotename> <branch>`
    - The `-u` flag establishes a tracking connection between that newly created branch on the remote and our local "contact-form" branch.
    - Where `<branch>` might be `master` or any other, i.e. a currently active one.

* * *

Some definitions:

1.  `Pull` = `LocalRepo <- RemoteRepo`
2.  `Push` = `LocalRepo -> RemoteRepo`
3.  `bare repository` \- A bare repository is normally an appropriately named `<directory>` with a `.git` suffix that does not have a locally checked-out copy of any of the files under revision control. That is, all of the Git administrative and control files that would normally be present in the hidden `.git sub-directory` are directly present in the repository.git directory instead, and no other files are present and checked out. Usually publishers of public repositories make bare repositories available.
4.  `<directory>` \- The name of a new directory to clone into. The "humanish" part of the source repository is used if no directory is explicitly given (`repo` for `/path/to/repo.git` and `foo` for `host.xz:foo/.git`). Cloning into an existing directory is only allowed if the directory is empty.
5.  `working tree` \- The tree of actual checked out files. The working tree normally contains the contents of the `HEAD` commit's tree, plus any local changes that you have made but not yet committed.
6.  `checkout` \- The action of updating all or part of the working tree with a tree object or blob from the object database, and updating the index and `HEAD` if the whole working tree has been pointed at a new branch.
7.  `HEAD` \- The current branch. In more detail: Your working tree is normally derived from the state of the tree referred to by `HEAD`. `HEAD` is a reference to one of the heads in your repository, except when using a detached `HEAD`, in which case it directly references an arbitrary commit.

* * *

Some links:

1.  [https://gitirc.eu/gitglossary.html](https://gitirc.eu/gitglossary.html)
2.  [https://gitirc.eu/git-clone.html](https://gitirc.eu/git-clone.html)
3.  [https://proglib.io/p/git-for-half-an-hour](https://proglib.io/p/git-for-half-an-hour)
4.  [https://git-scm.com/book](https://git-scm.com/book)
5.  [https://git-scm.com/book/en/v2/Git-Internals-Git-Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)
