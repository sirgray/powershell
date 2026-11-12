
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

**********************************
Set-Location ..
New-Item -ItemType Directory -Name "Lesson7_Mod4" -Force
Set-Location -Path "Lesson7_Mod4"
git init -b main
git config user.name "Ops Student"
git config user.email "student@lab.com"

Set-Content -Path "core.py" -Value "# Core Engine"
git add core.py
git commit -m "Initial core commit"

git checkout -b feature/cache
Set-Content -Path "cache.py" -Value "def init(): pass"
git add cache.py
git commit -m "feat: add cache skeleton"

Add-Content -Path "cache.py" -Value "`ndef get(): pass"
git add cache.py
git commit -m "WIP: add get method"

Add-Content -Path "cache.py" -Value "`ndef clear(): pass"
git add cache.py
git commit -m "fixup typo in cache"


# 1. Inspect messy 3-commit feature branch history
Write-Host "=== Before History Squash ===" -ForegroundColor Yellow
git log main..HEAD --oneline

# 2. Perform soft reset to merge base and commit cleanly as a single atomic unit
$mergeBase = (git merge-base main HEAD)
git reset --soft $mergeBase
git commit -m "feat(cache): implement complete caching module"

# 3. Verify clean single-commit history
Write-Host "=== After Clean Atomic Squash ===" -ForegroundColor Green
git log main..HEAD --oneline
git show --stat


***********************

Set-Location ..
New-Item -ItemType Directory -Name "Lesson7_Mod5" -Force
Set-Location -Path "Lesson7_Mod5"
git init -b main
git config user.name "Ops Student"
git config user.email "student@lab.com"

# Enable Git's conflict resolution recorder
git config rerere.enabled true

Set-Content -Path "settings.yaml" -Value "timeout: 30`nretries: 3"
git add settings.yaml
git commit -m "Default settings"

# Branch A modifies timeout to 60
git checkout -b hotfix/timeout
Set-Content -Path "settings.yaml" -Value "timeout: 60`nretries: 3"
git add settings.yaml
git commit -m "Increase timeout to 60"

# Main branch modifies timeout to 120
git checkout main
Set-Content -Path "settings.yaml" -Value "timeout: 120`nretries: 3"
git add settings.yaml
git commit -m "Set production timeout to 120"

*****************************

# 1. Trigger merge conflict intentionally
Write-Host "=== Attempting Merge (Expecting Conflict) ===" -ForegroundColor Yellow
git merge hotfix/timeout

# 2. Inspect raw conflict markers injected by Git
Write-Host "=== Raw Conflict Markers in settings.yaml ===" -ForegroundColor Red
Get-Content -Path "settings.yaml"

# 3. Inspect unmerged conflict files programmatically
git diff --name-only --diff-filter=U

# 4. Resolve conflict cleanly, stage, and finalize merge commit
Set-Content -Path "settings.yaml" -Value "timeout: 90`nretries: 3"
git add settings.yaml
git commit -m "Merge hotfix/timeout: standardize timeout at 90s"

# 5. Verify clean merge graph
git log --oneline --graph -n 4


*************************

PowerShell
Set-Location ..
New-Item -ItemType Directory -Name "Lesson7_Mod6_CherryPick" -Force
Set-Location -Path "Lesson7_Mod6_CherryPick"
git init -b main
git config user.name "Ops Student"
git config user.email "student@lab.com"

# Base commit on main
Set-Content -Path "server.ps1" -Value "Write-Host 'Server v1.0'"
git add server.ps1
git commit -m "chore: initial server script"

# Create a feature branch with 3 commits: 1 unfinished feature, 1 critical security hotfix, 1 experimental change
git checkout -b feature/experimental
Set-Content -Path "ui.ps1" -Value "Write-Host 'Unfinished UI'"
git add ui.ps1
git commit -m "wip: unfinished UI layout"

Set-Content -Path "security.ps1" -Value "Write-Host 'Patch CVE-2026-991'"
git add security.ps1
git commit -m "fix(security): patch critical auth bypass"

Set-Content -Path "experimental.ps1" -Value "Write-Host 'Broken beta feature'"
git add experimental.ps1
git commit -m "feat: experimental telemetry"




# Return to main branch
git checkout main

# 1. Inspect commits on feature/experimental to locate the exact security patch SHA-1
Write-Host "=== Commits available on feature/experimental ===" -ForegroundColor Yellow
git log feature/experimental --oneline

# 2. Capture the exact SHA-1 hash of the middle commit ("fix(security): patch critical auth bypass")
$hotfixSha = (git log feature/experimental --grep="patch critical auth bypass" --format="%H")
Write-Host "Target Hotfix SHA-1: $hotfixSha" -ForegroundColor Cyan

# 3. Cherry-pick ONLY that single security commit onto main (using -x to record source SHA)
git cherry-pick -x $hotfixSha

# 4. Verify that ONLY server.ps1 and security.ps1 exist on main (ui.ps1 and experimental.ps1 were left behind)
Write-Host "=== Files on main after cherry-pick ===" -ForegroundColor Green
Get-ChildItem -Name
git log -n 2


********************


Set-Location ..
New-Item -ItemType Directory -Name "Lesson7_Mod6_Bisect" -Force
Set-Location -Path "Lesson7_Mod6_Bisect"
git init -b main
git config user.name "Ops Student"
git config user.email "student@lab.com"

# Create 6 sequential commits where Commit #4 introduces a subtle bug ('FAIL' instead of 'OK')
1..6 | ForEach-Object {
    $status = if ($_ -ge 4) { "FAIL" } else { "OK" }
    Set-Content -Path "health.txt" -Value $status
    git add health.txt
    git commit -m "Release build v1.$_"
}


# 1. Automate binary search bug detection with git bisect run
Write-Host "=== Starting Automated Git Bisect ===" -ForegroundColor Cyan

# Identify first commit (known good) and HEAD (known bad)
$firstCommit = (git rev-list --max-parents=0 HEAD)

git bisect start
git bisect bad HEAD
git bisect good $firstCommit

# Run automated PowerShell check across midpoint commits until culprit is found
git bisect run pwsh -Command 'if ((Get-Content health.txt) -eq "OK") { exit 0 } else { exit 1 }'

# 2. Reset bisect session back to original HEAD
git bisect reset


***********************


New-Item -ItemType Directory -Name "Lesson7_Mod8" -Force
Set-Location -Path "Lesson7_Mod8"
git init -b main
git config user.name "Ops Student"
git config user.email "student@lab.com"

Set-Content -Path "deploy.ps1" -Value "Write-Host 'Production Deploy v1.0'"
git add deploy.ps1
git commit -m "chore: initial production script"

# 1. Start modifying deploy.ps1 on main (simulating unfinished work)
Add-Content -Path "deploy.ps1" -Value "`nWrite-Host 'Half-finished feature line...'"
git status -s

# 2. Save unfinished changes onto the stash stack with a descriptive label
git stash push -m "WIP: half-finished logging update"
git status -s

# 3. Inspect the stash list and inspect the saved diff inside stash@{0}
git stash list
git stash show -p "stash@{0}"

# 4. Restore the stashed changes back into the working directory and drop the stash entry
git stash pop
Get-Content -Path "deploy.ps1"

# 5. Create a parallel working directory for a hotfix branch using git worktree
git worktree add -b hotfix/urgent "/root/Lesson7_Mod8_Hotfix"
git worktree list

# 6. Clean up the secondary worktree directory when finished
git worktree remove "/root/Lesson7_Mod8_Hotfix"

*************************


Task Set 1: Git Internals & Reflog Disaster Recovery (After Module 3)

Focus: Inspecting .git/objects and recovering commits after a destructive reset
Environment: Killercoda Terminal (pwsh)
Scenario
A junior engineer ran git reset --hard on a production configuration repository, wiping out two critical commits. You must inspect the repository's internal Git objects, locate the lost commits via git reflog, and restore the repository state cleanly.





Task Set 2: Conflict Resolution & Automated git bisect (After Module 6)

Focus: Resolving three-way 
merge conflicts and automating bug isolation with git bisect run
Environment: Killercoda Terminal (pwsh)
Scenario
You have a repository with a merge conflict on version.txt and a regression bug hidden across 5 commits in calc.ps1. Resolve the merge conflict cleanly, then run an automated git bisect to identify the exact commit that broke calc.ps1.







