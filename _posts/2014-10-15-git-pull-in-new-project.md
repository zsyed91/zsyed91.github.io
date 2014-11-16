---
layout: post
title:  Git Tips - New GitHub repo and existing repo
date:   2014-10-15
summary: How to pull in a new GitHub repository to an existing repository without cloning
---


There comes a time where you are working on an existing project and want to create a GitHub repository. 
The problem is how do you "clone" the new GitHub repo without messing up your local workspaces?


When you clone a new repository onto your local workspace, all git is really doing is creating a remote branch under origin/master and aliases the remote as origin.
We can take advantage of this fact and do this to "clone" a new GitHub repository into our existing repository.


#### Add a remote repository
Run the following command:

	git remote add origin git@github.com:username/repository-name.git

Replace `username` with your GitHub username (or the username of the project). Replace `repository-name` with the name of the project.

#### Pull in the GitHub repository files

Now we have a remote branch under `origin/master`. We can pull it into our existing local `master` branch by doing the following:


	git checkout master        <-- Switch to master branch
	git fetch origin           <-- Pull down new GitHub repository files (usually just license and readme)
	git merge origin/master    <-- Merge these files into our local branch


#### Push our existing project onto GitHub

Now that we have all it all merged in we can push our existing project onto GitHub:

	git push origin master     <-- master is optional as git defaults to pushing master

Now all the files in our git repository will be available on GitHub. That's all there is to it!