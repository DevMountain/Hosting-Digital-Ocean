# Cheat Sheet for Editing Hosted Project

## Changes to the regular code?
1. Make sure code works on your local (unhosted) project.
2. Add, commit, and push working changes to GitHub.
3. In your command line editor, use the ```ssh``` command to connect to your server droplet and use ```cd``` to go to your project folder on the server.
4. Use ```git status``` to show the status of your project on the server. It should be one (or more) commits behind the origin/master branch on GitHub since you just recently pushed new changes to GitHub which you have not yet pulled down onto the server. If it says you are up to date, you may need to run ```git fetch```. ```git fetch``` will not change any of your code; it merely refreshes Git's look at which files are up to date.

## Changes to .gitignored files like .env or config.js?

## Changes to node modules (new dependencies installed)?