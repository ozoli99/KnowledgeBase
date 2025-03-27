## Interactive Rebase

Today, let's explore **interactive rebasing**, a powerful tool for rewriting Git history in a structured way.

### What is Interactive Rebase?

`git rebase -i` (interactive rebase) allows you to modify a series of commits before merging them. You can use it to:
- Squash multiple commits into one
- Edit commit messages
- Reorder commits
- Remove unwanted commits

### How to Use Interactive Rebase?

`git rebase -i HEAD~N`

Replace `N` with the number of previous commits you want to modify (e.g., `git rebase -i HEAD~5` to edit the last 5 commits).

A text editor (like Vim) will open, showing a list of commits like:

    pick abc123 First commit<br>
    pick def456 Second commit<br>
    pick ghi789 Third commit

Modify the commands at the beginning of each line:
- `pick` → Keep the commit as is
- `reword` → Edit the commit message
- `squash (s)` → Combine the commit with the one above
- `edit` → Make changes to the commit
- `drop` → Delete the commit

Save and close the editor. If needed, Git will prompt you to edit commit messages or make changes.

### Example: Squashing Commits

Suppose you have:

    pick abc123 Added login page
    pick def456 Fixed login button color

Change it to:

    pick abc123 Added login page
    squash def456 Fixed login button color

This combines both commits into one, keeping the history clean.

### Why Use Interactive Rebase?

- Keeps Git history clean and meaningful
- Helps organize commits before merging to `main`
- Avoids unnecessary "fix" commits cluttering history

**Warning:** Avoid rebasing commits that are already pushed to a shared repository unless you know how to handle force pushes (`git push --force`).

## Understanding Git Cherry-Pick (`git cherry-pick`)

Today, let's explore `git cherry-pick`, a useful command that allows you to apply a specific commit from one branch to another without merging the entire branch.

### What is `git cherry-pick`?

Cherry-picking lets you **selectively copy a commit** from another branch and apply it to your current branch. This is useful when you need to bring in a bug fix or feature without merging unrelated changes.

### How to Use `git cherry-pick`?

#### 1. Find the commit hash

First, identify the commit you want to apply:

`git log --oneline`

Example output:

    a1b2c3d Fix login button bug
    e4f5g6h Add new settings panel

#### 2. Apply the commit to the current branch

`git cherry-pick a1b2c3d`

This applies the `Fix login button bug` commit to your current branch.

#### 3. Handling conflicts (if any)

If there are conflicts, Git will pause cherry-picking and let you resolve them.
- Resolve the conflicts manually.
- Stage the resolved files (`git add <file>`).
- Continue the cherry-pick (`git cherry-pick --continue`).

#### 4. Abort the cherry-pick if needed

If you want to cancel the process (`git cherry-pick --abort`).

### When to Use `git cherry-pick`?

- When a **hotfix** from `main` needs to be applied to a feature branch.
- When a specific commit from an abandoned branch needs to be preserved.
- When you want to **move a change without merging** unrelated work.

### Caution!

Overusing cherry-picking can lead to **duplicate commits** and a messy history. Use it **strategically** and consider whether merging or rebasing might be better alternatives.

## Git Stash (`git stash`)

Today, let's explore `git stash`. a powerful command that allows you to save your uncommited changes temporarily without committing them.

### What is `git stash`?

When working on a feature or bug fix, sometimes you need to switch branches without committing incomplete work. `git stash` helps by saving your local changes and allowing you to retrieve them later.

### Basic Usage of `git stash`

#### 1. Stash current changes:

`git stash`

This saves your uncommited changes and reverts your working directory to the last committed state.

#### 2. List stashed changes:

`git stash list`

Example output:

    stash@{0}: WIP on feature-branch: 123abc4 Add new login method
    stash@{1}: WIP on main: 789xyz2 Fix navbar issue

#### 3. Apply the latest stash:

`git stash apply`

This restores your stashed changes but keeps them in the stash list.

#### 4. Apply a specific stash:

`git stash apply stash@{1}`

This restores a specific stash.

#### 5. Remove a stash after applying it:

`git stash pop`

This restores the latest stash and removes it from the stash list.

#### 6. Drop a specific stash:

`git stash drop stash@{0}`

This deletes a specific stash entry.

#### 7. Clear all stashes:

`git stash clear`

This removes all stashed changes.

### When to Use `git stash`?

- When you need to **switch branches** but don't want to commit unfinished work.
- When you're experimenting with changes but want to revert quickly.
- When you want to temporarily save work before pulling updates from remote.

### Caution!

- Stashed changes are **not committed**, so they can be lost if not reapplied.
- Always check `git stash list` before running `git stash clear` to avoid losing important changes.

## Git Reflog (`git reflog`)

Today, let's explore `git reflog`, a powerful command that helps you recover lost commits and track your Git history beyond `git log`.

### What is `git reflog`?

Git maintains a history of all HEAD movements, including branch switches, commits, rebases, resets, and cherry-picks. `git reflog` allows you to see this history, even for commits that are no longer referenced in `git log`.

### How to Use `git reflog`?

#### 1. View the reflog history:

`git reflog`

Example output:

    7a3f6b6 HEAD@{0}: commit: Fix login bug
    b2d1e4c HEAD@{1}: rebase (finish): updating HEAD
    5e8f2a3 HEAD@{2}: checkout: moving from feature-branch to main

Each entry shows the commit hash, timestamp, and action.

#### 2. Restore a lost commit:

If you accidentally deleted a commit, find its hash in `git reflog` and reset or checkout to it:

`git checkout 7a3f6b6` or `git reset --hard 7a3f6b6`

#### 3. Undo a recent action:

Suppose you did a mistaken `git reset --hard` and lost changes. Use `git reflog` to find the previous state and restore it.

#### 4. Clean up the reflog (optional):

If you want to remove old reflog entries:

`git reflog expire --expire=90.days.ago --all`
`git gc --prune=90.days.ago`

This deletes logs older than 90 days and prunes unreachable commits.

### When to Use `git reflog`?

- Recovering lost commits after an accidental reset or rebase.
- Tracking movements of HEAD and undoing recent mistakes.
- Debugging branch history to understand changes over time.

**Note:** Reflog data is local and not shared with remote repositories.

## Git Bisect (`git bisect`)

Today, let's explore `git bisect`, a powerful command that helps you find the commit that introduced a bug by performing a **binary search** through your commit history.

### What is `git bisect`?

`git bisect` automates the process of finding a commit where a bug was introduced. Instead of manually checking each commit, Git helps you efficiently **narrow down the problematic commit** in a logarithmic number of steps.

### How to Use `git bisect`?

#### 1. Start bisecting

`git bisect start`

#### 2. Mark a known bad commit (where the bug exists)

`git bisect bad`

#### 3. Mark a known good commit (where the bug didn't exist)

`git bisect good <commit-hash>`

Git will now **checkout an intermediate commit** between the good and bad commits.

#### 4. Test the commit and mark it accordingly

- If the bug is present: `git bisect bad`
- If the bug is not present: `git bisect good`

Git will keep selecting new commits to test **until it finds the first bad commit**.

#### 5. Once Git finds the culprit commit, stop bisecting

`git bisect reset`

This returns you to your original branch.

### Example Scenario

Imagine your application was working fine at commit A but is broken at commit Z. Instead of checking each commit manually, `git bisect` will check the middle commit first, then continue **halving the search space** until it finds the exact commit that caused the issue.

### Why Use `git bisect`?

- **Saves time** by quickly locating a problematic commit.
- **Works well in large projects** where manually checking every commit is impractical.
- **Can be automated** using `git bisect run <script>` to test commits automatically.

## Git Blame (`git blame`)

Today, let's explore `git blame`, a useful command for tracking down who last modified a specific line of code and when.

### What is `git blame`?

`git blame` shows line-by-line annotations of a file, including:
- The commit hash that last modified each line.
- The author who made the change.
- The timestamp of the change.
This is useful for debugging, code reviews, and understanding the history of changes in a file.

### How to Use `git blame`?

#### 1. Basic Usage

`git blame <file>`

Example output for `main.cpp`:

    a1b2c3d (Alice 2025-03-10 10:15:20) int main() {
    d4e5f6g (Bob   2025-03-11 12:30:45)     return 0;
    a1b2c3d (Alice 2025-03-10 10:15:20) }

This shows that Alice wrote the `main()` function and Bob modified the `return 0;` line later.

#### 2. Show annotations for a specific commit range

`git blame <file> <commit-hash>`

This helps inspect changes before a certain commit

#### 3. Ignore whitespace changes

`git blame -w <file>`

This is useful when reformatting changes shouldn't be considered as actual modifications.

#### 4. Limit blame to a specific line range

`git blame -L 10,20 <file>`

This shows blame information only for lines 10 to 20 of the file.

### When to Use `git blame`?

- To identify who introduced a bug and understand why a change was made.
- To get historical context before modifying a file.
- To check when and why a line of code was last changed.

## Git Hooks (`git hooks`)

Today, let's explore Git hooks, a powerful feature that allows you to automate tasks before or after certain Git events.

### What Are Git Hooks?

Git hooks are custom scripts that run automatically in response to Git actions like committing, pushing, or merging. They help enforce best practices, automate workflows, and improve code quality.

### Types of Git Hooks

Git provides two categories of hooks:
- Client-side hooks (run on a developer's machine)
    - `pre-commit`: Runs before a commit is created (e.g., checking code style)
    - `commit-msg`: Runs after writing a commit message (e.g., enforcing a commit message format)
    - `pre-push`: Runs before pushing changes (e.g., running tests before allowing a push)
- Server-side hooks (run on a Git server)
    - `pre-receive`: Runs before accepting pushed changes (e.g., rejecting pushes that don't meet requirements)
    - `post-receive`: Runs after accepting changes (e.g., triggering deployment)

### How to Set Up a Git Hook

#### 1. Navigate to the hooks directory

`cd .git/hooks`

#### 2. Create or modify a hook script

Hooks are stored as shell script. For example, to enforce commit message formatting:

`nano .git/hooks/commit-msg`

Add the following script to ensure commit messages follow the Conventional Commits format:

    #!/bin/sh
    if ! grep -qE "^(feat|fix|chore|docs|refactor|test): .+" "$1"; then
        echo "Commit message must follow Conventional Commits format."
        exit 1
    fi

#### 3. Make the script executable

`chmod +x .git/hooks/commit-msg`

#### 4. Test it by committing

`git commit -m "invalid message"`

This will be rejected unless the message follows the format (`feat: add login button`).

### Why Use Git Hooks?

- Automate code quality checks (e.g., linting, tests)
- Enforce commit message standards
- Improve CI/CD by automating deployment triggers
- Prevent accidental pushes of sensitive files

## Git Submodules (`git submodule`)

Today, let's explore Git submodules, a feature that allows you to include external repositories within your main repository while keeping them separate.

### What Are Git Submodules?

A Git submodule is a pointer to a specific commit in another repository. This is useful when your project depends on an external repository but you don't want to merge its history into your main project.

### How to Use Git Submodules

#### 1. Adding a Submodule

To add a repository as a submodule inside your project

`git submodule add <repository-url> <path>`

Example

`git submodule add https://github.com/example/library.git libs/library`

This clones `library.git` into `libs/library` and tracks it as a submodule.

#### 2. Cloning a Repository with Submodules

If you clone a repository that contains submodules, run

`git clone --recurse-submodules <repository-url>`

If you already cloned it but didn't initialize submodules

`git submodule update --init --recursive`

#### 3. Updating Submodules

To update all submodules to the latest commit in their respective repositories

`git submodule update --remote --merge`

#### 4. Removing a Submodule

To remove a submodule

    git submodule deinit -f <path>
    rm -rf .git/modules/<path>
    git rm -f <path>

### When to Use Git Submodules?

- When using an external library that shouldn't be merged into your main repo's history
- When working with separate projects that need to stay independent but be referenced within a larger repository
- When including third-party dependencies that aren't available through package managers

## Git Worktrees (`git worktree`)

Today, let's explore Git worktrees, a lesser-known but extremely useful feature that allows you to have multiple working directories from a single Git repository.

### What Are Git Worktrees?

A worktree is an additional working directory associated with a Git repository. This allows you to check out different branches simultaneously in separate directories, which is ideal for testing, fixing bugs, or working on multiple features without creating new clones.

### Why Use Worktrees?

- No need to clone the repo multiple times
- Work on multiple branches in parallel
- Ideal for testing, cherry-picking, or working on quick fixes

### How to Use Git Worktrees?

#### 1. Add a New Worktree

`git worktree add <path> <branch>`

Example

`git worktree add ../feature-test feature-branch`

This checks out `feature-branch` into `../feature-test` directory.

If the branch doesn't exist yet, Git will create it:

`git worktree add -b new-feature ../new-feature`

#### 2. List Existing Worktrees

`git worktree list`

This shows the paths and branches associated with each worktree.

#### 3. Remove a Worktree

`git worktree remove <path>`

This deletes the working directory but doesn't delete the branch.

### Use Cases for Worktrees

- Working on hotfixes while keeping your main branch intact
- Running tests or builds in parallel for different branches
- Keeping long-lived feature branches in separate folders