# One Step Setup

In case you want to know what all the one step setup is doing. Or if you need to change the behavior for some reason, below is the what, how, and why, of what it is doing.

What it's doing.

```cli
touch /swapfile;
fallocate -l 1G /swapfile;
chmod 600 /swapfile;
mkswap /swapfile;
swapon /swapfile;
apt update -y;
apt upgrade -y;
apt install nodejs -y;
apt install npm -y;
npm i -g n;
n stable;
PATH=$PATH;
npm i -g npm;
npm i -g pm2;
apt-get install nginx -y;
```

This can be broken up into 4 main pieces.

swapfile
node
pm2
nginx

## swapfile

Many students buy a Digital Ocean droplet on the \$5 tier, which comes with limited memory. A swapfile can effectively extend the amount of given memory by swapping out less-recently-used files to the hard disk. This can come in handy. For example, sometimes when running a build on a low-memory droplet, the process will time out because there is not enough memory. Having a swapfile in place can help with this. A swapfile is also a good idea if your project uses Gulp.

###### Create swapfile and turn on

```cli
touch /swapfile
fallocate -l 1G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

###### Extra swap options:

- If you need to turn off the swapfile: `swapoff /swapfile`.
- If you need to turn the swapfile back on: `swapon /swapfile`.
- If you want to have the droplet load with the swapfile on, use `nano /etc/fstab` to open the fstab file, and at the bottom type `/swapfile none swap sw 0 0`.
- If you want to tell the server to swap files less often, use `nano /etc/sysctl.conf` to open the sysctl.conf file and type `vm.swappiness=10` to set the swappiness to 10 (instead of the default 60).

###### Possible issues:

- If the you get an error related to the `fallocate` command, it may be that the swapfile is currently on. You cannot fallocate a swapfile that is currently in use. Try turning off the swapfile with `swapoff /swapfile` and then entering the commands again, starting with `fallocate -l 1G /swapfile` and ending with `swapon /swapfile` (which turns it back on).

---

## install Node

The first time you access your droplet, you need to install Node/npm so you can manage and run your code.

- `apt update && apt upgrade` to update the software Linux know about.
- `apt install nodejs -y;apt install npm -y;`. This will install vs 4.2 of node and npm. While it's nice that ubuntu wants to make sure it has a stable version of node.

- `npm i -g n;` Globally installs a program called n that we can use to select what version of node we want to be running.
- `n stable;` Tell n to update us to the latest stable release (you can instead specify the version you want if you need a specific version ie n 8.4.2)
- `npm i -g npm;` Tell npm to install the latest version compatible with the node we just updated to

## install certbot

Certbot is a program to help automate sign-signed certificates. They are used to verify that a person controls the domain name on the certificate. We will be using it to make our droplets use https / SSL security.  
These will register the certbot application in our ubuntu repository, and have it install.

- `snap install certbot --classic`

## install pm2

- `npm i -g pm2` Fairly straight forward, we are going to use pm2 to keep our server running after we leave. So we need to install it globally.

## install nginx

- `apt install nginx -y` Install the program nginx, we will use this as a server router to control which domains go to which ports on our server. Nginx is capable of way more than we have it do here, but this will work as a base entry point.
