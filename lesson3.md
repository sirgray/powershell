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

!!!!
github account
create private repository (RemoteDemo)

!!!!


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

***********************************************

# Step 1: Create local feature branch
git switch -c feature/about-page

# Step 2: Make edits and commit
Set-Content -Path "about.html" -Value "<h1>About Our Enterprise</h1>"
git add about.html
git commit -m "Add enterprise about page"

# Step 3: Push feature branch to GitHub
git push -u origin feature/about-page



# Delete feature branch on GitHub server
git push origin --delete feature/about-page

# Delete local copy of feature branch
git switch main
git branch -D feature/about-page



#To clean stale references:
git fetch --prune


# Create annotated tag
git tag -a v1.0.0 -m "Production Release Version 1.0.0"

# Explicitly push tag to GitHub
git push origin v1.0.0


*************************************************************


Scenario
You are setting up an online backup for an internal server configuration project named ServerConfig. You must initialize the repository, configure the remote link, and push your initial commit.

Tasks:
1.	Create a folder named ServerConfig and initialize it as a local Git repository.
2.	Create a file settings.conf containing text "PORT=8080". Stage and commit it with message "Add default server port".
3.	Rename your default branch to main.
4.	Add a remote alias named origin pointing to [https://github.com/example-org/server-config.git](https://github.com/example-org/server-config.git).
5.	Run git remote -v to confirm your remote endpoints.
6.	Run git log --oneline to verify local commit history before pushing.



Scenario
You joined a team building an API gateway called ApiGateway. You need to clone the repository, create a feature branch for a rate-limiting feature, commit your scaffold, push the branch upstream, and verify isolation on main.

Tasks:
1.	Clone the repository from [https://github.com/example-org/api-gateway.git](https://github.com/example-org/api-gateway.git) into a local directory named ApiGateway.
2.	Navigate inside ApiGateway and create a feature branch named feature/rate-limiter.
3.	Create a file limiter.py containing text "# Rate limiting logic".
4.	Stage and commit limiter.py with message "Add rate limiter scaffold".
5.	Push the feature branch to origin using git push -u origin feature/rate-limiter.
6.	Switch back to main and run Get-ChildItem to confirm limiter.py is isolated to the feature branch.















