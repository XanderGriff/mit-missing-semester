- #[[Missing Semester]] #Lecture
- **Link:** https://missing.csail.mit.edu/2020/version-control/
- **Notes:**
    - There are a number of version control systems out there, but Git has become the de facto tool
    - Goal of this lecture is not to have to do git hacks (ie. delete and reclone a repository)
    - Key to understanding the interface of Git is to understand the underlying data model
    - Git models the history of a collection of files and folders within some top-level directory as a series of snapshots.
    - Git refers to files as "blobs" and directories as "trees"
    - Git history is a Directed Acyclic Graph (DAG) of snapshots
        - This basically means that each snapshot in Git refers to a set of parents, which are the snapshots that preceded it.
        - Of note is that it is a __set of parents__ as opposed to one parent (which would be a linear history) because a snapshot might descend from multiple parents (eg. merging two parallel branches of development into one branch)
    - Commits also have metadata
        - author, message, etc
        - Commits in Git are immutable, meaning that edits to the commit history are actually creating entirely new commits and references are updated to point to the new ones
    - Data model pseudocode:
        - ```// a file is a bunch of bytes
type blob = array<byte>

// a directory contains named files and directories
type tree = map<string, tree | blob>

// a commit has parents, metadata, and the top-level tree
type commit = struct {
  parent: array<commit>
  author: string
  message: string
  snapshot: tree
}

type object = blob | tree | commit
objects = map<string, object>

def store(object):
	id = sha1(object)
	objects[id] = object

def load(id):
	return objects[id]```
        - Blobs, trees, and commits are unified in this way: **they are all objects**
        - Objects are identified by SHA-1 hashes, which are 40 character hex strings
    - While all snapshots can be identified by their SHA-1 hashes, it's inconvenient for humans who aren't naturally able to recall 40 hex characters. Instead Git also stores __references__
        - references are pointers to commits
        - references are mutable
        - eg. the `master` reference usually points to the latest commit in the main branch of development
        - "where you currently are" in Git is referred to by a special reference: `HEAD`
    - A Git repository is just a collection of `objects` and `references`
        - All Git commands map to some manipulation of the commit DAG by adding objects and adding/updating references
        - Whenever you type a command, consider how that command will manipulate the underlying graph structure
    - All of the Git files are stored in the top level Git directory in the `.git` folder
    - Git has a staging area, which allows you to tell Git which changes should be included in the next commit
        - [here](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html) is a great link on what it means to write really good commit messages.
            - tldr:
                - Write commits like an email. First line (<50chars) should be the subject and rest of the message below (<72chars, preceded by an empty line)
        - [here](https://vi.stackexchange.com/questions/15139/strange-highlighting-during-git-commit) is an interesting thread explaining why some weird syntax highlighting happens in vim when you're writing commit messages
    - 
