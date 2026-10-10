

# Git Inspector
- a Python command-line tool that reads information from a Git and shows summaries of its commit history, branches, working-tree status, contributors, tags, remotes, file types, diff statistics, and commit-message searches.

## Requirements
- Python 3.13.x
- Git
- pip
- Docker 

---
## Local Setup

## Clone the repository
git clone https://github.com/Burcu-weining/Git-Inspector-Python-Docker.git
cd Git-Inspector-Python-Docker

### Create a virtual environment
(macOS)
python3.13 -m venv .venv
source .venv/bin/activate
python --version

Confirm that Python 3.13.x is being used:
python --version

### Install runtime dependencies
pip install -r requirements.txt
The required dependencies are in `requirements.txt` so that a people can see what exact package versions are needed. 
By doing this, no extra packages will accidentally be installed.

----------------------------
## Git Inspector
1. Ran th full pytest to see the failures/passes and it returned 15 failures and 8 passes - `pip install pytest` then `pytest`
2. Looking at each bug and debugging them one by one (the detail of each bug can be found in the report)
3. ran each test again to see if the test passed
4. Reran everything using pytest after fixing every bug to make sure no other bugs were accidentally created. 

## Create the demo Repository
`python scripts/make_demo_repo.py`
--------------------------
### Commit Log
Create the demo repository
Display all recent commits from the demo repository:

```bash
python -m git_inspector.cli log --path demo-repo
```
## What it does:
Reads the git commit history from demo-repo and displays each commit's hash, author, date, and message in newest-first order
## Expected result:
The demo repository contains 7 commits, so all 7 commits should be displayed

-------
Limit the number of displayed commits:

```bash
python -m git_inspector.cli log --limit 3 --path demo-repo
```
## What it does:  
Runs the commit log command but limits the output to the three most recent commit
## Expected result: 
Exactly 3 commits should be displayed:
1. Remove obsolete notes
2. Update README with usage
3. Add archive and tests

---------
Filter commits by author:

```bash
python -m git_inspector.cli log --author Ada --path demo-repo
```

## What it does:
Displays only commits where the author's name is "Ada"
## Expected result: 
The output should contain the 4 commits made by Ada Lovelace and should not include commits by Grace Hopper or Alan Turing

-------
Filter commits by date:

```bash
python -m git_inspector.cli log --since 2024-02-01 --path demo-repo
```
## What it does: 
Displays only commits made on or after 1 February 2024
## Expected result:
Exactly 4 February commits should be shown

--------
### Branches

```bash
python -m git_inspector.cli branches --path demo-repo
```

## What it does:
Reads the local branches in the repo and identifies which branch is currently checked out
## Expected result: 
Two branches should be displayed, feature-x and main, and main should be the one we are currently in

------------------
### Working-Tree Status

```bash
python -m git_inspector.cli status --path demo-repo
```

## What it does: 
Displays the current working-tree state and indicates whether each changed file is staged, unstaged, or untracked
## Expected result: 
onl readme should be marked unstaged, staged_new should be marked staged, and scratch should be untracked

-------------
### Contributors

```bash
python -m git_inspector.cli contributors --path demo-repo
```

## What it does:
Counts commits made by each person and ranks them from most active to least active
## Expected result: 
- Ada Lovelace — 4 commits
- Grace Hopper — 2 commits
- Alan Turing — 1 commit

Limit the contributor output:

```bash
python -m git_inspector.cli contributors --top 2 --path demo-repo
```
## What it does:
Displays only the two contributors with the highest commit counts
## Expected result:  
Only Ada Lovelace and Grace Hopper should be printed

---
### Tags

```bash
python -m git_inspector.cli tags --path demo-repo
```

## What it does: 
Displays all Git tags together with their associated messages
## Expected result:
v0.9-beta — Add notes and data
v1.0 — First stable release

-------------
### Remotes

```bash
python -m git_inspector.cli remotes --path demo-repo
```

## What it does: 
Displays each configured git remote, its URL, and whether it is used for fetching or pushing
## Expected result: 
Two rows should be displayed for origin --> push / fetch

---------------
## File Types

```bash
python -m git_inspector.cli filetypes --path demo-repo
```
## What it does:  
Counts tracked files according to their file extension

## Expected result: 
- `py — 2
- none — 1
- md — 1
- gz — 1
- json — 1
- txt — 1

----------
## Diff Statistics

```bash
python -m git_inspector.cli diffstat --path demo-repo
```

## What it does:
Calculates the total number of files changed, lines inserted, and lines deleted at the recent commit history
**Expected result:**
10 files changed, +27 insertions, -8 deletions

--------------
## Search Commit Messages

```bash
python -m git_inspector.cli search --query bug --path demo-repo
```

## What it does: 
Searches commit messages for the word bug. The search is not case sensitive.
## Expected result:
The lowercase bug should match the commit, "Fix Bug in parser"

-------------
Search for a term that does not exist:

```bash
python -m git_inspector.cli search --query nonexistent --path demo-repo
```

## What it does:
Searches the commit history for a message containing the word nonexistent
## Expected result:
No matching commits

-------------------
## Running the Test 
Install pytest:

```bash
pip install pytest
pytest
```

## What it does:
Installs the pytest testing in the terminal, and runs all the tests
## Expected result:  
It shows the number of pass and fail tests. 
23 passed  --> expected

--------------
## Docker

The project can also run inside a Docker container.

The Docker image:
- uses Python 3.13.x
- installs Python runtime dependencies from `requirements.txt`
- installs the git command-line required by `git-inspector`
- uses `.dockerignore` to exclude unnecessary local development files from the Docker build context

---
### Build the Docker Image

From the `A2` directory:

```bash
docker build -t git-inspector-a2 .
```

## What it does:  
Builds a Docker image using the project's dockerfile and gives the resulting image the name `git-inspector-a2`.
## Expected result: 
The image should build successfully. The corrected Dockerfile should install the packages in requirements.txt and install git.

---------
### Verify Git is Inside the Container

```bash
docker run --rm --entrypoint git git-inspector-a2 --version
```

## What it does:  
directly runs `git --version` inside the container
## Expected result:  
A Git version should be displayed:(because it si installed)
git version 2.47.3

----------------
### Run Git Inspector Against a Repository

```bash
docker run --rm \
  -v "$PWD/demo-repo:/repo" \
  git-inspector-a2 log --path /repo
```

## What it does:
Mounts the local demo-repo directory into the container at the repo and runs the git inspector log command
## Expected result 
All 7 demo-repository commits should be displayed in newest order first, matching the local log command

---------------------
### Branches

```bash
docker run --rm \
  -v "$PWD/demo-repo:/repo" \
  git-inspector-a2 branches --path /repo
```

## What it does: 
Mounts the demo repo into the container and displays local branches
## Expected result:  
- feature-x
- main --> current branch

---------
## Status

```bash
docker run --rm \
  -v "$PWD/demo-repo:/repo" \
  git-inspector-a2 status --path /repo
```

## What it does: 
checking if the files are staged, unstaged, untracked
## Expected result:
- README.md — unstaged
- staged_new.txt — staged
- scratch.tmp — untracked

---
### Contributors

```bash
docker run --rm \
  -v "$PWD/demo-repo:/repo" \
  git-inspector-a2 contributors --path /repo
```

## What it does  
Counts commits by author inside the repository and ranks the contributors the number of commits
## Expected result:
- Ada Lovelace — 4 commits
- Grace Hopper — 2 commits
- Alan Turing — 1 commit

---
### Tags

```bash
docker run --rm \
  -v "$PWD/demo-repo:/repo" \
  git-inspector-a2 tags --path /repo
```

## What it does: 
Displays the git tags and their messages from the mounted demo repo
## Expected result:
v0.9-beta — Add notes and data
v1.0 — First stable release`

---
### Remotes

```bash
docker run --rm \
  -v "$PWD/demo-repo:/repo" \
  git-inspector-a2 remotes --path /repo
```

## What it does
Displays the git remote configuration of the mounted repository
## Expected result: 
Two origin rows should appear:
-fetch
-push

---
### File Types

```bash
docker run --rm \
  -v "$PWD/demo-repo:/repo" \
  git-inspector-a2 filetypes --path /repo
```

## What it does: 
Counts the tracked files in the mounted repo
## Expected result:  
- py — 2
- (none) — 1
- md — 1
- gz — 1
- json — 1
- txt — 1

---
## Diff Statistics

```bash
docker run --rm \
  -v "$PWD/demo-repo:/repo" \
  git-inspector-a2 diffstat --path /repo
```

## What it does:  
Calculates the files changed, insertions, and deletions from the mounted repo.
## Expected result:
10 files changed, +27 insertions, -8 deletions

---
### Search Commit Messages

```bash
docker run --rm \
  -v "$PWD/demo-repo:/repo" \
  git-inspector-a2 search --query bug --path /repo
```

## What it does: 
Searches commit messages in the mounted repository from inside the docker
## Expected result: 
The lowercase query bug should successfully find "Fix Bug in parser"

-----------
After running these commands, check the final Docker image size:
`docker images git-inspector-a2`
-------------
