# Version Control (Git)

Notes derived from [Missing Semester of CS # Version Control (Git)](https://missing.csail.mit.edu/2020/version-control/) \
A simple git workflow [tutorial](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)

## What is Version Control

Version control allows you to track changes to folder and its contents in a series of snapshots.

### A Snapshot

A snapshot encapsulates the entire state of files/folders within a top-level directory.

### Problems Solved with Version Control

- Allows you to see who made what changes to a specific piece of code.
- When was this particular file edited?
- Over the last 1000 revisions, when/why did a particular unit test stop working?

*XKCD Comic* [Link](https://imgs.xkcd.com/comics/git.png)

## Git's Data Model

### Snapshots

Git models the history of a collection of files and folders within a top-level directory as a series of snapshots.

- Git Terminology
  - A file in git is called a "blob"
  - A directory in git is called a "tree"

### What This Looks Like

```text
<root> (tree)
|
+- Python (tree)
|  |
|  + hello.py (blob, contents = `print('Hello, World!')`)
|
+- JavaScript (tree)
|  |
|  + hello.js (blob, contents = `console.log('Hello, World!')`)
|
+- README.md (blob, contents = "# Introduction to Git")
```

The top-level tree contains two three elements, a tree "Python" (contains a single blob, 'hello.py'), a tree "JavaScript" (contains a single blob, 'hello.js') and a blob 'README.txt'

### Modeling History: Relating Snapshots

In Git, a history is a directed acyclic graph (DAG) of snapshots, all that means is that each snapshot in Get refers to a set of "parents", the snapshots that preceded it. It's a set of parents rather than a single parent. This is because a snapshot might descend from multiple parents, int he case of merging two parallel branches of development.

Git calls these snapshots "commit"s. Commit history can be visualized as:

```text
o <-- o <-- o <-- o (feature-api)
            ^
             \
              --- o <-- o (bug-fix-client)
```

In the above ASCII art, the `o`s correspond to individual commits (snapshots). The arrows point to the parent of each commit ("comes before" relation).

After the third commit the history branches into two separate branches. The `feature-api` branch and the `bug-fix-client` branch. This allows for teams to work on new features and bug fixes. In parallel, independently from each other.

These branches are then able to be merged to create a new snapshot incorporating all of the new changes. Producing a new history that looks like this (merge commit = X):

```text
o <-- o <-- o <-- o <---- X (feature-api)
            ^            /
             \          v
              --- o <-- o (bug-fix-client)
```

Commits in Git are immutable. This doesn't mean mistakes can't be corrected, however; "edits" to the commit history are actually creating entirely new commits, and references are updated to point to the news ones.

## Git Data Model, as Pseudocode

```text
// a file is a bunch of bytes
type blob = array<byte>

// a directory contains named files and directories
type tree = map<string, tree | file>

// a commit has parents, metadata, and the top-level tree
type commit = struct {
    parent: array<commit>
    author: string
    message: string
    snapshot: tree
}
```

### Objects and Content-Addressing

An "object" is a blob, tree or commit:

```text
type object = blob | tree | commit
```

In the Git data store, all objects are content-addressed by their [SHA-1 hash](https://en.wikipedia.org/wiki/SHA-1).

```python
object = map<str, object>

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

Blobs, trees, and commits are all objects. When they reference other objects, they don't actually contain them in their on-disk representation, but have a reference to them by their hash.

### References

Now, all snapshots can be identified by their SHA-1 hash. That's inconvenient for humans.

Git's solution to this is human-readable names for SHA-1 hashes, called "references". References are pointers ot commits. References are mutable (they can be updated to point to a new commit). For example, the `master` reference usually points to the last commit in the main branch of development.

```python
references = map<str, str>

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```

With this, Git can use human-readable names like "master" to refer to a particular snapshot in the history.

To know "where we currently are" in the history, Git uses a special reference called "HEAD".

### Repositories

Finally, we can define what (roughly) is a a Git repository: it is the data `objects` and `references`.

On disk, all Git stores are objects and references: that's all there is to Git's data model. All `git` commands map to some manipulation of the commit graph bu adding objects and add/uupdating references.

Whenever you're typing in any command, think about what manipulation the command is making to the underlying graph data structure.

