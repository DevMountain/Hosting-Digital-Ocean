# Hosting your database on Digital Ocean

## Postgres on Digital Ocean Ubuntu droplet

###### Install Postgres
- Run ```sudo apt-get update```.
- Run ```sudo apt-get install postgresql postgresql-contrib```.

###### Create a database

###### See all databases on droplet
- Inside your droplet, run ```psql``` to enter a Postgres session.
- Run ```\l``` to list database information.


## Copy database to hosted server
Whether you have a local database you want to copy to your server (e.g., local to Heroku) or a hosted database you want to move to a different server (e.g., Heroku to Digital Ocean), the ```pg_dump``` command is your friend.

###### For more options, see [these Digital Ocean Postgres docs](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04#create-a-new-database).

###### Possible issues
- **pg_dump command not found:** You may try to use the ```pg_dump``` command only to have your command line editor say something like "command not found," even though you are sure you correctly installed pg_dump. If so, you may need to find out where it was installed and point your command line editor to that path using your .bash_profile or .bashrc file. Try the following:
    - If you know exactly where pg_dump was installed, copy the full path. 
    - If you do not know where pg_dump is installed, use ```find``` to search for pg_dump. 

        <details> <summary> find command </summary>

        - The ``find`` command includes a path, options, and a search expression. For example, if you thought pg_dump was installed in your ```/Applications/``` folder and wanted to search for pg_dump by its name, you might try ```find /Applications/ -name pg_dump``` (where ```-name``` is the search-by-name option). 
        - If you have no idea where pg_dump was installed, you might try simply ```find / -name pg_dump 2>/dev/null``` to search your entire root folder (since you are searching all your folders, use the ```2>/dev/null``` command to suppress errors, limiting your search to more useful results). 
        - Find the correct path to pg_dump in the search results and copy it.
        
        </details>
    
    - Once you have copied the path to pg_dump, open the .bash_profile or .bashrc file in your home folder (e.g., if you use VS Code, type ```open -a Visual\ Studio\ Code ~/.bash_profile```). Your command line editor uses this file to configure your command line. In the file, on a new line, type 

        ```export PATH="/Path/To/Folder/PgDump/Is/In/:$PATH"```. 
    
        Point the path to the folder pg_dump is in, not to pg_dump itself, and make sure to include ```export PATH=``` and ```:$PATH``` on either end. Once your .bash_profile has the updated PATH code, save and close this file. 
    - Open a new command line window (your CLI only notices changes to the .bash_profile when opening a new window) and try the ```pg_dump``` command again.

## Access Digital Ocean database from local computer using SSH tunnel
If you hosted your database on Digital Ocean and are noticing difficulty connecting to it from your local computer (e.g., through a GUI on your computer like PGAdmin or SQL Tabs), try an SSH tunnel to securely connect to the remote database.
- To create a secure tunnel, run ```ssh -N -L [new_port]:[host]:[remote_port] [user]``` in the command line. For example, if the server user is "anna" and the database is a Postgres database running on 5432, the user might run ```ssh -N -L 5555:localhost:5432 anna``` to create a secure tunnel and forward the local port 5555 to the remote port 5432, encrypting it in the process. For more on this, see [these tips](http://www.revsys.com/writings/quicktips/ssh-tunnel.html).
- Connect to the database using localhost and the new port. For instance, in the situation above, the user anna could then use a GUI to connect to the database on port 5555 on localhost. 
- Keep in mind the tunnel only stays open as long as the command line window is open where the tunnel command was entered. Closing the window will disconnect you from the database.

## Change your db user password:
log into psql then ```\password```

