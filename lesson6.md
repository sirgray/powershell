# Create dedicated module directory
New-Item -ItemType Directory -Name "Lesson6_Mod1" -Force
Set-Location -Path "Lesson6_Mod1"

# Declare a script-scoped status variable
$script:AppStatus = "Ready"

function Set-EnvironmentState {
    param (
        [string]$NewStatus
    )

    # Local variable assignment (does not pollute caller scope)
    $localState = "Updating to $NewStatus"
    Write-Host $localState -ForegroundColor Yellow

    # Explicitly update the script-scoped variable
    $script:AppStatus = $NewStatus

    # Return structured custom object
    return [PSCustomObject]@{
        PreviousStatus = "Ready"
        UpdatedStatus  = $script:AppStatus
        Timestamp      = (Get-Date)
    }
}

# Run function and capture return object
$result = Set-EnvironmentState -NewStatus "Maintenance"
$result | Format-List

*******************************************

# Create dedicated module directory and sample test files
Set-Location ..
New-Item -ItemType Directory -Name "Lesson6_Mod2" -Force
Set-Location -Path "Lesson6_Mod2"

Set-Content -Path "file1.txt" -Value "Alpha"
Set-Content -Path "file2.txt" -Value "Beta Payload"
Set-Content -Path "file3.txt" -Value "Gamma Test String"

function Measure-PipelinePayload {
    [CmdletBinding()]
    param (
        [Parameter(ValueFromPipeline = $true)]
        [System.IO.FileInfo]$InputFile
    )

    Begin {
        Write-Host "=== Pipeline Processing Started ===" -ForegroundColor Cyan
        $totalBytes = 0
        $fileCount  = 0
    }

    Process {
        Write-Host "Processing file: $($InputFile.Name) ($($InputFile.Length) Bytes)" -ForegroundColor Gray
        $totalBytes += $InputFile.Length
        $fileCount++
    }

    End {
        Write-Host "=== Processing Complete ===" -ForegroundColor Cyan
        [PSCustomObject]@{
            TotalFilesProcessed = $fileCount
            TotalSizeBytes      = $totalBytes
            AverageSizeBytes    = [math]::Round($totalBytes / $fileCount, 2)
        }
    }
}

# Pipe file objects directly into the function
Get-ChildItem -File *.txt | Measure-PipelinePayload


*******************************************

# Create dedicated module directory and test CSV dataset
Set-Location ..
New-Item -ItemType Directory -Name "Lesson6_Mod3" -Force
Set-Location -Path "Lesson6_Mod3"


@'
ComputerName,ServiceStatus,Port
"Server-01","Running",8080
"Server-02","Stopped",443
"Server-03","Running",22
'@ | Set-Content -Path "servers.csv"


function Test-ServerEndpoint {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true, ValueFromPipelineByPropertyName = $true)]
        [string]$ComputerName,

        [Parameter(Mandatory = $true, ValueFromPipelineByPropertyName = $true)]
        [int]$Port
    )

    Process {
        # Construct object output by matching property names dynamically from pipeline
        [PSCustomObject]@{
            TargetHost = $ComputerName
            TargetPort = $Port
            AuditDate  = (Get-Date -Format "yyyy-MM-dd")
            Status     = if ($Port -eq 443 -or $Port -eq 8080) { "HTTP/S Standard" } else { "Custom Port" }
        }
    }
}

# Read CSV into custom objects and pipe directly into the function
Import-Csv -Path "servers.csv" | Test-ServerEndpoint


***************************************


# Create dedicated module directory and dummy local file

Set-Location ..
New-Item -ItemType Directory -Name "Lesson6_Mod4" -Force
Set-Location -Path "Lesson6_Mod4"
Set-Content -Path "local_config.json" -Value "{ 'env': 'dev' }"


function Get-ResourceConfig {
    [CmdletBinding(DefaultParameterSetName = "LocalSet")]
    param (
        # Parameter Set 1: Local File Path mode
        [Parameter(Mandatory = $true, ParameterSetName = "LocalSet")]
        [ValidateNotNullOrEmpty()]
        [string]$FilePath,

        # Parameter Set 2: Remote URL mode
        [Parameter(Mandatory = $true, ParameterSetName = "RemoteSet")]
        [ValidatePattern('^https://')]
        [string]$RemoteUrl,

        # Shared parameter available in both sets
        [Parameter(Mandatory = $false)]
        [switch]$VerboseOutput
    )

    # Determine active parameter set context dynamically
    switch ($PSCmdlet.ParameterSetName) {
        "LocalSet" {
            Write-Host "Mode: Local File Search -> $FilePath" -ForegroundColor Cyan
            Get-Item -Path $FilePath
        }
        "RemoteSet" {
            Write-Host "Mode: Remote Endpoint Fetch -> $RemoteUrl" -ForegroundColor Yellow
            # Simulated remote return object
            [PSCustomObject]@{ ResourceUrl = $RemoteUrl; Status = "Fetched" }
        }
    }
}

# Test Execution 1: Using LocalSet parameter set
Get-ResourceConfig -FilePath "local_config.json"

# Test Execution 2: Using RemoteSet parameter set
Get-ResourceConfig -RemoteUrl "https://api.internal.lab/config"


********************************

Set-Location ..
New-Item -ItemType Directory -Name "Lesson6_Mod5" -Force
Set-Location -Path "Lesson6_Mod5"

function Write-AuditLog {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [string]$Component,

        [Parameter(Mandatory = $true)]
        [ValidateSet("Information", "Warning", "Error")]
        [string]$Severity,

        [Parameter(Mandatory = $true)]
        [string]$Message
    )

    # Construct structured event record
    $logRecord = [PSCustomObject]@{
        EventId   = [guid]::NewGuid().ToString().Substring(0, 8)
        Timestamp = (Get-Date -Format "yyyy-MM-dd HH:mm:ss")
        Component = $Component
        Severity  = $Severity
        Message   = $Message
        User      = $env:USER
    }

    # Append structured record to CSV log file on disk
    $logRecord | Export-Csv -Path "system_audit.csv" -Append -NoTypeInformation

    # Output JSON string representation to console host
    $logRecord | ConvertTo-Json -Compress
}



# Generate audit log entries
Write-AuditLog -Component "AuthService" -Severity "Information" -Message "User logged in successfully"
Write-AuditLog -Component "Database" -Severity "Warning" -Message "Query response time elevated"

# Display generated CSV log artifact
Import-Csv -Path "system_audit.csv" | Format-Table -AutoSize

***********************************

Set-Location ..
New-Item -ItemType Directory -Name "Lesson6_Mod7" -Force
Set-Location -Path "Lesson6_Mod7"


# 1. Define function to test (saved in AppMath.ps1)
$appMathCode = @'
function Test-IsEvenNumber {
    param (
        [int]$Number
    )

    return ($Number % 2 -eq 0)
}
'@
Set-Content -Path "AppMath.ps1" -Value $appMathCode

# 2. Write automated unit test suite runner (Test-AppMath.ps1)
$testRunnerCode = @'
# Source the target script function
. ./AppMath.ps1

$testCases = @(
    @{ Number = 4;  Expected = $true  },
    @{ Number = 7;  Expected = $false },
    @{ Number = 0;  Expected = $true  }
)

$passCount = 0
$failCount = 0

foreach ($test in $testCases) {
    $result = Test-IsEvenNumber -Number $test.Number
    if ($result -eq $test.Expected) {
        Write-Host "[PASS] Input: $($test.Number) | Output: $result" -ForegroundColor Green
        $passCount++
    } else {
        Write-Host "[FAIL] Input: $($test.Number) | Expected: $($test.Expected), Got: $result" -ForegroundColor Red
        $failCount++
    }
}

Write-Host "=== Test Results: $passCount Passed, $failCount Failed ===" -ForegroundColor Cyan
'@
Set-Content -Path "RunTests.ps1" -Value $testRunnerCode




# Execute the unit test suite
./RunTests.ps1

****************************************

Task Set 1: Practical Checkpoint 
Focus: Advanced pipeline processing, Begin/Process/End blocks, and ValueFromPipelineByPropertyName
Environment: Killercoda Terminal (pwsh)
Scenario
Build a utility function named Get-ConfigReport. The function must accept objects via pipeline property name binding (.ConfigName and .Path), process each item in a Process block, and output a structured report object.

















