# Create a dedicated working directory and dummy files
New-Item -ItemType Directory -Name "/Mod1_Demo" -Force
Set-Location -Path "Mod1_Demo"
Set-Content -Path "app.log" -Value "Application initialized successfully."
Set-Content -Path "server.conf" -Value "PORT=8080"


# Store array of directory items
$files = Get-ChildItem -Path "."

# Inspect exact .NET Class Name of the array container
$files.GetType().FullName

# Inspect the type of an individual file element inside the array
$files[0].GetType().FullName

# Inspect all available public properties and methods on an object instance
Get-ChildItem | Get-Member


*******************

# Create a Git repository environment
Set-Location ..
New-Item -ItemType Directory -Name "/Mod1_GitDemo" -Force
Set-Location -Path "Mod1_GitDemo"
git init
git config user.name "Ops Student"
git config user.email "student@lab.com"

# Execute a native git command
git status

# Inspect the automatic process exit status variable
if ($LASTEXITCODE -ne 0) {
    Write-Error "Git command failed with exit code $LASTEXITCODE"
}

***********************



# Setup directory with new files and old files, plus a Git repo with log history
Set-Location ..
New-Item -ItemType Directory -Name "/Mod2_Demo" -Force
Set-Location -Path "Mod2_Demo"

# Create dummy files
Set-Content -Path "recent_script.ps1" -Value "Write-Host 'Hello'"
Set-Content -Path "recent_config.json" -Value "{}"

# Manually backdate an old file to make date filtering return visible contrasts
$oldFile = New-Item -ItemType File -Name "old_archive.txt" -Value "Legacy Data" -Force
$oldFile.LastWriteTime = (Get-Date).AddDays(-10)

# Setup Git history with commit messages to filter
git init
git config user.name "Ops Student"
git config user.email "student@lab.com"
git add .
git commit -m "Initial commit setup"
Set-Content -Path "patch.py" -Value "# fix for issue 101"
git add patch.py
git commit -m "fix: resolve authentication timeout bug"
Set-Content -Path "feature.py" -Value "# new feature"
git add feature.py
git commit -m "feat: add user dashboard view"


# Example 1: Filter files modified within the last 24 hours
Get-ChildItem -Recurse | Where-Object { $_.LastWriteTime -gt (Get-Date).AddDays(-1) }

# Example 2: Filter string lines returned from native git log matching regular expressions
(git log --oneline) | Where-Object { $_ -match "fix|bug" }


*****************************


# Create sample files with varying file sizes
Set-Location ..
New-Item -ItemType Directory -Name "/Mod2_Transform" -Force
Set-Location -Path "Mod2_Transform"

# Create a small file and a larger 2MB dummy payload file
Set-Content -Path "small_notes.txt" -Value "Small text note."
$largeBuffer = New-Object byte[] (2 * 1024 * 1024)
[System.IO.File]::WriteAllBytes("/root/Mod2_Transform/data_payload.bin", $largeBuffer)

# Create calculated property converting Bytes to Megabytes
Get-ChildItem | Select-Object Name, @{ 
    Name       = "SizeMB"
    Expression = { [math]::Round($_.Length / 1MB, 2) } 
}


***********************


# Create workspace directory and initialize a Git repository
Set-Location ..
New-Item -ItemType Directory -Name "/Mod3_Scripting" -Force
Set-Location -Path "Mod3_Scripting"
git init
git config user.name "Ops Student"
git config user.email "student@lab.com"
Set-Content -Path "README.md" -Value "# Infrastructure Repository"
git add README.md
git commit -m "Initial root commit"


$scriptCode = @'
[CmdletBinding()]
param (
    [Parameter(Mandatory = $true, HelpMessage = "Branch name must start with 'feature/'")]
    [ValidatePattern('^feature/')]
    [string]$BranchName,

    [Parameter(Mandatory = $true, HelpMessage = "Provide a valid Jira ticket number (1000-9999)")]
    [ValidateRange(1000, 9999)]
    [int]$JiraTicket
)

# Display Execution Header
Write-Host "=== Initializing Feature Branch ===" -ForegroundColor Cyan
Write-Host "Target Branch Name : $BranchName" -ForegroundColor Gray
Write-Host "Linked Jira Ticket : OPS-$JiraTicket" -ForegroundColor Gray

# Execute Native Git Command
git checkout -b "$BranchName"

# Verify Native Process Execution Code
if ($LASTEXITCODE -eq 0) {
    Write-Host "Successfully created and checked out $BranchName!" -ForegroundColor Green
} else {
    Write-Error "Failed to create branch $BranchName. Native Git exit code: $LASTEXITCODE"
}
'@

Set-Content -Path "New-FeatureBranch.ps1" -Value $scriptCode


./New-FeatureBranch.ps1 -BranchName "feature/user-auth" -JiraTicket 1024



