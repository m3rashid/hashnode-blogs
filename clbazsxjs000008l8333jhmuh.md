# How to use git

### GIT - The easiest method (in a summary)

1. Create a repo on GitHub
2. Clone that remote repo on local PC
3. Write/code whatever in that folder (repo you cloned)
4. Push to remote repo

#### Step 1 (Basic Setup)

* Create a GitHub account
* Create new repository
* public/private (whatever you want)
* add readme (if you want)
* Click on Create repository

Now your repo is created


#### Step 2 (Cloning)

* go inside your repo and click on the green button (with a download kind of sign and code written on it)
* that will create a link, copy that link
* open your folder where you want to keep this folder(repo)
* right click --> open git bash
* ```git clone "theLinkYouCopied"```

Like for example -- ```git clone "https://www.github.com/username/repo"``` (Link must be in quotes)
Now cloned, and since you cloned from GitHub, git is already initialised in this repo.


#### Step 3 (The Coding Part)

(that you do yourself, the productivity part) The usual coding, problem-solving stuff


#### Step 4 (Pushing to remote repository)

And now whenever you feel like, code is complete, or maybe a part of it is complete, you confirm the changes,
but before this you need to confirm your identity

```git config user.name "yourGithubUsername"```
```git config user.email "yourEmail```
######(Whatever is in quotes must be in quotes)
You can also configure this globally

Then you add the changes by

```git add fileThatChanged``` (file name with extension without quotes)
 
Example - if I have a file named test.cpp and I want to add this file, then I would write ```git add test.cpp```
Or if you want to add all changed files, just write ```git add .``` (that (.) dot is important, meaning everything in this folder)

Now you need to commit to the changes. (Commit is same as commitment as in English language) meaning to finalise the decision.

```git commit -m "commit message"```
-m for message, and commit message in quotes, This message should be short, compact but convey the changes you did.  It is a general practice to write commit messages in present tense.

Ok, now the changes are finalised, you need to push this to your remote repo, Just write ```git push``` (In case of single branch repository)

Else, you need to write  ```git push origin branchName``` (branchName specifies the branch you want to commit to)

This will ask to confirm your identity, it will open your GitHub with an oAuth request, just give your GitHub credentials there. Once authenticated, changes will be pushed to your remote origin (GitHub)


##### List of some commonly used git commands
| Command | Description |
|----- |-----|
|```git init``` | to initialise git into a repository |
|```git status``` | to check the status of your repository|
|```git log``` | to see all commits |
| ```git diff``` | to show the changes in files line by line |
| ```git checkout commitID``` | to roll back (revert back) to a previous commit (commit ID is a unique ID given to each commit you make, can be seen in git log) |
| ```git checkout -b branchName``` | to go the existing branch, if it exists. Else, it creates a new branch with that given name |
| ```git branch``` | shows all the branches in your repository |
| ```git merge branchName``` | to merge the specified branch with the current branch |