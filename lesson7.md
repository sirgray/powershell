
New-Item -ItemType Directory -Name "Lesson7_Mod1" -Force
Set-Location -Path "Lesson7_Mod1"
git init
git config user.name "Ops Student"
git config user.email "student@lab.com"

Set-Content -Path "config.ini" -Value "ENV=PROD"
git add config.ini
git commit -m "Initial infrastructure config"


# 1. Inspect the HEAD pointer reference
Get-Content -Path ".git/HEAD"

# 2. Retrieve the full 40-character SHA-1 hash of the latest commit
$commitHash = (git rev-parse HEAD)
Write-Host "HEAD Commit SHA-1 : $commitHash" -ForegroundColor Cyan

# 3. Inspect internal object type and raw contents using git cat-file
git cat-file -t $commitHash
git cat-file -p $commitHash

# 4. Extract the Tree SHA-1 from the commit and inspect its contents
$treeHash = (git rev-parse "$commitHash^{tree}")
Write-Host "Root Tree SHA-1   : $treeHash" -ForegroundColor Yellow
git cat-file -p $treeHash


***************************************

Set-Location ..
New-Item -ItemType Directory -Name "Lesson7_Mod2" -Force
Set-Location -Path "Lesson7_Mod2"
git init -b main
git config user.name "Ops Student"
git config user.email "student@lab.com"

Set-Content -Path "app.py" -Value "API_KEY = 'DEV_123'`nPORT = 8080"
git add app.py
git commit -m "Base app setup"

git checkout -b feature/auth
Set-Content -Path "app.py" -Value "API_KEY = 'SECRET_999'`nPORT = 8080`nAUTH = True"
git add app.py
git commit -m "Enable auth and rotate API key"

git checkout main
Set-Content -Path "readme.md" -Value "# Production Docs"
git add readme.md
git commit -m "Add readme on main"


# 1. Double-dot range: Show commits on feature/auth not yet merged into main
Write-Host "=== Commits unique to feature/auth (main..feature/auth) ===" -ForegroundColor Cyan
git log main..feature/auth --oneline

# 2. Triple-dot range: Show divergent commits on both branches with direction arrows
Write-Host "=== Symmetric difference (main...feature/auth) ===" -ForegroundColor Yellow
git log main...feature/auth --oneline --left-right --graph

# 3. Pickaxe search: Find which commit introduced or removed 'SECRET_999' across all branches
Write-Host "=== Pickaxe Forensics for 'SECRET_999' ===" -ForegroundColor Green
git log --all -S "SECRET_999" --pretty=format:"%h | %an | %ad | %s" --date=short

********************************************

Set-Location ..
New-Item -ItemType Directory -Name "Lesson7_Mod3" -Force
Set-Location -Path "Lesson7_Mod3"
git init -b main
git config user.name "Ops Student"
git config user.email "student@lab.com"

Set-Content -Path "deploy.ps1" -Value "Write-Host 'Step 1: Build'"
git add deploy.ps1
git commit -m "Commit 1: Build step"

Add-Content -Path "deploy.ps1" -Value "Write-Host 'Step 2: Critical Production Logic'"
git add deploy.ps1
git commit -m "Commit 2: Critical logic"

Add-Content -Path "deploy.ps1" -Value "Write-Host 'Step 3: Broken Buggy Code'"
git add deploy.ps1
git commit -m "Commit 3: Broken code"



# 1. Accidentally wipe out Commit 2 and Commit 3 using hard reset
Write-Host "=== Simulating accidental hard reset to Commit 1 ===" -ForegroundColor Red
git reset --hard HEAD~2
git log --oneline

# 2. Inspect local HEAD movement history using reflog
Write-Host "=== Inspecting Reflog for Lost Commits ===" -ForegroundColor Cyan
git reflog -n 5

# 3. Recover Commit 2 using reflog index HEAD@{1}
Write-Host "=== Recovering Commit 2 via Reflog ===" -ForegroundColor Green
git reset --hard "HEAD@{1}"
git log --oneline
Get-Content -Path "deploy.ps1"


