[Write yourself a Git!](https://wyag.thb.lt/)

A smol hands-on course to make a git-like VCS using python. Doesn't implement all the functionality of git but is enough to make you understand how git works.

Here's some notes:

Actual git is implemented in C.

Implemented commands such as `add`, `cat-file`, `check-ignore`, `checkout`, `commit`, `hash-object`, `init`, `log`, `ls-files`, `ls-tree`, `rev-parse`, `rm`, `show-ref`, `status` and `tag`.

Work tree -> the codebase.

Git files are stored in `.git/` directory inside the work tree.

Git has following files(minimum) inside `.git/`:

- `objects/`
- `refs/`
- `HEAD`
- `config`

The `objects/` directory holds the actual data(called objects). There are 4 types of objects in git: `blob`, `commit`, `tree` and `tag`. More on objects later.

`refs/` hold references, `HEAD` stores the ref to current branch and `config` contains some configuration about the repository(ex. `repositoryformatversion`).

On init,

- we create directories such as `objects/`, `refs`(`refs/tags` and `refs/heads`)
- we create files such as `HEAD`, `description`, `config`
- Git config is stored in `.git/config` file. Making sure `repositoryformatversion` is not 0.

Objects are the most fundamental way git stores data. Some info about them:

Object names:

During commit, git calculates SHA-1(hardened SHA-1) of the file(we'll see what's stored in the file later), and based on that sha, we store the object in the following manner:

First two values of sha becomes the directory: `.git/objects/<sha[0:2]>/<sha[2:]>`

Size:

Most of the objects are compressed using zlib and then stored.

About objects:

We have 4 types of objects, let's discuss them and their structure:

- `blob`

Blob objects just store the files in a blob

```
<object type == "tree"> <blob size>\x00<blob data>
```

> What we’ve just implemented is called “loose objects”. Git has a second object storage mechanism called packfiles. Packfiles are much more efficient, but also much more complex, than loose objects. Simply put, a packfile is a compilation of loose objects (like a tar) but some are stored as deltas (as a transformation of another object).
> The packfile is stored in `.git/objects/pack/`. It has a `.pack` extension, and is accompanied by an index file of the same name with the `.idx` extension.

- `tree`

Tree objects store the path(from worktree), it's SHA(which can infered as a blob path), and the file's mode

```
<object type == "tree"> <blob size>\x00
<mode> <path> 0x00 <sha1>
```

If the path is a file(which can be infered from the file mode), the sha-1 points to the blob object.

If the path is a directory, the sha-1 points to another tree object which holds data for that directory files.

- `commit`

The commit object holds the commit data related to a particular commit.

```
<object type == "commit"> <blob size>\x00
tree <sha-1 to a tree object>
parent <sha-1 to parent commit object> (0 or more parents)
author <name> <email> ...timestamps
commiter <name> <email> ...timestamps
gpgsig ....
<commit message>
```

This commit object is then hashed and gets refered by branches to point to last commit.

- `tag`

Tag objects just point to certain blobs which we have tagged(using `git tag`)

We make sure the data is sorted because even if contents of the file are same, if their positions are changed, the SHA-1 of the file would be different.

To get logs, we actually go to a particular commit and traverse through their parent IDs until we find the start of commit.

refs:

Refs are references to sha-1 ids. Branch names are basically refs to their last commit. They are just text files.

Refs can either contain SHA-1 id or another reference(`ref: refs/remotes/origin/master`). The `HEAD` file actually contains reference to a branch. In case `HEAD` contains a SHA-1 id, it's in detached head state.

> The most simple use of refs is tags. A tag is just a user-defined name for an object, often a commit.

There's two type of tags: "lightweight" tags and tag objects.

> “Lightweight” tags are just regular refs to a commit, a tree or a blob.

> Tag objects are regular refs pointing to an object of type tag. Unlike lightweight tags, tag objects have an author, a date, an optional PGP signature and an optional annotation. Their format is the same as a commit object.

A branch is a reference to a commit.

Tags are refs that live in `.git/refs/tags`, branches are refs that live in `.git/refs/heads`.

Branch ref is updated after each commit.

Index and staging area:

There's a concept of index file. In a way, staging area are the same as index files.

Index files are basically a copy of HEAD data with some extra metadata such as file modification date, user id, group id, etc.

> When the repository is “clean”, the index file holds the exact same contents as the HEAD commit, plus metadata about the corresponding filesystem entries
> When you git add or git rm, the index file is modified accordingly.
> When you git commit those changes, a new tree is produced from the index file, a new commit object is generated with that tree, branches are updated and we’re done.

structure of index file:

- An header with the format version number and the number of entries the index holds;
- A series of entries, sorted, each representing a file; padded to multiple of 8 bytes.
- A series of optional extensions, which we’ll ignore.

it contains `ctime`, `mtime`, `dev`(device id), `ino`(file's inode number), `mode_type`(regular, symlink, etc), `mode_perms`, `uid`, `gid`, `fsize`(file size), `sha`(object's sha), `name`(name of the object, i.e., it's path).

it's a binary file.

> It begins with a header with the DIRC magic bytes, a version number and the total number of entries in that index file.

"the staging area is a copy, not a delta"

during `git status`, we compare

- the index against worktree -> "changes not staged for commit"
- the index against HEAD commit -> "changes to be committed"(HEAD is converted to index like structure and then compared against their ctime, mtime, sha, etc.)

`git rm` removes entry from index

`git add` removes existing entry from index(if present), hash the file into blob object, create entry in object, write index back.

`git commit` -> index -> tree object -> commit object

gitignore:

- lives in index
- outside of index(global)

matching against file path to the pattern in gitignore.

Done!
