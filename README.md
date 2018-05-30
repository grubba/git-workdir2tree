# git-workdir2tree
Convert git workdirs created with `git new-workdir` to
git 2.5-style worktrees.

## How to use

* Make a backup.

* `cd` to a directory which has previously been created with `git new-workdir`.

```

$ cd src/Roxen/devel
$ ls -l .git
total 1564
-rw-r--r-- 1 grubba grubba      23 May 29 12:32 COMMIT_EDITMSG
-rw-r--r-- 1 grubba grubba    1255 Apr  5 11:30 COMMIT_EDITMSG~
-rw-r--r-- 1 grubba grubba   10239 May 29 12:31 FETCH_HEAD
-rw-r--r-- 1 grubba grubba      22 Apr  5 11:34 HEAD
-rw-r--r-- 1 grubba grubba     419 Apr 17 15:12 MERGE_MSG~
-rw-r--r-- 1 grubba grubba       0 May 29 12:32 MERGE_RR
-rw-r--r-- 1 grubba grubba      41 May 29 12:31 ORIG_HEAD
-rw-r--r-- 1 grubba grubba     911 Mar 20 16:27 addp-hunk-edit.diff~
lrwxrwxrwx 1 grubba grubba      38 Jan 22  2013 config -> /home/grubba/src/Roxen/5.2/.git/config
-rw-r--r-- 1 grubba grubba 1433326 May 14 11:27 gitk.cache
lrwxrwxrwx 1 grubba grubba      37 Jan 22  2013 hooks -> /home/grubba/src/Roxen/5.2/.git/hooks/
-rw-r--r-- 1 grubba grubba  124999 May 29 12:32 index
lrwxrwxrwx 1 grubba grubba      36 Jan 22  2013 info -> /home/grubba/src/Roxen/5.2/.git/info/
drwxr-xr-x 2 grubba grubba    4096 Mar 16 13:47 logs/
lrwxrwxrwx 1 grubba grubba      39 Jan 22  2013 objects -> /home/grubba/src/Roxen/5.2/.git/objects/
lrwxrwxrwx 1 grubba grubba      43 Jan 22  2013 packed-refs -> /home/grubba/src/Roxen/5.2/.git/packed-refs
lrwxrwxrwx 1 grubba grubba      36 Jan 22  2013 refs -> /home/grubba/src/Roxen/5.2/.git/refs/
lrwxrwxrwx 1 grubba grubba      39 Jan 22  2013 remotes -> /home/grubba/src/Roxen/5.2/.git/remotes
lrwxrwxrwx 1 grubba grubba      40 Jan 22  2013 rr-cache -> /home/grubba/src/Roxen/5.2/.git/rr-cache/

```

* Execute the script `git-workdir2tree`.

```

$ git-workdir2tree
Initializing the worktree git dir...
Moving COMMIT_EDITMSG...
Moving COMMIT_EDITMSG~...
Moving FETCH_HEAD...
Moving HEAD...
Moving MERGE_MSG~...
Moving MERGE_RR...
Moving ORIG_HEAD...
Moving addp-hunk-edit.diff~...
Moving gitk.cache...
Moving index...
Moving logs...

```

* The directory should now be worktree-compatible.

```

$ ls -l .git
-rw-r--r-- 1 grubba grubba 56 May 30 11:04 .git
$ cat .git
gitdir: /home/grubba/src/Roxen/5.2/.git/worktrees/devel
$ git worktree list
/home/grubba/src/Roxen/5.2    546e31fe3 [5.2]
/home/grubba/src/Roxen/devel  50696ddd8 [devel]

```

* The remaining symlinks from the old `.git`-directory are in the
  directory `.git.workdir`. This directory can be deleted after
  verifying that everything seems to work.

```

$ ls -lR .git.workdir
.git.workdir:
total 0
lrwxrwxrwx 1 grubba grubba 38 Jan 22  2013 config -> /home/grubba/src/Roxen/5.2/.git/config
lrwxrwxrwx 1 grubba grubba 37 Jan 22  2013 hooks -> /home/grubba/src/Roxen/5.2/.git/hooks/
lrwxrwxrwx 1 grubba grubba 36 Jan 22  2013 info -> /home/grubba/src/Roxen/5.2/.git/info/
lrwxrwxrwx 1 grubba grubba 39 Jan 22  2013 objects -> /home/grubba/src/Roxen/5.2/.git/objects/
lrwxrwxrwx 1 grubba grubba 43 Jan 22  2013 packed-refs -> /home/grubba/src/Roxen/5.2/.git/packed-refs
lrwxrwxrwx 1 grubba grubba 36 Jan 22  2013 refs -> /home/grubba/src/Roxen/5.2/.git/refs/
lrwxrwxrwx 1 grubba grubba 39 Jan 22  2013 remotes -> /home/grubba/src/Roxen/5.2/.git/remotes
lrwxrwxrwx 1 grubba grubba 40 Jan 22  2013 rr-cache -> /home/grubba/src/Roxen/5.2/.git/rr-cache/

```

## In case of failure

The script has quite of bit of sanity checks, and should be reasonably
safe to use. The script attempts to abort without modifying anything
in the common failure cases (missing binaries, not a workdir, permission
issues, etc). However it may leave a partially initialized worktree
configuration in the master `.git`-directory under some circumstances.

### Required command _xxx_ is missing from PATH.

Install the required command, and make sure that it is in one
of the directories listed in `$PATH`.

### A worktree named _xxx_ already exists.

Either rename the workdir `_xxx_` to something new, or check if the
directory `.git/worktrees/_xxx_` in the master repository is empty, or
only contains the files `commondir` and/or `gitdir`, in which case
that directory should be safe to delete.

### Failed to create worktrees directory.
### Failed to create worktree git dir.
### Failed to initialize worktree git dir.
### Failed to rename old git directory .../.git.
### Failed to create new .git file.

Check your filesystem permissions.

## How to reverse

* If `.git` is a directory you're done.

* Move or copy the contents of the directory named as _gitdir_
  in the `.git` file to `.git.workdir/`.

* Remove the files `.git.workdir/commondir` and/or `.git.workdir/gitdir`
  if they exist.

* Remove the `.git` file.

* Rename `.git.workdir` to `.git` .

* Test that the repository works.

* Delete the worktree _gitdir_ directory from above.

## If all else fails

Restore the backup you took when you started.
