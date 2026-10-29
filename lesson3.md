	Go to GitHub →Settings →Developer Settings →Personal Access Tokens →Tokens (classic).
	Click Generate new token.
	Select scope: Check repo (Full control of private repositories).
  Copy the generated string (e.g., ghp_xK89234823472938472394823).


New-Item -ItemType Directory -Name "RemoteDemo"
Set-Location -Path "RemoteDemo"
git init
git branch -M main
git config --global user.email "your.email@example.com"
git config --global user.name "Your Name"

Set-Content -Path "readme.md" -Value "# Remote Demonstration Project"
git add readme.md
git commit -m "Initial local commit"


git remote add origin https://github.com/<sirgray>/RemoteDemo.git

git remote -v

git push -u origin main



git config --global credential.helper store

# Step 1: Download remote metadata without modifying working files
git fetch origin

# Step 2: Compare local main against fetched remote state
git log main..origin/main --oneline

# Step 3: Safely merge once verified
git merge origin/main





# Move up to parent workspace
Set-Location -Path ..

# Clone project into a target folder named ClonedDemo
git clone https://github.com/sirgray/RemoteDemo ClonedDemo

# Step inside
Set-Location -Path "ClonedDemo"

# Verify remote tracking configuration
git remote -v







