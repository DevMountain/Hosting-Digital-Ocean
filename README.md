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

###### Steps for making changes to a project that is already hosted:
- [Cheat sheet.](https://github.com/Alan-Miller/digital-ocean/blob/master/cheat-sheet-for-editing-hosted-project.md)



***


## SSH Key

An SSH key gives us a secure connection to our server droplet. 
- To begin the creation process, type ```ssh-keygen -t rsa```. You are creating an SSH key for your whole computer, so it does not matter what folder you are in when you type this.
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

###### Possible issues:
- When pasting in your SSH key, you might get an error saying your private key is invalid, even when you are sure you copied the correct key. This can happen if you accidentally copied spaces at the end of the key string. This especially happens with Windows users using git bash, even when it looks like there are no extra spaces in the selection. Try pasting the key into Notepad or another editor (like VS Code) and then copying again from there.


***


## connect to server
- Use ```ssh root@your.ip.address``` (e.g., ```ssh root@127.48.12.123```) to connect to your droplet through the command line.
- You will need your password to connect. **YOU DIDN'T FORGET IT, DID YOU?**

<details> <summary> Additional SSH login options </summary>
&nbsp;

###### Change SSH password
If you need to change your password, you can do so after first logging into your droplet. Obviously, this means you need to know your old password. If you do change it, don't forget you cannot recover passwords, so you will have to take care to remember the new one.
- Log in to your droplet using ```ssh root@[your.IP.address]```.
- Type ```passwd```. You will be prompted to enter your old password and then the new password (twice). 
&nbsp;

###### Add SSH password to ssh-agent keychain
To log in without typing your password, you can add the password to the ssh-agent, a program that holds private keys for authentication. [See these docs for more.](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)
- Start the ssh-agent by running ```ssh-agent -s```.
- Modify the hidden SSH config file on your computer to automatically load keys into ssh-agent's keychain.
    - Open the config file using ```nano ~/.ssh/config```.
    - Add the following to the config file:
```sh
    Host *
        AddKeysToAgent yes
        UseKeyChain yes
        IdentityFile ~/.ssh/id_rsa
```
- Add your SSH private key to the ssh-agent by running ```ssh-add -K ~/.ssh/id_rsa```.
&nbsp;

###### Custom SSH login
If you find it inconvenient to type in your IP address when logging into your server, try customizing your SSH login.
- On your local computer open the hidden SSH config file in your home folder. If you want to use nano, you can enter ```nano ~/.ssh/config```. 
- Inside this file, enter a Host, User, and Hostname in the format below. The Host will be the name you want to use for logging in, and the Hostname will be the IP address for the server. The User will be either "root" or the user if you created a user on the server. Optionally, you can specify a port or leave out this line (which sets the port to its default, 22). Below is a sample config file. A person could log in to the 103.723.6.24 droplet from this computer using either ```ssh bakerc``` to log in as root or ```ssh bakerm``` to log in as user mb.

``` sh

    Host bakerc
        User root
        Hostname 103.723.6.24

    Host bakerm
        User mb
        Hostname 103.723.6.24

```

</details>


***


## upgrade Node

The first time you access your droplet, there is likely an older version of Node installed on the computer. If so, you should update. You might want to run the same version of Node as the one installed on your computer.
- To see what version you currently have, type ```node -v```.
- Run ```apt-get update && apt-get dist-upgrade``` to update the software Linus know about. 
- Run ```apt-get install nodejs -y ; apt-get install npm -y``` to install Node.js and npm.
- Run ```npm i -g n``` to globally install ```n```, which you can use to upgrade Node (or downgrade to an earlier version).
- If you want to install the latest version of Node, type ```n latest```.
- If you want to install an earlier version of Node, type ```n x.x.x``` (e.g., ```n 8.6.0```).
- Run ```npm i -g npm``` to update and install the latest stable version of npm.

###### Possible issues:
- It is possible to get an error that says Node is not compatible with npm. This might happen if you have the latest version of Node installed on your server and it is not a stable version or is not yet supported. Try downgrading to a slightly earlier version of node using ```n x.x.x``` as mentioned above.


***


## swapfile (optional)
Many students buy a Digital Ocean droplet on the $5 tier, which comes with limited RAM. A swapfile can effectively extend the amount of given RAM by swapping out less-recently-used files to the hard disk. This can come in handy. For example, sometimes when running a build on a low-RAM droplet, the process will time out because there is not enough RAM. Having a swapfile in place can help with this. A swapfile is particularly a good idea if your project uses Gulp.

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

###### Possible issues:
- If the you get an error related to the ```fallocate``` command, it may be that the swapfile is currently on. You cannot fallocate a swapfile that is currently in use. Try turning off the swapfile with ```swapoff /swapfile``` and then entering the commands again, starting with ```fallocate -l 1G /swapfile``` and ending with ```swapon /swapfile``` (which turns it back on). 

</details>


***


## nginx (optional)
When you have multiple files to host, nginx will let you keep them on the same droplet by watching for traffic coming to your droplet and routing that traffic to the appropriate project on the droplet.

<details> <summary> nginx installation and configuration </summary> 

###### Install nginx
- Run ```sudo apt-get install nginx```. 
- The ```nginx/``` folder should now be installed in the ```/etc/``` folder. Inside ```nginx/```, Ubuntu should have installed multiple files and folders, including ```sites-available/``` and ```sites-enabled/```. If these two folders are not here, create them inside the ```nginx/``` folder by running ```touch sites-available sites-enabled```. Although the simplest way is to edit the default file that was probably created for you in ```sites-available/```, it may be a better practice to leave the default file alone and instead create and configure a small file for each hosted project site. 
- After making configuration files in ```sites-available``` for each project, we will make links to those files in the ```sites-enabled``` folder. nginx uses this ```sites-enabled/``` folder to know which projects should be active.

###### Configure nginx 
- Go to nginx's "sites-available/" folder by running ```cd /etc/nginx/sites-available```.
- SIDE NOTE: A default file was probably created automatically in ```sites-available/``` by the installation. This file has many comments and shows sample configurations for nginx files. If you do not have a default file, below is an example of a default file. It is just a reference. We won't need it for this tutorial, so feel free to move ahead.

    <details> <summary> default file in sites-available/ </summary>

        ```
        ##
        # You should look at the following URL's in order to grasp a solid understanding
        # of Nginx configuration files in order to fully unleash the power of Nginx.
        # http://wiki.nginx.org/Pitfalls
        # http://wiki.nginx.org/QuickStart
        # http://wiki.nginx.org/Configuration
        #
        # Generally, you will want to move this file somewhere, and start with a clean
        # file but keep this around for reference. Or just disable in sites-enabled.
        #
        # Please see /usr/share/doc/nginx-doc/examples/ for more detailed examples.
        ##

        # Default server configuration
        #
        server {
            listen 80 default_server;
            listen [::]:80 default_server;

            # SSL configuration
            #
            # listen 443 ssl default_server;
            # listen [::]:443 ssl default_server;
            #
            # Note: You should disable gzip for SSL traffic.
            # See: https://bugs.debian.org/773332
            #
            # Read up on ssl_ciphers to ensure a secure configuration.
            # See: https://bugs.debian.org/765782
            #
            # Self signed certs generated by the ssl-cert package
            # Don't use them in a production server!
            #
            # include snippets/snakeoil.conf;

            root /var/www/html;

            # Add index.php to the list if you are using PHP
            index index.html index.htm index.nginx-debian.html;

            server_name _;

            location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
            }

            # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
            #
            #location ~ \.php$ {
            #	include snippets/fastcgi-php.conf;
            #
            #	# With php7.0-cgi alone:
            #	fastcgi_pass 127.0.0.1:9000;
            #	# With php7.0-fpm:
            #	fastcgi_pass unix:/run/php/php7.0-fpm.sock;
            #}

            # deny access to .htaccess files, if Apache's document root
            # concurs with nginx's one
            #
            #location ~ /\.ht {
            #	deny all;
            #}
        }


        # Virtual Host configuration for example.com
        #
        # You can move that to a different file under sites-available/ and symlink that
        # to sites-enabled/ to enable it.
        #
        #server {
        #	listen 80;
        #	listen [::]:80;
        #
        #	server_name example.com;
        #
        #	root /var/www/example.com;
        #	index index.html;
        #
        #	location / {
        #		try_files $uri $uri/ =404;
        #	}
        #}
        ```
    </details>

- Inside the ```sites-available/``` folder, create a file for each project using the ```touch``` command followed by the name of your project. No file extension is needed for these files. 
- Use ```nano``` to open each file and put in code in the format below. Notice the comments telling you what changes to make for your project.


```
server {
    listen 80; #80 IS THE USUAL PORT TO USE HERE

    server_name your_project.your_domain.com #PUT YOUR DOMAIN HERE

    location / {
        proxy_pass http://127.0.0.1:3001; #PUT YOUR SERVER PORT HERE
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

- If you are wondering how nginx knows to check each of these new files you linked to in ```/sites-enabled```, take a look at the ```nginx.conf``` file in the ```nginx/``` folder by running ```cat /etc/nginx/nginx.conf```. Near the bottom of the file, you should see ```include /etc/nginx/sites-enabled/*;```. This line points nginx to each file in ```/sites-enabled```, so any new file you create there will be included.



###### Possible issues:
- Welcome to nginx page.

</details>


***


## prep code for production
###### turn off React's service worker
If your project was bootstrapped using create-react-app, a default service worker was registered in your index.js file. Make sure ```registerServiceWorker()``` is commented out or that the service worker is otherwise not registered. Doing so will save some headaches caused when trying to serve your local files and server files together.

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

<details> <summary> Other recommendations </summary>

###### Title the app
Inside your index.html file, find the ```<title>``` tags inside the ```<head>```. If you used create-react-app, the index.html file will be inside the public/ folder and the ```title``` tags will say ```<title>React App</title>```. Inside ```<title></title>```, put the name of your app. This name will appear in the browser tab when you go to your site.

###### Customize the README
If you used create-react-app, your README is full of boilerplate docs about create-react-app. Delete all of this and replace it with your own content. A good use for this README is to introduce users to your app with an introduction or overview or images that help the user understand how the app works. This can be particularly useful for portfolio pieces, since potential employers will have an intro page showing them what your app is all about and what they should expect.

</details>

***


## copy project to server
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


###### Possible issues:
- It is possible to get a timeout error when running a build on your server. This may happen if your droplet is the cheapest tier (with the least RAM). You might fix this by implementing a swapfile (see the optional section on swapfiles). If you already created a swapfile, trying running through all those swapfile commands again (perhaps there was an error when creating it the first time).
- If you see an error saying ```npm build``` was called without any arguments, try ```npm run build``` instead. Your ```package.json``` file shows both ```start``` and ```build``` together in the ```scripts``` section, and you are used to running ```npm start``` (with no "run" command), so you may think you can run ```npm build``` the same way. It is true that leaving out ```run``` is a shorthand way of running scripts, but there is already a built-in npm command for ```npm build``` (used to build Node add-ons FYI), and that built-in command overshadows the ```npm build``` shorthand. **TL;DR**: Try ```npm run build``` instead.


***


## test with Node
- Test to see if your hosted project works. Try running node on your server file. If, for example, your server file is called "index.js" and it is inside a folder called "server", run ```node server/index.js```.
- Now enter your IP address (the one Digital Ocean gave you) in the browser URL bar, followed by a ```:``` and the port your server is running on (e.g., ```127.48.12.123:3100```). Your hosted site should appear. You are almost done! Currently, your site is running but will stop as soon as you close the command line window or otherwise terminate the running Node process.
- Once you've tested your site, use Ctrl+C to terminate the Node process. To keep your app running forever, move on to the final required step, in which you will install something called forever.

###### Possible issues:
- You might run Node and find that your app's front end does not appear. Perhaps you see the words ```Cannot GET``` in the browser. Try testing one of your GET endpoints to see if the back end works by typing the endpoint URL into the browser bar (e.g., '122.48.12.123:3100/api/products'). If the browser correctly displays data from your endpoint, this probably indicates that your project is hosted on the server but your server file is not pointing to your build folder correctly. 
    - Carefully check the express.static line again. It is easy to miss a slash (```/```) or a period (```.```). The correct code should probably look like this: ```app.use( express.static( `${__dirname}/../build` ) );```. Notice the ```__dirname``` variable (with two underscores), followed by a slash, then traveling up one folder and going into the ```build``` folder. 
    - You might also double-check that you ran ```npm run build``` to create a build folder.


***


## forever

###### Install and start forever
- Use ```npm install -g forever``` to install.
- Use ```cd``` to go to the top level of your project file.
- Use ```forever start [path to server file]``` to start forever (e.g., ```forever start server/server.js```). 

###### After starting forever
- Once you use the ```forever start``` command, your project should be running and accessible online even after closing the command line window.
- If you need to deploy new changes to your project after hosting it, try this [cheat sheet](https://github.com/Alan-Miller/digital-ocean/blob/master/cheat-sheet-for-editing-hosted-project.md).

###### Possible issues:
- **-a option**: When starting a process, you may see these two errors:```error: Cannot start forever``` and ```error: log file /root/.forever/[some_process].log exists. Use the -a or --append option to append log```. If so, trying simply adding the ```-a``` option to your previous ```start``` command.
- **Miscellaneous**: Sometimes you run forever and it fails and you don't know why. It can be helpful to stop forever and instead go back to testing the project by running Node, which will often give more useful errors. Running Node also lets you see console logs in your server code, which can help you debug.

<details> <summary> Additional forever commands </summary>

- ```forever list```: Shows currently running forever processes. Notice how each process has a UID and a PID.
- ```forever restart [id]```: Restart a specific process, replacing [id] with the process UID or PID.
- ```forever restartall```: Restart all current processes.
- ```forever stopall```: Stop all processes.
- ```forever -h```: Help. Shows other forever actions and options.
- ```forever columns [add/rm] [column name]```: Format the list of processes you see when you run ```forever list``` by adding or removing columns using the column name. For instance, you might remove the "command" column or the "logfile" column if you don't find that information useful. You might add the "dir" column because it shows the project folder forever is running in, which is helpful for identifying which project is running which process.
- ```-a --uid [custom UID]``` options together with ```forever start```: These options let you set the UID to whatever numbers or characters you want. This can be convenient for both identifying which process is running and for easily remembering the UID so you can stop or restart the process quickly. For example, if you have an app called Foto, you could start forever with ```forever -a --uid foto start server/server.js``` to create a "foto" UID, and ```forever restart foto``` to restart that process.


</details>