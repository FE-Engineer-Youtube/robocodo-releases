# Using RoboCodo

[Home](../README.md) · [Getting started](getting-started.md) · [Troubleshooting](troubleshooting.md)

Start here after completing machine preparation, **Test connection**, and **Connect**. Your repositories and coding tasks live on Linux; the Windows app is how you manage them. Codex must be installed and authenticated as the connected Linux account to run coding tasks.

## 1. Select a project and repository

1. Connect to the intended development machine.
2. Select a project from its configured projects directory.
3. Select the Git repository you want to change. A **repository** contains the project's files and their recorded history; a project may contain more than one repository.
4. Check the repository's current branch and any existing changes. A **branch** is a named line of development, used to keep work separate until it is ready to combine.

If your project is unavailable, check the [projects folder](troubleshooting.md#projects-folder-unavailable) before creating a task. Access to the Linux machine does not automatically grant access to your Git hosting provider; fetching and publishing require that Linux account's Git credentials and repository permissions.

## 2. Create an isolated task workspace

1. Create a task workspace for the selected repository using the isolated worktree option.
2. Check the starting branch and the task's branch/workspace details before starting it.
3. Enter a prompt describing the outcome you want and any constraints. For a first task, choose a small change that you can easily review.
4. Start the task and watch its progress.

For example: “Improve the introduction in this project's README. Explain who the project is for and how to run its existing checks. Keep the change limited to documentation.”

### Isolated worktree or shared checkout?

A **checkout** is the working set of files for a repository. A **Git worktree** is another working directory attached to the same repository history.

| Workspace | What it means | How to use it safely |
| --- | --- | --- |
| Isolated worktree | The task works in its own directory, usually on its own branch, while sharing the repository's Git history. | Prefer this for new tasks. Review and commit changes in that task's workspace. |
| Shared checkout | Work happens directly in the existing working directory. Edits and branch changes affect anyone or any process using it. | Coordinate access and avoid overlapping tasks or manual edits in the same checkout. |

Isolation separates working files, not Linux permissions, credentials, or all external effects. A worktree is not a security sandbox. Tasks can still interact with shared services or other resources available to the account. Do not assume a worktree installs dependencies or copies uncommitted changes from another checkout.

## 3. Steer or stop an active task

1. Read the task's progress and messages, including questions or reported failures.
2. Send a follow-up instruction in the task to refine scope, answer a question, or correct its direction. Be specific about the result you want.
3. Use the task's stop control when you want execution to stop. Wait for its status to settle before performing Git operations or manually editing the same workspace.
4. Review the current changes before continuing or starting another task.

Stopping a task is not an undo operation. Changes already written may remain, and work already performed against external systems is not automatically reversed.

## 4. Review the changes

1. Open the task workspace's Git changes/review view and confirm the selected repository and branch.
2. Read the **diff**, the comparison showing added and removed lines, for every changed file. Include new or untracked files in your review.
3. Check that the change matches your request, contains no credentials or unrelated edits, and does not accidentally remove needed files.
4. Ask the task to correct anything that is unclear or incomplete.
5. Run the project's documented checks in that Linux task workspace, or ask the task to run them and inspect the results. Review what ran and any failures; a task's completion message alone is not verification.

An edit is not saved in Git history until it is committed. It can still exist on disk after you switch views or stop a task.

## 5. Commit, publish, and open a pull request

A **commit** records a set of changes in the repository's local history. **Publishing**, usually a Git push, sends commits to a **remote**, the repository hosted on a service such as GitHub. Here, “local Git history” lives on the Linux machine. A **pull request** asks collaborators to review and merge a branch into another branch.

1. In the task workspace, confirm the repository, branch, and files being included. Include only the changes you have reviewed.
2. Use the commit action and write a short message explaining the change. Ensure that Git's author name and email are configured for this Linux account or repository if Git requests them.
3. Use the publishing action for the task branch. Check the destination repository and remote before sending commits. The Linux account needs Git authentication and push permission there.
4. Open a pull request from the published task branch using the available pull-request action, or open your hosting provider in a browser and create it there.
5. Check the source branch, target branch, title, and description. Summarize the result and the checks performed, then follow your project's review process.

Committing does not publish; publishing does not merge a pull request. If publishing fails, read the error before retrying. See [slow Git operations](troubleshooting.md#slow-git-fetch-or-push) for delays. Do not force-push to resolve an unexpected history conflict.

## 6. Switch branches safely

Before switching a branch in a workspace:

1. Stop any task that is editing that workspace and wait for it to finish stopping.
2. Inspect the Git changes, including untracked files.
3. If there are uncommitted changes, preserve them before switching. Commit them on the intended branch, or use a separate isolated task workspace for the other work. If you are familiar with Git, a **stash** can temporarily store changes, but check that any untracked files you need are included and review the result when restoring it.
4. Once the current workspace is clean and the intended changes are preserved, switch branches and verify the selected branch before starting another task.

Git may allow a branch switch with some uncommitted changes and carry them to the new branch, or refuse a switch that would overwrite files. Do not rely on a successful switch as evidence that changes were saved. Do not discard changes or force a switch simply to clear a warning.

If the desired branch is already checked out in another worktree, return to that workspace or choose a different task branch. Avoid changing branches underneath an active task or someone using a shared checkout.
