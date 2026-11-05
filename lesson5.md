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


****************************

# Create workspace directory and Git repo
Set-Location ..
New-Item -ItemType Directory -Name "/Mod4_ErrorDemo" -Force
Set-Location -Path "Mod4_ErrorDemo"
git init
git config user.name "Ops Student"
git config user.email "student@lab.com"
Set-Content -Path "app.py" -Value "print('v1')"
git add app.py
git commit -m "Initial commit"


# Set error preference to stop execution on errors
$ErrorActionPreference = 'Stop'

function Invoke-GitCommand {
    param (
        [Parameter(Mandatory = $true)]
        [string]$Command
    )

    try {
        Write-Host "Executing: git $Command" -ForegroundColor Yellow
        
        # Invoke native command string dynamically
        Invoke-Expression "git $Command"

        # Check native process exit code
        if ($LASTEXITCODE -ne 0) {
            throw "Native Git command 'git $Command' failed with exit code $LASTEXITCODE."
        }
    }
    catch {
        Write-Host "TRAPPED EXCEPTION: $_" -ForegroundColor Red
    }
    finally {
        Write-Host "Execution attempt complete." -ForegroundColor Gray
    }
}

# Test failure scenario: try checking out a non-existent branch
Invoke-GitCommand -Command "checkout non-existent-branch"


**********************************

# Create working directory and git repo with commit state
Set-Location ..
New-Item -ItemType Directory -Name "/Mod5_DataDemo" -Force
Set-Location -Path "Mod5_DataDemo"
git init
git config user.name "Ops Student"
git config user.email "student@lab.com"
Set-Content -Path "server.py" -Value "# Main server"
git add server.py
git commit -m "Add server script"


$repoStatus = [PSCustomObject]@{
    RepositoryName = (Get-Item .).Name
    CurrentBranch  = (git branch --show-current)
    HasUncommitted = [bool](git status --porcelain)
    LastCommitHash = (git log -1 --format="%h")
    CheckedAt      = (Get-Date)
}

# Display structured object properties as a list
$repoStatus | Format-List


***********************

# Prepare workspace directory
Set-Location ..
New-Item -ItemType Directory -Name "/Mod5_JsonDemo" -Force
Set-Location -Path "Mod5_JsonDemo"


# Step A: Create and export JSON config to disk
@'
[
    { "Environment": "Development", "TargetBranch": "main" },
    { "Environment": "Production",  "TargetBranch": "release" }
]
'@ | Set-Content -Path "environments.json"

# Step B: Read and parse JSON string into .NET Objects
$config = Get-Content -Path "environments.json" | ConvertFrom-Json
$config | Where-Object { $_.Environment -eq "Development" }


***************************

# Ensure profile directory path exists in Killercoda sandbox
$profileDir = Split-Path $PROFILE
if (-not (Test-Path $profileDir)) {
    New-Item -ItemType Directory -Path $profileDir -Force
}


# Create profile script file if it does not exist on disk
if (-not (Test-Path $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force
}

******************************

# Create a test Git repository and move inside it
Set-Location ..
New-Item -ItemType Directory -Name "/Mod8_PromptDemo" -Force
Set-Location -Path "Mod8_PromptDemo"
git init


function prompt {
    $folder = Split-Path (Get-Location) -Leaf
    $gitBranch = ""

    # Test if current directory is inside a Git repository
    if (git rev-parse --is-inside-work-tree 2>$null) {
        $branchName = (git branch --show-current 2>$null)
        $gitBranch = " [$branchName]"
    }

    Write-Host "PS " -NoNewline -ForegroundColor Green
    Write-Host "$folder" -NoNewline -ForegroundColor Cyan
    Write-Host "$gitBranch" -NoNewline -ForegroundColor Yellow
    return "> "
}




Set-Location /root
function prompt { "PS $($ExecutionContext.SessionState.Path.CurrentLocation)> " }


******************************************


Task Set 1: Mid-Day Checkpoint 
Focus: Advanced pipeline manipulation, parameter validation, and object creation
Environment: Killercoda Terminal (pwsh)
Scenario
Write a PowerShell utility script named Get-GitRepoStats.ps1. The script must inspect the working directory, validate parameter inputs, and output a structured table of matching filesystem files.


Task Set 2: Practical Scripting Checkpoint — Complete Breakdown of Initialize-WorkBranch.ps1
Focus: Error handling, native exit codes, and automated branch creation
Environment: Killercoda Terminal (pwsh)
Scenario
Build an enterprise script Initialize-WorkBranch.ps1 that validates parameters, checks for uncommitted local edits, auto-stashes work, and initializes a new feature branch safely.


Task Set 3: Advanced Pipeline & Profile Automation
Focus: Custom JSON configuration parsing, profile prompts, and pipeline filtering
Environment: Killercoda Terminal (pwsh)
Scenario
Parse a JSON environment configuration file, extract target environment values via pipeline filters, and install a custom Git-aware prompt into your PowerShell profile.




