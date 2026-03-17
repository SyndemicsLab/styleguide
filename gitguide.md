# General Git Tooling

This serves as a general git cheat sheet specific to our lab. After [setup]{#sec-setup} it is meant to have copy-able commands and assumes we will be interacting directly with our lab's GitHub page. A generic cheat sheet is provided by git [here]{`https://git-scm.com/cheat-sheet`}. If you are looking for a quick reference to the git workflow, refer to the [git workflow]{#sec-gitworkflow} section.

## Special Files, Folders, and Terminology

Git makes use of special files and folders. In this guide, we will focus on two:

- `.git/`
- `.gitignore`

Every git repository is denoted with a folder named `.git/`. This contains details specific to the repository and should not be manually edited unless you know exactly what you are doing (the use cases for editing this folder are so few and far between that I would advise making a new repository before editing the contents of this folder).

The file `.gitignore` contains a list of files to ignore during tracking. Files listed here are not "checked into" version control but instead kept local. This generally includes building and rendering files as well as large data files we do not want to store on GitHub. An example is shown below:

```git
ignored_folder/                     # ignore the folder and the contents

ignored/*                           # ignore the contents, but not the folder
!ignored/file_we_want_to_track.md   # `!` provides an exception

ignored_file.md
ignored_*.md                        # `*` is a wildcard for all matches
```

Finally, git makes use of several key terms. A list of key terms is included below, but a more complete list is [provided by git]{`https://git-scm.com/docs/gitglossary`}:

- local: Your personal workspace, e.g. your computer, your cluster space, etc.
- remote: A location outside your personal workspace that houses a copy of your repository, generally GitHub
- repository: A collection of code files tracked by git
- clone: A copy of (or the action of copying) the repository
- branch: A divergence from the main working project, usually a work in progress
- stage: Prepare a file to be tracked by the repository
- commit: Create a persistent track point for the files in the repository
- merge: Combine two branches, generally merging feature into main

## Setup {#sec-setup}

### Git

Git is a distributed version control system used by people developing code to ensure consistency and backups are readily available. Created by Linus Torvalds, it has been said to mean various different things ranging from ["global information tracker" to "stupid content tracker"]{`https://github.com/git/git/blob/e83c5163316f89bfbde7d9ab23ca2e25604af290/README`}.

That being said, we must ensure we have access to git on our local computers. This is unique by each operating system and the constraints of the BMC IT department. Currently, the installer is whitelisted by RTP (Research Technology Program) so it should be able to be installed, but if you require admin permissions make sure to contact RTP and ask for them to install git.

- [Windows]{`https://git-scm.com/install/windows`}
- [Mac]{`https://git-scm.com/install/mac`}
- [Linux]{`https://git-scm.com/install/linux`}

### GitHub

All users should already have a GitHub account setup and registered with the lab's GitHub page. For more information about making a GitHub account please refer to the [GitHub documentation]{`https://docs.github.com/en/get-started/start-your-journey/creating-an-account-on-github`}.

When starting a project, there are two potential situations: starting or editing a project.

#### Starting a Project {#sec-startproject}

If starting a new project, first determine the purpose of the project. Will you be creating an analysis project, a library, a command line interface tool, a UI tool, etc.? This dictates if we are using a template and if we should initialize with a `.gitignore` file.

Once decided on follow the steps below:

1. Go to the lab's [GitHub page]{`https://github.com/SyndemicsLab`}
2. Select the green "New" button above the repositories
3. Fill out the Repository Name and Description
4. Start with a private visability (unless you know you want it to be public)
5. Choose the template OR add the `.gitignore`, `README`, and the `GNU Affero General Public License v3.0`
6. Select "Create repository"

This will create a new repository on GitHub. Make sure it is what you want. The rest is identical to editing an existing project.

#### Editing an Existing Project

In order to edit an existing project, we first must have a repository already on GitHub. If you do not have a repository, then return to [starting a project]{#sec-startproject}. Also note: cloning and pushing to GitHub often requires you have an SSH connection. To set this up, please refer to the documentation listed on [GitHub]{`https://docs.github.com/en/authentication/connecting-to-github-with-ssh`}. You will need a key for each local device you want to clone the repository.

1. Navigate to the repository you want to edit
2. Select the green dropdown button labeled "Code"
3. Copy the link
4. Open a local terminal to use git (git bash for Windows, terminal on mac or linux)
   1. Optionally, you can use GitHub Desktop or your IDE's clone feature. We're explaining the terminal because it is the most generic.
5. Navigate to where you want the repository located then type the command replacing the `<pasted-uri>` with the link you copied earlier:
   1. `git clone <pasted-uri>`

You now should have a copy of the repository on your local system! If you have questions/troubles please first verify if you're using SSH that you have an SSH key setup with GitHub and that your local machine has git installed.

## Git Workflow {#sec-gitworkflow}

The git workflow generally follows the pattern:

1. Branch
2. Edit
3. Stage and Commit
4. Pull Request and Merge

We will explore each of these steps now.

### Branch

Git repositories all have a core branch. In our repositories, that branch is named `main` and protected so that the only way to edit it is after code has been reviewed. Thus, we must always branch off of main to make changes.

The commands for branching are:

- `git branch` - Show all the branches available
- `git checkout <other-branch-name>` - Change the branch you are on
- `git checkout -b <new-branch-name>` - Create a new branch and move to it

### Edit

Editing is the largest stage of the git process. None of these commands are required, but rather potentially helpful tools to help you keep track of changes.

The editing commands are:

- `git status` - Show the current status of all your files
- `git diff HEAD` - Show all changes between the current files and the last commit
- `git stash` - Put all staged and unstaged changes on the "stash stack"
- `git stash pop` - Apply the changes from the last thing added to the "stash stack"
- `git log` - View the history of the repository
- `git reset --hard` - Delete all staged and unstaged changes. *BE CAREFUL!*

### Stage and Commit

The stage and commit section focuses on creating a permanent snapshot of the code status. Staging has to happen before committing as only staged files get committed.

The stage and commit commands are:

- `git add <file>` - stage a specific file
- `git add -a` - stage all files
- `git reset` - unstage everything
- `git commit -m 'message'` - make a commit with a message
- `git push` - copy all commits from your local to the remote
- `git pull` - copy all commits from the remote to your local

### Pull Request and Merge

The final step is the pull request and merge. This focuses on moving the code edited on the branch into the main branch as a "finalized" state. This stage happens primarily on GitHub and follows the [GitHub documentation]{`https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request`}. Note, we have a template  for pull requests within our lab that require an associated task item as well as a brief description and two tasks to verify completion.

Once the pull request (PR) is approved by a reviewer, the person who issued the PR should squash and rebase the branch onto main and delete the branch. That's all there is to it!
