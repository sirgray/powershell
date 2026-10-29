Instructions & Tasks

System Discovery & Directory Setup (PowerShell)

Display your logged-in username using the $env:USER variable.

Create a new folder named SystemLogger and change your active location into it.

Git Initialization & Identity Setup (Git)

Initialize SystemLogger as a Git repository.

Configure your Git identity for this session (Name: "Ops Student", Email: "ops@lab.com").

File Creation & Staging Workflow (PowerShell + Git)

Create a file named logger.py containing the text: print("System Log Active").

Create a second file named app.log containing the text: [INFO] Server Started.

Create a .gitignore file so Git completely ignores all .log files.

Stage logger.py and .gitignore, then commit with the message "Initial logger setup". Run git status to verify app.log is ignored.

Branch Isolation & Reversion Test (Git)

Create and switch to a new branch named feature/debug-mode.

Append print("Debug Mode ON") to logger.py.

Test git diff to view your modification, then use git restore logger.py to discard the changes and revert back to a clean state.
