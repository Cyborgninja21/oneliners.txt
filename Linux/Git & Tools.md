GIT
===
(*) Initialize a git repo in the current directory: `git init`
(*) Make some changes to a file and add it to the next commit: `git add FILE`
(*) Commit your changes with a message: `git commit -m "changes happened"`
(*) Get status but don't show untracked files: `git status -uno`
(*) Oops, your last commit message was wrong. Amend it: `git commit --amend -m "changes happened to FILE"`
(*) Dang it, you also forgot to add a file with the last commit: `git add FILE && git commit --amend --no-edit`

(*) List the branches available to git: `git branch`
(*) You're ready for something new. Make a new branch: `git checkout NAMEOFBRANCH`
(*) Make a new branch and carry over staged files: `git checkout -b name-of-new-branch`
(*) Stage files from another branch: `git checkout NAMEOFTHEBRANCH FILE1 FILE2`
(*) Revert a file to a version seen in COMMITREF: `git checkout COMMITREF -- file1/to/restore`

(*) You want to save your changes, but not commit them (yet). Stash: `git stash save MyStashedChanges`
(*) What stashed changes do I have?: `git stash list`
(*) Apply some stashed changes (use `stash list` to find your index integer): `git stash apply INDEX`

(*) Compare two branches with a simple summary: `git diff --compact-summary <branch1> <branch2>`
(*) Delete all local git branches that have been merged and deleted from remote: `git branch -d $( git branch -vv | grep '\[[^:]\+: gone\]' | awk '{print $1}' | xargs )`
(*) (Powershell) Delete all local branches that have been merged into master: `git branch --merged origin/master | Where-Object {  !$_.Contains('master') } | ForEach-Object { git branch -d $_.trim() }`
(*) Cleanup remote git repository of all branches already merged into master: `git branch --remotes --merged | grep -v master | sed 's@ origin/@:@' | xargs git push origin`
(*) (Powershell) Delete all local branches that are not master: `git branch | Where-Object { !$_.Contains('master') } | ForEach-Object { git branch -D $_.Trim() }`
(*) (Powershell) Copy current branch to clipboard: `(git branch | Where-Object { $_.Contains('*') } | Select-Object -First 1).Trim('*').Trim() | Set-Clipboard`
(*) (Powershell) Initialise git in working directory with latest Visual Studio .gitignore: `git init; (Invoke-WebRequest https://raw.githubusercontent.com/github/gitignore/master/VisualStudio.gitignore -UseBasicParsing).Content | Out-File -FilePath .gitignore -Encoding utf8; git add -A`
(*) Get full git commit history of single file: `git log -p --name-only --follow <file>`
(*) Open browser from terminal to create PR after pushing something in Git: `git remote -v | grep origin| tail -1| awk '{print $2}'| cut -d"@" -f2| sed 's/:/\//g'| xargs -I {} $BROWSER https://{}`
(*) Print GitHub url for the current url: `git remote -v | sed -n '/github.com.*push/{s/^[^[:space:]]\+[[:space:]]\+//;s|git@github.com:|https://github.com/|;s/\.git.*//;p}'`
(*) Push to all (different) remotes in git directory without having to combine them.: `git remote | while read line ; do git push $line; done`
(*) (Powershell) Stage all files for commit except those that are *.config at any level within your git repo: `git status | Where-Object {$_.Contains('modified') -and !$_.Contains('.config')} | ForEach-Object { git add $_.Replace('modified:','').Trim() }`
(*) Show contents of all git objects in a git repo: `find .git/objects/ -type f \| sed 's/\.git\/objects\/\///' | sed 's/\///g' | xargs -n1 -I% echo echo "%" \$\(git cat-file -p "%"\) \0 | xargs -n1 -0 sh -c`
(*) Print all git repos from a user: `USERNAME=username && curl -s https://api.github.com/users/${USERNAME}/repos?per_page=1000 | grep git_url | awk '{print $2}'| sed 's/"\(.*\)",/\1/'`
(*) Print all git repos from a user (only curl and grep): `USERNAME=username && curl -s https://api.github.com/users/${USERNAME}/repos?per_page=1000 | grep -oP '(?<="git_url": ").*(?="\,)'`
(*) Print all git repos from a user: `USERNAME=username && curl -s "https://api.github.com/users/${USERNAME}/repos?per_page=1000" | jq '.[].git_url'`
(*) Print all git repos from a user: `USERNAME=username && curl -s "https://api.github.com/users/${USERNAME}/repos?per_page=1000" | python <(echo "import json,sys;v=json.load(sys.stdin);for i in v:; print(i['git_url']);" | tr ';' '\n')`
(*) (Powershell) Pull multiple repositories in child folders (a.k.a. I'm back from leave script): `gci -Directory | foreach {Push-Location $_.Name; git fetch --all; git checkout master; git pull; Pop-Location}`
(*) See where settings are coming from: `git config --show-origin --list`
(*) Nice git aliases to visualize git log: `git config --global alias.lg2 "log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''%C(white)%s%C(reset) %C(dim white)- %an%C(reset)' --all"`

(*) Create a multi-patch from the current checked-branch: `git format-patch --singoff master --stdout > multi.diff`
TODO (*) Apply a patch
