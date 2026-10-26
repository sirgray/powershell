git --version

# If not installed
sudo apt-get update && sudo apt-get install -y git


# Set global username
git config --global user.name "Student Developer"

# Set global email address
git config --global user.email "student@example.com"

# Set default main branch name to 'main'
git config --global init.defaultBranch main

# Verify settings list
git config --list


# Create a new project directory
New-Item -ItemType Directory -Name "GitDemo"
Set-Location -Path "GitDemo"

# Turn directory into a Git repository
git init

Get-ChildItem -Force

git status

Set-Content -Path "readme.md" -Value "# My Web App"
git status

git add readme.md
git status

git commit -m "Initial commit: Add readme file"

***********************************

git log

# Append a line to readme.md
Add-Content -Path "readme.md" -Value "Version 1.0 - Operational"

# Run git diff
git diff

# Create temporary files
Set-Content -Path "debug.log" -Value "debug info"
New-Item -ItemType Directory -Name "tmp" -Force
Set-Content -Path "tmp/cache.dat" -Value "cache data"

# Create .gitignore file and define rules
Set-Content -Path ".gitignore" -Value "*.log`ntmp/"
# Accidental overwrite
Set-Content -Path "readme.md" -Value "ACCIDENTAL DELETION OF CONTENT"

# Verify broken file state
Get-Content -Path "readme.md"

# Revert working file back to last committed version
git restore readme.md

# Verify content has been recovered
Get-Content -Path "readme.md"

git switch -c feature/login

# Create a feature-specific file
Set-Content -Path "login.py" -Value "# Login authentication engine"

# Stage and commit on the new branch
git add login.py
git commit -m "Add login script on feature branch"

***************************************

Focus: Repository creation, identity configuration, file staging, and initial commits

Environment: Killercoda Terminal (pwsh)

Scenario
You are starting a new software project called InventoryApp. You need to initialize your repository, configure your developer identity, stage multiple files, and make structured commits.

Tasks:
1.	Create a folder named InventoryApp and turn it into a local Git repository.
2.	Configure your identity for this project (Name: "App Dev", Email: "dev@corp.com").
3.	Create two files:
o	app.py containing text: "# Inventory Tracker App"
o	config.txt containing text: "DB_HOST=localhost"
4.	Stage only app.py and commit it with the message "Add core app entrypoint".
5.	Run git status to verify config.txt remains untracked.
6.	Stage and commit config.txt with the message "Add local configuration".












