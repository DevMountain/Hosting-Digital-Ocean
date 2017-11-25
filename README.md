# Digital Ocean

## Overview

###### Basic hosting steps:
1. Get secure connection to droplet with SSH key.
1. Sign up for a droplet on Digital Ocean.
1. Push working code to GitHub. Make sure express.static points to build folder.
1. Clone project from GitHub to server and ```npm install```.
1. Create .env/config.js files on server.
1. Create a build with ```npm run build```.
1. Run forever (so hosted project is always running).

###### Optional steps:
1. Set up swapfile to extend the limited RAM that comes with the cheaper droplets.
1. Set up nginx to host multiple projects on same droplet.
1. Set up a domain name.

###### Have a hosted project you need to edit?
- [Try this cheat sheet.](https://github.com/Alan-Miller/digital-ocean/blob/master/cheat-sheet-for-editing-hosted-project.md)



***


## SSH Key

An SSH key gives us a secure connection to our server droplet. 
- To begin the creation process, type ```ssh-keygen -t rsa```.
- You will be asked for a location and filename. Just use the defaults.
```sh
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/username/.ssh/id_rsa):
```
- You will be asked to enter a password. **YOU MUST NOT FORGET THIS PASSWORD**. It cannot be recovered. Make sure it is something you can remember.
```sh
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```
- Two files will then be created in the .ssh in the home folder of your computer (i.e., in ~/.ssh). These files are ```id_rsa``` and ```id_rsa.pub```, your private and public keys, respectively. You will notice a randomart image of your key like the one below. 
```sh
Your identification has been saved in demo_rsa.
Your public key has been saved in demo_rsa.pub.
The key fingerprint is:
cc:28:30:44:01:41:98:cf:ae:b6:65:2a:f2:32:57:b5 user@user.local
The key's randomart image is:
+--[ RSA 2048]----+
|       o=.       |
|    o  o++E      |
|   + . Ooo.      |
|    + O B..      |
|     = *S.       |
|      o          |
|                 |
|                 |
|                 |
+-----------------+
```
- You will copy the public key (```id_rsa.pub```) to give to Digital Ocean. You will not give out the private key. To see the public key, type ```cat ~/.ssh/id_rsa.pub```. This will show you a long string starting with ```ssh-rsa``` and ending with an email address.
- You can also copy the key using ```pbcopy < ~/.ssh/id_rsa.pub```.


***


## Digital Ocean account
- Sign up for a droplet on Digital Ocean.
- In the Security Settings, paste in the public SSH key. Give it a name to remember which key it is, probably something like "Michelle-Macbook" to remember which computer it is connected to.
- Most students should choose Ubuntu for the operation system.
- Select a droplet size. Most students choose the cheapest droplet (about $5 per month).
- When asked, choose a data center close to you (e.g, San Francisco for the West Coast).
- Select the SSH key you registered earlier.
- Choose a droplet name that can represent multiple projects, not just one (e.g., "kevin-droplet" rather than "personal-project").
- Once the droplet is created, you will receive the droplet's IP address. You will use this to connect to your server through the command line, and you will also use it to log on to the site once it is hosted (unless you set up a domain).

#### Possible errors:
- When pasting in your SSH key, you might get an error saying your private key is invalid, even when you are sure you copied the correct key. This can happen if you accidentally copied spaces at the end of the key string. This especially happens with Windows users using git bash, even when it looks like there are no extra spaces in the selection. Try pasting the key into Notepad or another editor (like VS Code) and then copying again from there.


***


## Connect to Server
- Use ```ssh root@your.ip.address``` (e.g., ```ssh root@127.48.12.123```) to connect to your droplet through the command line.
- You will need your password to connect. **YOU DIDN'T FORGET IT, DID YOU?**


***


## Swapfile (optional)
Many students buy a Digital Ocean droplet on the $5 tier, which comes with limited RAM. A swapfile can effectively extend the amount of given RAM by swapping out less-recently-used files to the hard disk. This can come in handy. For example, sometimes when running a build on a low-RAM droplet, the process will time out because there is not enough RAM. Having a swapfile in place can help with this.

<details> <summary> swapfile details </summary> 

###### Create swapfile and turn on
```cli
touch /swapfile
fallocate -l 1G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

###### Extra swap options:
- If you need to turn off the swapfile: ```swapoff /swapfile```.
- If you need to turn the swapfile back on: ```swapon /swapfile```.
- If you want to have the droplet load with the swapfile on, use ```nano /etc/fstab``` to open the fstab file, and at the bottom type ```/swapfile   none    swap    sw    0   0```.
- If you want to tell the server to swap files less often, use ```nano /etc/sysctl.conf``` to open the sysctl.conf file and type ```vm.swappiness=10``` to set the swappiness to 10 (instead of the default 60).

#### Possible errors:
- If the you get an error related to the ```fallocate``` command, it may be that the swapfile is currently on. You cannot fallocate a swapfile that is currently in use. Try turning off the swapfile with ```swapoff /swapfile``` and then entering the commands again, starting with ```fallocate -l 1G /swapfile``` and ending with ```swapon /swapfile``` (which turns it back on). 

</details>


***


## Upgrade Node

The first time you access your droplet, there is likely an older version of Node installed on the computer. If so, you should update. You might want to run the same version of Node as the one installed on your computer.
- To see what version you currently have, type ```node -v```.
- Run ```apt-get update && apt-get dist-upgrade``` to update the software Linus know about. 
- Run ```apt-get install nodejs -y ; apt-get install npm -y``` to install Node.js and npm.
- Run ```npm i -g n``` to globally install ```n```, which you can use to upgrade Node (or downgrade to an earlier version).
- If you want to install the latest version of Node, type ```n latest```.
- If you want to install an earlier version of Node, type ```n x.x.x``` (e.g., ```n 8.6.0```).
- Run ```npm i -g npm``` to update and install the latest stable version of npm.

#### Possible errors:
- It is possible to get an error that says Node is not compatible with npm. This might happen if you have the latest version of Node installed on your server and it is not a stable version or is not yet supported. Try downgrading to a slightly earlier version of node using ```n x.x.x``` (replacing the x's with the version numbers).


***


## Prep code for production
###### .env variables
- On local machine, instead of using absolute paths (e.g., 'http://localhost:3200/auth') to environment variables. In other words, everywhere you have a full path with "localhost" in it, replace that path string with a reference to a variable, and store that variable and value in your .env (or config.js) file.
    - For example, if you have an ```<a>``` tag with an Auth0 link like this...

        ``` <a href={"http://localhost:3200/auth"}><li>Log in</li></a> ``` 
    
        ... replace the string so it says something like this:

        ```<a href={process.env.REACT_APP_LOGIN}><li>Log in</li></a>``` 
    
    - In the .env file, store the variable. No need for a keyword like ```var``` or ```const```. Also, quotation marks are optional (unless there is a space inside the string, in which case they are required). The variable for the example above would look like this inside the .env file:

        ```REACT_APP_LOGIN=http://localhost:3200/auth```

- Replacing full paths with environment variables is generally a good idea throughout your whole app (both front end and back). For React, however, keep two things in mind:
    1. If you built your front end with ```create-react-app```, your React front end can only access variables that start with ```REACT_APP_```. The ```npm start``` command builds them into the app. Variables that are accessed outside of React (i.e., in your back end), do not need the ```REACT_APP_``` prefix.
    2. React does not allow you to access files outside the src folder, so if you need environment variables in your front end, you will have to put an .env file inside the src folder.

###### build folder
- Make sure the project is working on your local machine.
- Run ```npm run build``` to create a build folder.
- In your server, the following code to point your server to your front end static files. This tells express to look for a build folder. The ```__dirname``` variable tells it to start at the current file where Node is running (i.e., your server file), and ```/../build``` tells it to then go up one file and into a build folder.
```app.use( express.static( `${__dirname}/../build` ) );```


## Copy project to server
###### push and pull from GitHub
- Commit and push your working code to GitHub.
- Use ```ssh root@your.ip.address``` to connect to your droplet, and use ```cd``` to go into your project folder on the server.
- Clone the project to the server using ```git clone url-to-your-github-project```. Once done, your code will be on the server, except for node_modules, .env variables, and a build folder (since these are all .gitignored and therefore not copied to and from GitHub).
###### node_modules
- Run ```npm install``` inside the project folder on the server to install node packages.
###### .env file
- Recreate any .env file or config.js in the the server. 
    - Use ```touch .env``` to make an .env file.
    - Use ```nano .env``` to edit the file.
- Go to your code editor and copy the contents of your local .env file. Inside nano on the server, paste in the contents you copied so they will now be in the server .env file. Change any full paths containing "localhost" (e.g., 'http://localhost:3100/api/users') to relative paths instead (e.g., '/api/users'). We do this because your server might be structured differently than your local machine, so we give the server a relative path, and it knows what to do from there.
- To exit nano, use Ctrl+x to exit. It will ask if you want to save. Type ```Yes``` or ```y```, then hit Return to confirm. 
###### build folder
- Create a build folder using ```npm run build```. This will create a folder called "build" with an optimized version of your project. The express.static line you added to the server file will tell the server to look in that build folder for the static files that need to be served.
- Now your entire project is saved to the server, including code, node_modules, .env files, and build folder. The next step is to run Node on our project to see if it works from the server.


#### Possible errors:
- It is possible to get a timeout error when running a build on your server. This may happen if your droplet is the cheapest tier (with the least RAM). You might fix this by implementing a swapfile (see the optional section on swapfiles). If you already created a swapfile, trying running through all those swapfile commands again (perhaps there was an error when creating it the first time).
- If you see an error saying ```npm build``` was called without any arguments, try ```npm run build``` instead. Your ```package.json``` file shows both ```start``` and ```build``` together in the ```scripts``` section, and you are used to running ```npm start``` (with no "run" command), so you may think you can run ```npm build``` the same way. It is true that leaving out ```run``` is a shorthand way of running scripts, but there is already a built-in npm command for ```npm build``` (used to build Node add-ons FYI), and that built-in command overshadows the ```npm build``` shorthand. **TL;DR**: Try ```npm run build``` instead.


***


## Run Node
- Test to see if your hosted project works. Try running node on your server file. If, for example, your server file is called "index.js" and it is inside a folder called "server", run ```node server/index.js```.
- Now enter your IP address (the one Digital Ocean gave you) in the browser URL bar, followed by a ```:``` and the port your server is running on (e.g., ```127.48.12.123:3100```). Your hosted site should appear. You are almost done! Currently, your site is running but will stop as soon as you close the command line window or otherwise terminate the running Node process.
- To keep your app running forever, move on to the final required step, in which you will install something called forever.

#### Possible bugs:
- You might run Node and find that your app's front end does not appear. Perhaps you see the words ```Cannot GET``` in the browser. Try testing one of your GET endpoints to see if the back end works by typing the endpoint URL into the browser bar (e.g., '127.48.12.123:3100/api/products'). If the browser correctly displays data from your endpoint, this probably indicates that your project is hosted on the server but your server file is not pointing to your build folder correctly. 
    - Carefully check the express.static line again. It is easy to miss a slash (```/```) or a period (```.```). The correct code should probably look like this: ```app.use( express.static( `${__dirname}/../build` ) );```. Notice the ```__dirname``` variable (with two underscores), followed by a slash, then traveling up one folder and going into the ```build``` folder. 
    - You might also double-check that you ran ```npm run build``` to create a build folder.


***


## forever