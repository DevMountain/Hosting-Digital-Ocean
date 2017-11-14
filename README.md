# Digital Ocean

### Overview

Basic steps:
1. Get secure connection to droplet with SSH key.
1. Sign up for a droplet on Digital Ocean.
1. Push working code to GitHub. Make sure express.static points to build folder.
1. Clone project from GitHub to server and ```npm install```.
1. Create .env/config.js files on server.
1. Create a build with ```npm run build```.
1. Run forever (so hosted project is always running).

Optional:
1. Set up swapfile to extend the limited RAM that comes with the cheaper droplets.
1. Set up nginx to host multiple projects on same droplet.
1. Set up a domain name.



***


### SSH Key

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


### Digital Ocean account
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


### Connect to Server
- Use ```ssh root@youripaddress``` (e.g., ```ssh root@127.48.12.123```) to connect to your droplet through the command line.
- You will need your password to connect. **YOU DIDN'T FORGET IT, DID YOU?**


***


### Swapfile (optional)
Many students buy a Digital Ocean droplet on the $5 tier, which comes with limited RAM. A swapfile can effectively extend the amount of given RAM by swapping out less-recently-used files to the hard disk. This can come in handy. For example, sometimes when running a build on a low-RAM droplet, the process will time out because there is not enough RAM. Having a swapfile in place can help with this.

<details> <summary> swapfile details </summary> 

#### Create swapfile and turn on
```cli
touch /swapfile
fallocate -l 1G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

#### Extra swap options:
- If you need to turn off the swapfile: ```swapoff /swapfile```.
- If you need to turn the swapfile back on: ```swapon /swapfile```.
- If you want to have the droplet load with the swapfile on, use ```nano /etc/fstab``` to open the fstab file, and at the bottom type ```/swapfile   none    swap    sw    0   0```.
- If you want to tell the server to swap files less often, use ```nano /etc/sysctl.conf``` to open the sysctl.conf file and type ```vm.swappiness=10``` to set the swappiness to 10 (instead of the default 60).

#### Possible errors:
- If the you get an error related to the ```fallocate``` command, it may be that the swapfile is currently on. You cannot fallocate a swapfile that is currently in use. Try turning off the swapfile with ```swapoff /swapfile``` and then entering the commands again, starting with ```fallocate -l 1G /swapfile``` and ending with ```swapon /swapfile``` (which turns it back on). 

</details>


***


### Upgrade Node

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


### Clone project from GitHub
- On local machine, switch any localhost paths to environment variables.
- Make sure the code is working on your local machine.
- In your server, add express.static


- Clone project onto server using ```git clone url-to-your-github-project```.
- Run ```npm install``` to install node packages.
- Recreate any .env file or config.js onto the server. Paste in the contents from the corresponding files on your local machine. Change any absolute paths containing "localhost" (e.g., 'http://localhost:3100/api/users') to relative paths instead (e.g., '/api/users').
- Create a build using ```npm run build```. This will create a folder called "build" with an optimized version of your project. The express.static lines you added to the server file will tell the server to look in that folder for the static files that need to be served.


#### Possible errors:
- It is possible to get a timeout error when running a build on your server. This may happen if your droplet is the cheapest tier (with the least RAM). You might fix this by implementing a swapfile (see the optional section on swapfiles). If you already created a swapfile, trying running through all those swapfile commands again (perhaps there was an error when creating it the first time).


***


### Run Node
- Test to see if your hosted project works. Try running node on your server file. If, for example, your server file is called "index.js" and it is inside a folder called "server", run ```node server/index.js```.
- Now enter your IP address (the one Digital Ocean gave you) in the browser URL bar, followed by a ```:``` and the port your server is running on (e.g., ```127.48.12.123:3100```). Your hosted site should appear. You are almost done! Currently, your site is running but will stop as soon as you close the command line window or otherwise terminate the running Node process.
- To keep your app running forever, move on to the final required step, in which you will install something called forever.

#### Possible bugs:
- You might run Node and find that your app's front end does not appear. Perhaps you see the words ```Cannot GET``` in the browser. Try testing one of your GET endpoints to see if the back end works by typing the endpoint URL into the browser bar (e.g., '127.48.12.123:3100/api/products'). If the browser correctly displays data from your endpoint, this probably indicates that your project is hosted on the server but your server file is not pointing to your build folder correctly. 
    - Carefully check the express.static line again. It is easy to miss a slash (```/```) or a period (```.```). The correct code should probably look like this: ```app.use( express.static( `${__dirname}/../build` ) );```. Notice the ```__dirname``` variable (with two underscores), followed by a slash, then traveling up one folder and going into the ```build``` folder. 
    - You might also double-check that you ran ```npm run build``` to create a build folder.


***


### forever