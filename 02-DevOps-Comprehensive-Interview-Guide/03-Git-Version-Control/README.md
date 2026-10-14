# Section 3: Git and Version Control

---

## Q17. What is Git?

**🔑 Keywords:** distributed version control, commit history, branching, collaboration, Git vs GitHub

**Beginner-Friendly Answer:**
Git is a distributed time-machine and collaboration tool for code. Every time you make a commit, Git saves a snapshot of your project. If you make a mistake, you can rewind back to any point in history. Because it's distributed, every team member has a full copy of the project history on their own computer. GitHub is the cloud platform where teams host and share their Git repositories.

**Detailed Answer:**
Git is a distributed version-control system that tracks source-code changes and supports collaboration, branching, merging, and recovery of earlier versions.

### Cross-question: What is the difference between Git and GitHub?

**Answer:**
Git is the version-control tool. GitHub is a hosting and collaboration platform for Git repositories.

---

## Q18. What is a Git repository?

**🔑 Keywords:** local repo, remote repo, `.git` metadata, commit graph, working tree

**Beginner-Friendly Answer:**
A Git repository (or "repo") is the directory containing all your project files plus a hidden `.git` folder that tracks every change, branch, tag, and commit ever made. A **local repo** lives right on your laptop, while a **remote repo** lives on a cloud service like GitHub so your team and CI/CD pipelines can access it.

**Detailed Answer:**
A repository stores project files and their version history. A local repository is on your machine, while a remote repository is hosted on a service such as GitHub.

---

## Q19. Explain git clone, git pull, and git fetch.

**🔑 Keywords:** `clone` (full copy), `fetch` (download without merge), `pull` (fetch + merge), inspecting remote changes

**Beginner-Friendly Answer:**
- **`git clone`:** Copies an entire remote repository from GitHub to your computer for the first time.
- **`git fetch`:** Checks the remote repo and downloads new commits to your computer *without* changing your current working files (great for safely inspecting what others wrote before merging).
- **`git pull`:** Does a `git fetch` AND automatically merges the new commits directly into your current branch.

**Detailed Answer:**
- `git clone` creates a local copy of a remote repository.
- `git fetch` downloads remote updates without automatically integrating them into the current branch.
- `git pull` fetches updates and then integrates them into the current branch, using the configured merge or rebase behavior.

### Cross-question: Why might you prefer git fetch during troubleshooting?

**Answer:**
It lets you inspect remote changes before integrating them into your current working branch.

---

## Q20. What is a branch?

**🔑 Keywords:** line of development, commit pointer, feature branching, pull requests, main/master branch

**Beginner-Friendly Answer:**
A branch is an isolated workspace for writing new code without disturbing the working `main` branch. Think of it like making a photocopy of a document to write rough notes on: if the notes turn out great, you merge them back into the original document; if they fail, you simply discard the photocopy without damaging the main document.

**Detailed Answer:**
A branch is a movable reference to a line of development. Developers can use separate branches to work on features or fixes without immediately changing the main branch.

---

## Q21. What is a merge conflict?

**🔑 Keywords:** concurrent edits, conflicting lines, manual resolution, merge tools, Git conflict markers

**Beginner-Friendly Answer:**
A merge conflict happens when two developers edit the exact same lines of code in the same file on different branches and try to merge them together. Git doesn't know which version is the correct one, so it halts the merge, places conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`), and asks a human to decide what to keep.

**Detailed Answer:**
A merge conflict occurs when Git cannot automatically reconcile changes, often because different changes affect the same lines. A developer must review the conflicting content and resolve it.

---

## Q22. What is the purpose of .gitignore?

**🔑 Keywords:** untracked files, build artifacts, node_modules, `.env` files, leaked credentials in git history

**Beginner-Friendly Answer:**
The `.gitignore` file lists files and folders that Git should completely ignore and never commit — such as `node_modules/`, temporary log files, and local `.env` files. Remember: if you accidentally committed a secret password earlier, adding it to `.gitignore` afterwards does NOT delete it from Git's past commit history! You must immediately rotate the secret and scrub the repo history.

**Detailed Answer:**
It tells Git which untracked files and directories to ignore, such as local build outputs, temporary files, or local environment files.

### Cross-question: Does adding a secret file to .gitignore remove it from Git history if it was committed earlier?

**Answer:**
No. It prevents future untracked files from being added automatically, but previously committed secrets remain in repository history until appropriately removed. Exposed credentials should be rotated immediately.

---

## Q23. Why is Git important in GitOps?

**🔑 Keywords:** single source of truth, declarative configuration, audit trail, automated drift reconciliation, Argo CD

**Beginner-Friendly Answer:**
In GitOps, Git is the single source of truth for your production infrastructure. Every change to your cluster (like updating an image tag or increasing replicas) is submitted as a Git commit or Pull Request. This gives you code reviews, audit trails, and instant rollbacks. If someone changes a setting live in the cluster without updating Git, GitOps controllers (like Argo CD) detect the drift and automatically revert it.

**Detailed Answer:**
Git stores the desired application configuration and provides version history, review, and traceability. GitOps controllers compare the declared state in Git with the actual cluster state and reconcile differences according to their configuration.

### Cross-question: What happens if someone manually changes a Kubernetes resource?

**Answer:**
Argo CD may detect a difference between the live resource and the desired configuration. Depending on sync and self-heal settings, it may report drift or reconcile the live state back to the declared state.
