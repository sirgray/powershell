# Create setup repo and initial file
New-Item -ItemType Directory -Name "UndoDemo"
Set-Location -Path "UndoDemo"
git init
git config --global init.defaultBranch main
git config --global user.name "Ops Student" 
git config --global user.email "ops@lab.com"
Set-Content -Path "config.json" -Value '{"status": "active"}'
git add config.json
git commit -m "Add core config"


# Make an unwanted edit
Set-Content -Path "config.json" -Value '{"status": "BROKEN_TEST"}'

git restore config.json

Get-Content -Path "config.json"

# Create temporary junk files
Set-Content -Path "temp1.tmp" -Value "junk"
Set-Content -Path "temp2.tmp" -Value "junk"

# Dry run: See what WOULD be deleted without actually deleting
git clean -n

# -f = force, -d = include untracked directories
git clean -fd


# Modify tracked file and create secret file
Add-Content -Path "config.json" -Value '{"debug": true}'
Set-Content -Path "secret.key" -Value "SUPER_SECRET_TOKEN"

# Accidental bulk staging
git add .
git status

git restore --staged secret.key
git status


# Create a bad commit
Set-Content -Path "buggy_feature.py" -Value "raise Exception('Crash!')"
git add buggy_feature.py
git commit -m "Add experimental feature"

# View commit hash
git log --oneline


# Revert the latest commit specified by hash or HEAD
git revert HEAD --no-edit

******************************************************


Set-Content -Path "step1.txt" -Value "Step 1"
git add step1.txt
git commit -m "Commit 1"

Set-Content -Path "step2.txt" -Value "Step 2"
git add step2.txt
git commit -m "Commit 2"


# Reset back 1 commit keeping staged changes
git reset --soft HEAD~1
git status

# Re-commit step2
git commit -m "Commit 2 again"

# Hard reset back 1 commit (Wipes working directory!)
git reset --hard HEAD~1
Get-ChildItem

# Make a commit with a typo
Set-Content -Path "auth.py" -Value "# Auth logic"
git add auth.py
git commit -m "Aded auth logic"

# Amend the commit message
git commit --amend -m "Add authentication logic"
git log -1 --oneline


# Student creates commit but forgets auth_helper.py
Set-Content -Path "auth_helper.py" -Value "# Helper functions"

# Stage the missed file
git add auth_helper.py

# Combine missed file into previous commit without changing the message
git commit --amend --no-edit


# Create valuable work
Set-Content -Path "critical_data.py" -Value "# 500 lines of critical code"
git add critical_data.py
git commit -m "Add critical business logic"

# DISASTER: Student accidentally hard resets!
git reset --hard HEAD~1
Get-ChildItem   # critical_data.py is GONE!


# Step 1: Open reflog history
git reflog

# Step 2: Reset HEAD directly back to the lost commit hash (f9e8d7c)
git reset --hard f9e8d7c

# Step 3: Verify restoration!
Get-ChildItem
Get-Content -Path "critical_data.py"


# Create nested temporary directories and ignored files
New-Item -ItemType Directory -Name "build" -Force
Set-Content -Path "build/app.exe" -Value "binary"
Set-Content -Path "debug.log" -Value "log data"

# Clean all untracked files AND directories (-d) including ignored files (-x)
git clean -fdx


**********************************************


Task Set 1:  Checkpoint 

Focus: Restoring files, cleaning untracked files, and unstaging changes
Environment: Killercoda Terminal (pwsh)
Scenario
You are working on an automation script repository called NetworkOps. You made bad local edits to a core file, accidentally staged temporary logs, and created junk build files. You must clean your workspace using proper Git undo commands.
Tasks:
1.	Create a directory named NetworkOps and initialize a Git repository inside it.
2.	Create router.py with text "print('Router Config v1')". Stage and commit it with message "Initial router setup".
3.	Edit router.py to add text "BROKEN CODE", but do not stage it.
4.	Create an untracked file temp_scan.tmp containing text "scanning...".
5.	Use git restore to revert router.py back to its committed state.
6.	Use git clean -fd to remove temp_scan.tmp. Verify git status reports a completely clean working tree.



Task Set 2: Second Checkpoint

Focus: Commit amending, safe reverting, accidental hard reset recovery via reflog
Environment: Killercoda Terminal (pwsh)
Scenario
You are managing an enterprise app. You need to fix a typo in your last commit message, safely revert a bad feature commit, and recover a feature destroyed by an accidental git reset --hard.
Tasks:
1.	Create a file database.py with text "# Database engine". Stage and commit with message "Aded database".
2.	Amend the commit message to fix the typo so it reads "Add database engine".
3.	Create a commit that adds bad_patch.py with text "CRASH". Use git revert to undo this commit safely.
4.	Create a file gold_feature.py with text "Valuable Code", stage and commit it with message "Add gold feature".
5.	Run git reset --hard HEAD~1 to simulate an accidental hard reset deleting gold_feature.py.
6.	Use git reflog to locate the commit hash of "Add gold feature" and recover gold_feature.py completely.


Task Set 3: Advanced History Restoration & Refactoring

Focus: Commit amending, selective resets (--soft vs. --hard), and emergency reflog recovery
Environment: Killercoda Terminal (pwsh)
Scenario
You are refactoring a core microservice script. During your work, you accidentally committed code with a typo in the log message, staged temporary debug files by mistake, and performed an unintended hard reset that wiped out an important feature branch. You must clean up the workspace and restore lost commits using Git's recovery tools.
Tasks:
1.	Initialize a new Git repository in a folder named MicroserviceOps. Configure your identity ("Dev Student", "dev@lab.com").
2.	Create app.py with text "print('Service Running')". Stage and commit it with message "Initial service code".
3.	Modify app.py to add "print('Debug Enabled')". Stage and commit with the typo message "Aded debug log".
4.	Use git commit --amend to fix the commit message typo to read "Add debug log" without modifying the file content.
5.	Create a new file secret_key.pem with text "PRIVATE_KEY". Modify app.py to add "print('v2.0')". Stage both files (git add .), then use git restore --staged to unstage secret_key.pem while keeping app.py staged. Commit app.py with message "Release v2.0".
6.	Run git reset --hard HEAD~1 to simulate an accidental wipe out of your "Release v2.0" commit.
7.	Use git reflog to locate the hash of "Release v2.0" and restore your repository back to that state. Verify using git log -1 --oneline and Get-Content app.py.
















