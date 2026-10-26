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





