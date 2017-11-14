# Digital Ocean

### Swap
Many students buy a Digital Ocean droplet on the $5 tier, which comes with limited RAM. A swapfile can effectively extend the amount of given RAM by swapping out less-recently-used files to the hard disk.

```cli
touch /swapfile
fallocate -l 1G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

- Turn off the swapfile with ```swapoff /swapfile```.
- Turn the swapfile back on with ```swapon /swapfile```.
- Make the droplet load with the swapfile by editing the fstab file. Use ```nano /etc/fstab``` to open the file and at the bottom of the file type in ```/swapfile   none    swap    sw    0   0```.
- You can lower the swappiness (which tells the server to swap files less often) by adjusting a setting int he sysctl.conf file. Use ```nano /etc/sysctl.conf``` to open the file and type ```vm.swappiness=10``` to set the swappiness to 10 (instead of the default 60).



