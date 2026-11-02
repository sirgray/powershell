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









