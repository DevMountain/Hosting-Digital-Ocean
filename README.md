# Digital Ocean

### Swapfile (optional)
Many students buy a Digital Ocean droplet on the $5 tier, which comes with limited RAM. A swapfile can effectively extend the amount of given RAM by swapping out less-recently-used files to the hard disk. This can come in handy. For example, sometimes when running a build on a low-RAM droplet, the process will time out because there is not enough RAM. Having a swapfile in place can help with this.

```cli
touch /swapfile
fallocate -l 1G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

<details> <summary> possible bugs </summary> 

- If the you get an error related to the ```fallocate``` command, it may be the the swapfile is currently on. You cannot fallocate a swapfile that is currently in use. Try turning off the swapfile with ```swapoff /swapfile``` and then trying the commands again, starting with ```fallocate -l 1G /swapfile``` and ending with ```swapon /swapfile``` (which turns it back on). 
</details>

- If you need to turn off the swapfile: ```swapoff /swapfile```.
- If you need to turn the swapfile back on: ```swapon /swapfile```.
- If you want to have the droplet load with the swapfile on, use ```nano /etc/fstab``` to open the fstab file, and at the bottom type ```/swapfile   none    swap    sw    0   0```.
- If you want to tell the server to swap files less often, use ```nano /etc/sysctl.conf``` to open the sysctl.conf file and type ```vm.swappiness=10``` to set the swappiness to 10 (instead of the default 60).



