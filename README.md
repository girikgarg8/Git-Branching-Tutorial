## Let's talk about Git stash ##

**Firstly, what is a stash in Git?**

Stash can be said to a private locker where the changes are stored temporarily, anything that is stashed is not a part of the commit.

Technically speaking, whenever we have some code changes that we don't want to be part of the next commit, we can add those changes to the staging area and use `git stash`. It's important to know that stash behaves like a stack data structure.

**What are the usecases of Git stash in real life projects?** 

1) Switching between branches on Git: Often, when you’ve been working on part of your project, things are in a messy state and you want to switch branches for a bit to work on something else. The problem is, you don’t want to do a commit of half-done work just so you can get back to this point later. The answer to this issue is the `git stash` command.

Stashing takes the dirty state of your working directory — that is, your modified tracked files and staged changes — and saves it on a stack of unfinished changes that you can reapply at any time (even on a different branch).

**Let's also clear one of the doubts: When does Git give a warning when switching branches**

Reference from this link: [Stackoverflow Link](https://stackoverflow.com/questions/38029499/git-does-not-warn-me-about-changes-when-switching-branches#:~:text=You%20get%20a%20message%20asking,to%20stash%20or%20commit%20changes) 

Git **doesn't always** give a warning if you have changes in the working directory/staging area and you try to switch to a different branch. The default behaviour is to copy everything from working directory/staging area to the branch that you are checking out to. **Git only gives a warning if there are going to be conflicts while switching the branches**. Let's see this in action:

No conflicts, hence no warning. Changes from previous branch copied to the latest branch:

![Git-no-warning-when-no-conflict-while-switching-branches](./Git-no-warning-when-no-conflict-while-switching-branches.png)

From the screenshot above, we can see that when we checkout to main branch from dev branch, as there are no conflicts in README.md, the changes from dev branch are copied to the main branch.

There are going to be conflicts, hence Git gives a warning while switching branches:

![Git-warning-when-switching-branches](./Git-warning-when-switching-branches.png)

From the screenshot above, we can see that Git asks us to commit or stash the changes before switching branches, when it sees that a conflict can occur due to switching of branches.

2) Using local overrides or configurations: At times, there are certain mock props, API keys or AWS credentials etc which we need to use in the local working directory, but we don't want to push them to the remote repository. In this case, we can stash the configurations etc before pushing to the remote repository. 

**Let's now try to use git stash**

For the demonstration purpose, let's create `correct.js` and `wrong.js` . `wrong.js` is a piece of dirty code that we don't want to be present in the next commit, hence we will use git stash.

Before git stash:

![Git-before-stash](./Git-before-stash.png)

After git stash:

![Git-after-stash](./Git-after-stash.png)

As we can see from the screenshots above, we committed `correct.js` and staged `wrong.js` for stashing. Before stashing, the working directory had both `correct.js` and `wrong.js` in it, but after using `git stash`, the staged files (wrong.js) were moved to the stash, and the working directory only had `correct.js`

**Let's also take a look at the message which we got while stashing the changes and try to understand:** 

`Saved working directory and index state WIP on main: 027e083 Initial commit` 

This message tells us that by using `git stash`, the working directory and the staging area work-in-progress from the branch main (which points to the commit 027e083 with the commit message Initial commit) has been saved into stash.

Another important note: By using the `--include-untracked` flag, we can stash the changes both from working area as well as the staging area. Without this flag, git only stashes the changes from staging area.

`git stash list` lists the current stash entries. Each stash entry is listed with its name (e.g. stash@{0} is the latest entry, stash@{1} is the one before, etc.), the name of the branch that was current when the entry was made, and a short description of the commit the entry was based on. See the screenshot below:

![Git-stash-list](./Git-stash-list.png)

`git stash apply` reapplies the changes stashed in the latest stash, and also doesn't remove the state from the stash list. See the screenshot below:

![Git-stash-apply](./Git-stash-apply.png)

`git stash show stash@{stash_index}` shows the diff between the stashed contents and the commit back when the stash entry was first created. As an example, if the stash was created when the last commit was '01234', this command shows the diff between the stashed contents and commit '01234'. See the example below:

![Git-stash-show](./Git-stash-show.png)

**Let's try to create multiple stashes, and see how we can apply a specific stash**

For the purpose of this demonstration, we have created two different stashes on two different files `wrong.js` and `wrong_2.js`. When we use `git stash apply`, we see the output as below:

![Git-multiple-stashes](./Git-multiple-stashes.png)

Let's try to apply both of the stashes one by one:

![Git-stash-apply-stash-index-0](./Git-stash-apply-stash-index-0.png)

From the screenshot above, we can see that when we apply the most recent stash, we get back the `wrong_2.js` in the working directory.

![Git-stash-apply-stash-index-1](./Git-stash-apply-stash-index-1.png)

From the screenshot above, we can see that when we apply the second stash, we get back the `wrong.js` back in the working directory. (The `wrong_2.js` file is from the stash applied in the previous step).

Sometimes, there can be merge conflicts when different stashes work on the same set of lines of the same file. In such cases, we need to resolve the merge conflict. For demonstration, we will be creating two stashes on the same file `wrong.js`. We'll then try to apply the stashes, it will lead to a merge conflict. See below:

![Git-stash-merge-conflict](./Git-stash-merge-conflict.png)

As we can see from the screenshot above, there's a merge conflict when the two stashes work on the same set of files. We need to resolve the conflict in such a case, VS code provides a convenient interface to resolve the merge conflicts.

![Git-stash-resolving-merge-conflicts](./Git-resolving-stash-merge-conflicts.png)

**How to include specific files only in the stash?**

Approach #1 : Only stage those specific file(s) to the staging area. Then use `git stash`

Approach #2 : Use `git stash -- <filename>` (for files in staging area) or `git stash --include-untracked -- <filename>` (for files in working area). See the screenshot below: 

![Git-stash-specific-file](./Git-stash-specific-file.png)

Let's talk about `git stash pop`: This command is used to remove the changes from stash and reapply them to the working copy. As an example, we are going to create a stash for creating a new file `dummy_stash_pop.txt`, and then we will use `git stash pop` on it.

![Git-stash-pop](./Git-stash-pop.png)

As we can see from the screenshot above, on using `git stash pop`, the changes from the stash were applied to the working directory, and also the stash entry was popped from the stack.

`git stash drop stash@{index}` removes the stash entry from the list of stash entries, without applying the stash changes to the working directory. See the screenshot below:

![Git-stash-drop](./Git-stash-drop.png)

`Git stash clear` clears all the stash entries. See screenshot below:

![Git-stash-clear](./Git-stash-clear.png)

By default, Git automatically generates stash message based on the branch and the commit when creating a stash entry. However, we can give custom stash message, using:

1. `git stash save <stash message>`: If the changes are staged

2. `git stash save <stash message> --include-untracked`: If the changes are in the working area and not staged. See the screenshots below:

![Git-stash-save-tracked](./Git-stash-save-tracked.png)

![Git-stash-save-untracked](./Git-stash-save-untracked.png)

**How to add a new file to an already existing git stash?**

There's a hack/workaround to do this:

1. Use `git stash pop` to apply the changes from the latest stash entry to the working directory. This also pops the entry from the stack.

2. Now stage all the files, including the new file(s) that you want to stash.

3. Now use `git stash` to stash the changes.

See demonstration below: here we demonstrated how we can add a file `newfile.js` to an existing stash.

![Git-stash-add-new-file-part-1](./Git-stash-add-new-file-part-1.png)

![Git-stash-add-new-file-part-2](./Git-stash-add-new-file-part-2.png)

**Let's also discuss how we can include a new file in a commit, without creating a new commit for it**

Refer to this link to learn about `git commit --amend` [Atlassian Documentation](https://www.atlassian.com/git/tutorials/rewriting-history#:~:text=The%20git%20commit%20%2D%2Damend,message%20without%20changing%20its%20snapshot.)

1. Stage the new file using `git add <filename>`

2. Use `git commit --amend` to include the staged file in the commit. Optionally, we can edit the commit message as well.

Let's see this in action.

![Git-commit-amend-part-1](./Git-commit-amend-part-1.png)

From the screenshot above, we can see that there's one commit in the commit history, and the commit object stores the changes from `correct.js` file.

![Git-commit-amend-part-2](./Git-commit-amend-part-2.png)

From the screenshot above, we can see that `README.txt` has been added to the commit. 

Moreover, by inspecting the commit object, we can see that the commit stores the changes from both `README.txt` and `correct.js`

However, there's one most important point to notice: The commit hash has changed ! Let's see why this happens:

1) The commit object stores a pointer to the tree object. As the tree object changes between the two commits (due to keeping track of different changes), so it changes the commit object as well. Refer the repository on Git Internals for deeper understanding: [Link to Github repository](https://github.com/girikgarg8/Git-Internals)

2) The commit object stores the timestamp at which the commit was created. Since it changes when we amend the commit, the commit object is changed.

Let's now explore about `git reflog` : 

**What is it?**

Refer to this article: [Article Link](https://graphite.dev/blog/every-engineer-should-understand-git-reflog)

Reflog is short for "Reference log". It is a file that stores the **chronological list of all changes made to the HEAD pointer** in the Git repository. Since the HEAD is a pointer to the current branch (that you are working on), and branch is a pointer to a commit, in essence, reflog tracks every commit ever made in the repository.

It's always to debunk a myth that 'HEAD always (indirectly) points to the latest commit on the current branch' --> This statement is not always correct. The `**HEAD may not always point to the latest commit on the current branch- the HEAD might point to a commit instead of pointing to a branch (Detached state)`

See this stackoverflow answer for more details: [Stackoverflow Link](https://softwareengineering.stackexchange.com/questions/363312/does-git-head-pointer-generally-usually-points-to-the-latest-last-end-commit-o)

**How is it different from `git log`?** 

See this Stackoverflow answer : [Link](https://stackoverflow.com/questions/17857723/whats-the-difference-between-git-reflog-and-log)

`git log` shows the current HEAD and its ancestry. What `git log` does it it prints the commit HEAD points to, then its parent, and so on. It traverses back through the repo's ancestry, by recursively looking up each commit's parent.

`git reflog` doesn't traverse HEAD's ancestry at all. The reflog stores the chronological order of all the changes made to the HEAD pointer. The reflog isn't part of the repo itself (it's stored separately to the commit themselves) and isn't included in pushes, fetches or clones, it's purely local.

On a side note, `git reflog` acts as a safety net. If you accidentally reset to an older commit, or rebase wrongly, or any other operation that visually "removes" commits, you can use the reflog to see where you were before and `git reset --hard` back to that ref to restore your previous state.

See the screenshot below:

![Git-reflog](./Git-reflog.png)

As we can see from the screenshot above, the `reflog` shows all the changes to the `HEAD` pointer, `HEAD@{n}` indicates `where HEAD pointer used to be n moves ago`. Also, when we amend the commit, it doesn't show up in the `commit history` but would reflect in the `reflog`.

**Let's now explore about branches in Git**

Branches are one of the most misunderstood concepts in Git.

![Git-branch-parallel-timelines](./Git-branch-parallel-timelines.png)

Many people visualize Git branches as parallel timelines of the project/repository, similar to what is depicted in the figure above. This understanding is good for visualization, but it's not entirely correct.

Precisely speaking, **`Branch is a pointer to commit in the commit history`**. It's important to know this fact so that going forward, we can understand the concept of `git reset` properly.

The correct understanding of commit is depicted in the screenshot below:

![Git-branch-pointer-to-commit](./Git-branch-pointer-to-commit.png)

Let's understand what happens, when we create a new branch using `git checkout -b <branch name>`. For the demonstration,let's consider that we checkout the `dev` branch from the `master` branch.

![Git-checkout-pointer-to-commit](./Git-checkout-pointer-to-commit.png)

As we can see from the figure above, when we checkout a new branch, we are just creating a new pointer to the current commit. Now, as we create commits in the commit history, the branch pointer also updates itself, see the figure below:

![Git-checkout-branch-pointer-move](./Git-checkout-branch-pointer-move.png)

**Let's also talk about the HEAD pointer**

'HEAD' is also one of the most misunderstood concepts in Git. 

Precisely speaking, HEAD points to the current branch you're working on, and since a branch points to a commit, HEAD indirectly points to a current (not necessarily the latest) branch on the current branch.

**Let's play around with HEAD pointer:**

![Git-HEAD-after-first-commit](./Git-HEAD-after-first-commit.png)

As we can see from the `git log`, the `HEAD` points to the `main` branch, and the `main` branch points to the `initial commit`.

Let's try to checkout a new branch, and see:

![Git-checkout-branch-pointer-HEAD](./Git-checkout-branch-pointer-HEAD.png)

From the screenshot above, we can observe that:

1. When we checkout a new branch, both the `main` and the `feature_branch` are pointing to the same commit.

2. The `HEAD` pointer is pointing to the current branch `feature_branch`.

Let's now try to create another commit in the `feature_branch` branch, and see the changes in the `feature_branch` and the `HEAD` pointer.

![Git-HEAD-pointer-moves-after-commit-on-branch](./Git-HEAD-pointer-moves-after-commit-on-branch.png)

From the screenshot above, we can see that the `main` branch points to the previous commit, and the `feature_branch` branch points to the new commit. Also, the `HEAD` pointer also moves along with the `feature_branch` branch pointer.

Let's also explore a command that we'll be using to visualize the commit history in the form of a graph:

`git log --all --decorate --oneline --graph`

Let's do a step by step analysis of this command:

`--all` shows all the branches, not just the current branch.

`--decorate` makes the `git log` display all the references (like tags, branches) that point to a commit.

`--oneline` flag condenses each commit to a single line.

`--graph` option draws an ASCII graph representing the branch structure of the commit history.

See this link [Atlassian Documentation](https://www.atlassian.com/git/tutorials/git-log) for more details, also refer to the example below:

![Git-log-outputted-format](Git-log-outputted-format.png) 

In order to see all the branches, use `git branch`. 

To switch to a particular branch, use `git checkout <branch name>`

**Let's talk about divergent branches**

Divergent branches in Git are two branches that have diverged from a common commit in the commit history. As an example, consider the figure below:

![Git-divergent-branches](./Git-divergent-branches.png)

The two branches `master` and `feature_branch` are divergent because they diverge from a common commit. Let's see this in the `git log` as well:

![Git-divergent-branches-log](./Git-divergent-branches-log.png)

From the screenshot above, we can see that there are two divergent branches from a common commit.

**Let's also learn how to read the Git log graph**

![Git-log-graph](./Git-log-graph.png)

The vertices (marked by asterisks) represents the commits in the commit history. The two parallel timelines (edges) indicate the different branches.

We can also explore `Gitlens for VS Code` extension which allows us to visualize the commit graph, authorship of code (blame annotations) etc.

**How to check which branch HEAD is pointing to?**

Option 1: Check using `git log`.

Option 2: Inspect the contents of `.git/HEAD`, see example below:

![Git-cat-HEAD](./Git-cat-HEAD.png)

Let's take a look at the `Git feature branch workflow` :

![Git-feature-branch-workflow](./Git-feature-branch-workflow.png)

The developers in a team checkout a new feature branch from the master branch. When they are done with their changes, they don't directly merge their code to the production branch, they instead merge their code to the test branch. The QA/tester folks thoroughly test the code using unit tests, integration tests, end-to-end tests etc. Once the tests are passed, it is then merged to the production branch. Usually, only few developers are given access to merge to production branch.

Hotfix branches : They allow developers to quickly fix critical issues in a production environment by working with the exact code deployed in production. 

The workflow can differ depending upon the company structure or the team preferences.

**Let's talk about tags in Git**

Tag is a pointer to a specific commit in the commit history. It is different from a branch as a tag always points to a specific commit in the commit history, whereas the branch pointer changes when new commits are created in the commit history.

The syntax for creating a tag in Git is `git tag -a <annotation> -m <tag message>`.

Both the `-a` and `-m` options are mandatory to create a tag. The annotation is a quick description of the tag, whereas the tag message is a detailed description of the tag.

Let's see this in action:

![Git-tag-commit-history](./Git-tag-commit-history.png)

![Git-tag-log](./Git-tag-log.png)

From the screenshots above, we can see that the `tag pointer` doesn't move with the commits in the commit history, whereas the `branch pointer` moves when new commits are created in the commit history.

In order to view a tag, use `git tag <tag annotation>`

![Git-tag-show](./Git-tag-show.png)

The tag object stores the tag annotation, tagger details, date and time of creating the tag, tag message and the details of the commit which the tag points to. 

To see all the tags, we can use `git show-ref --tags` or list the contents of `.git/refs/tags`, see the screenshots below:

![Git-show-ref](./Git-show-ref.png)

In order to push the git tag to Github, use the syntax `git push origin <tag name>`. 

If we wish to push all the tags, we can use `git push --tags` (not recommended though).

**Let's now talk about open source contributions**

Tips on how to contribute to an open source repository:

1. Try to go through any live link of the project, and explore around.

2. Setup the repository on your local. Consult the documentation to see the installation steps, contribution guide, Code of Conduct etc

3. Go through the list of open issues - these can be bugs, feature requests etc.

4. As a new contributor, try to look for an issue which is labelled as  a `good first issue`. 

5. Since you might not be added as a collaborator to the repository, fork the repository. Then clone the forked repository using `git clone <repository URL>` 

6. Checkout a new branch for the feature/bug that you will be working on.

7. Make the required code changes. Make sure to use semantic commit messages. See this [link](https://gist.github.com/joshbuchea/6f47e86d2510bce28f8e7f42ae84c716) for more details.

8. Raise a pull request (PR). Address comments, if any on the PR. Else congratulations on your contribution !

**Let's learn about upstream repository in Git**

**What is it?**

Refer to the diagram below:

![Git-upstream-and-downstream](./Git-upstream-and-downstream.png)

Talking about upstream and downstream: 

In terms of source control, you're downstream when you clone from a repository. Information flowed "downstream" to you.

When you make changes, you usually want to send them back "upstream" so they make it into that repository so that everyone pulling from the same source is working with all the same changes.

**What are the ways to set the upstream?**

There are two ways to set the upstream:

1. By changing the fetch URL of the `origin` remote: 

```
git remote set-url origin /original/repo
git remote set-url --push origin /your/fork
```

See example below:

![Git-remote-set-push-url-remote](./Git-remote-set-push-url-remote.png)

This is not the recommended way to set the upstream.

2. By adding a new upstream remote:

```
git remote add origin upstream /original/repo
```

Use this remote to pull the changes using `git pull upstream <branch name>`. See the screenshots below:

![Git-remote-add-upstream](./Git-remote-add-upstream.png)

![Git-pull-upstream-main](./Git-pull-upstream-main.png)

If you're aiming to contribute to Google Summer of Code (GSoC), there's a proposal document that you need to submit to the organization. See this [example](https://drive.google.com/file/d/1tG33LLWhy2lApiXaPl1ukRv7xho0V-Ne/view) for reference.

## Let's now learn about how we can fix mistakes in Git ##

***Before we go ahead, there's an important lesson : `git checkout` is not only used to switch or create branches -  it can also be used to revert changes, checkout to a commit etc .***

`git checkout -- <file>` restores the file to the last staged version, discarding any changes in the working area. See the [documentation](https://git-scm.com/docs/git-checkout) for more details.

Let's see this in action: 

![Git-checkout-file](./Git-checkout-file.png)

As we can see from the screenshot above, the contents of `wrong_2.txt` are replaced by the last staged version.

The documentation also mentions `When the <tree-ish> is given, overwrite both the index and the working tree with the contents at the <tree-ish>.` What this means is that, by using `git checkout <commit hash> -- <file>`, we can revert the file to its version in that specific commit.

Let's see this in action:

![Git-checkout-commit-file](./Git-checkout-commit-file.png)

As we can see from the screenshot above, the contents of `wrong_3.js` are reverted to those of the specified commit.