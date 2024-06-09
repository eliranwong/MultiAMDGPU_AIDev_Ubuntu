# git

https://guides.github.com/

https://guides.github.com/introduction/git-handbook/

# Global Configuration

> git config --global user.name myusername

> git config --global user.email myusername@email.com

# User Configurations

Edit file ~/.gitconfig

> nano ~/.gitconfig

* [user]<br>
* username = myusername<br>
* email = myusername@email.com

# Common Commands

e.g.

> git add README.md

> git commit -m "update file(s)"

> git push

or

> git push --set-upstream origin main

or

> git push -u origin main

or

> git push --set-upstream origin main

To manage branches:

To create:

> git checkout -b new_branch

To switch to a remote branch:

> git branch -r

> git checkout branch_name

To delete:

> git branch -D branch_name

To list all non-staged files:

> git ls-files --other --modified --exclude-standard

To overwrite changes in local files:

> git fetch --all

> git reset

Trouble-shoot hanging for pushing large files:

> git config --global http.postBuffer 157286400

read more about this solution at https://stackoverflow.com/questions/15843937/git-push-hangs-after-total-line

# Discard Local Changes

There are different ways to discard local changes and pull the latest changes from the GitHub repository, depending on your situation and preference. Here are some common methods:

- If you want to discard all local changes and commits that are not pushed to the remote repository, you can use `git reset --hard` followed by `git pull`. This will reset your local repository to the same state as the remote repository and then pull the latest changes. For example¹²:

```bash
git reset --hard
git pull
```

For example,

```bash
git reset --hard origin/main
```

- If you want to discard only local changes that are not committed, but keep your local commits that are not pushed to the remote repository, you can use `git checkout .` followed by `git pull`. This will revert all modified files to their original state and then pull the latest changes. For example²:

```bash
git checkout .
git pull
```

- If you want to temporarily save your local changes and apply them later, after pulling the latest changes from the remote repository, you can use `git stash` followed by `git pull` and `git stash pop`. This will store your local changes in a stash, update your local repository with the remote changes, and then reapply your stashed changes. For example²:

```bash
git stash
git pull
git stash pop
```

# Create Pull Request

```
Editing a GitHub repository and creating a pull request involves several steps. Here’s a detailed guide to help you through the process:

### Step 1: Fork the Repository
1. **Navigate to the Repository**: Go to the GitHub repository you want to contribute to.
2. **Fork the Repository**: Click the "Fork" button at the top right corner of the repository page. This will create a copy of the repository under your GitHub 
account.

### Step 2: Clone the Forked Repository
1. **Clone the Repository**: Open your terminal or command prompt.
2. **Get the Repository URL**: Go to your forked repository on GitHub, click the "Code" button, and copy the URL (either HTTPS or SSH).
3. **Run the Clone Command**:
    ```bash
    git clone https://github.com/your-username/repository-name.git
    ```
    Replace `your-username` with your GitHub username and `repository-name` with the name of the repository.

### Step 3: Create a New Branch
1. **Navigate to the Repository Directory**:
    ```bash
    cd repository-name
    ```
2. **Create and Switch to a New Branch**:
    ```bash
    git checkout -b your-branch-name
    ```
    Replace `your-branch-name` with a descriptive name for your branch.

### Step 4: Make Your Changes
1. **Edit the Code**: Open the files you want to edit in your preferred code editor and make your changes.
2. **Save Your Changes**: Save the files after making the necessary modifications.

### Step 5: Commit Your Changes
1. **Stage the Changes**:
    ```bash
    git add .
    ```
    This stages all the changes. You can also stage specific files by replacing `.` with the file names.
2. **Commit the Changes**:
    ```bash
    git commit -m "Your commit message"
    ```
    Replace `"Your commit message"` with a descriptive message about the changes you made.

### Step 6: Push the Changes to GitHub
1. **Push the Changes**:
    ```bash
    git push origin your-branch-name
    ```
    Replace `your-branch-name` with the name of your branch.

### Step 7: Create a Pull Request
1. **Go to Your Forked Repository on GitHub**: Navigate to your forked repository on GitHub.
2. **Compare & Pull Request**: You should see a prompt to create a pull request for the branch you just pushed. Click the "Compare & pull request" button.
3. **Fill Out the Pull Request Form**:
    - **Title**: Provide a descriptive title for your pull request.
    - **Description**: Add a detailed description of the changes you made and why they are necessary.
4. **Create the Pull Request**: Click the "Create pull request" button.

### Step 8: Respond to Feedback
1. **Monitor the Pull Request**: Keep an eye on your pull request for any feedback or requested changes from the repository maintainers.
2. **Make Additional Changes if Needed**: If changes are requested, make the necessary edits, commit them, and push them to the same branch. The pull request will 
automatically update with your new commits.

### Step 9: Merge the Pull Request (if you have permissions)
1. **Merge the Pull Request**: If you have the necessary permissions and the pull request is approved, you can merge it. Otherwise, the repository maintainers will 
handle the merge.

### Additional Tips
- **Stay Updated**: Regularly pull changes from the original repository to keep your fork up-to-date.
    ```bash
    git remote add upstream https://github.com/original-owner/repository-name.git
    git fetch upstream
    git merge upstream/main
    ```
    Replace `main` with the default branch of the original repository if it's different.

By following these steps, you can effectively contribute to a GitHub repository and create a pull request.
```
