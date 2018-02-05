# Cheat Sheet for Editing Hosted Project

## Changes to the main code?
1. Make sure code works on your local (unhosted) project.
2. Add, commit, and push working changes to GitHub.
3. In your command line editor, use the ```ssh``` command to connect to your server droplet and use ```cd``` to go to your project folder on the server.
4. Use ```git status``` to show the status of your project on the server. It should be one (or more) commits behind the origin/master branch on GitHub since you just recently pushed new changes to GitHub which you have not yet pulled down onto the server. If it says you are up to date, you may need to run ```git fetch```. ```git fetch``` will not change any of your code; it merely refreshes Git's look at which files are up to date.
5. Use ```git pull``` to pull down the latest code changes onto your server. Your server now has working code.
6. Use ```npm run build``` for React projects which use ```express.static``` to serve up files from a build folder. This recreates the build folder with the recent changes so they become part of the files that are served up.
7. Restart PM2. ```pm2 restart all```

## Changes to node modules (i.e., new dependencies installed)?
1. Installing new node modules in the local project should have changed your local package.json file. This file is not .gitignored, so you can update your hosted project's package.json file by following steps 1–5 above which describe how to push and pull working code to the server.
2. Once package.json file is up to date on the server, run ```npm install``` to install the new dependencies into your project.
3. Use ```npm run build``` to make a new build folder so these changes become part of the code served up.
4. Restart PM2.

## Changes to .gitignored files like .env or config.js?
1. Because these files are ignored by Git and will not be updated with ```git pull```, they need to be updated manually.
2. If these files are at the top of the project folder, use ```cd``` to go to the top of the folder and use ```nano``` to open the files (e.g., ```nano .env```).
3. Type or paste in the edits to these files. Some of these edits can be pasted as is from your local .env or config files. Keep in mind, however, that when it comes to URLs, your local project might make use of absolute paths (e.g., '<span>http</span>://localhost:3001/api/products') whereas your hosted project's .env and config files should generally use relative paths instead (e.g., '/api/products').
4. Run ```npm run build``` to build these changes into your project.
5. Restart PM2.
