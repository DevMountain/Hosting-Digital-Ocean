# Hosting your database on Digital Ocean

## Postgres on Digital Ocean Ubuntu droplet

###### Install Postgres
- Run ```sudo apt-get update```.
- Run ```sudo apt-get install postgresql postgresql-contrib```.

###### Create a database
- Run ```sudo -u postgres createdb [db_name]``` to create a new database owned by the postgres role. For example, ```sudo -u postgres createdb wonderapp``` will create a database called "wonderapp" for the postgres user.
- For more Postgres options and commands, including how to create and edit tables through psql commands, see these [Digital Ocean Postgres docs](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04#create-a-new-database).
- To simply copy a pre-existing local (or hosted-elsewhere) database to your newly created Digital Ocean database, see pg_dump instructions in the **Copy and transfer database** section below.

###### See all databases on droplet
- Inside your droplet, run ```psql``` to enter a Postgres session.
- Run ```\l``` to list database information.

***

## Copy and transfer database
Whether you have a local database you want to copy to your server (e.g., local to Heroku) or a hosted database you want to move to a different server (e.g., Heroku to Digital Ocean), the ```pg_dump``` command is your friend. This command is installed with Postgres and can quickly copy over the contents of one database to another. 

###### pg_dump commands and options
The exact format you use for each part of your pg_dump command will depend a bit on where the database is and whether it is the source or the destination. Note that all these commands are done from your local computer, not from inside your server.
- When using the ```pg_dump``` command, you might consider using the ```-O``` and ```-c``` options with it. Use ```-O``` (shorthand for ```--no-owner```) to forego any commands to set ownership to match the original database. Use ```-c``` (shorthand for ```--clean```) to clean (drop) the destination database's objects before dumping in the new ones.
- To copy a local database, just include the database name and any options: ```pg_dump -Oc wonderapp```.
- To copy a Digital Ocean database, use ```ssh``` to log in, ```-U``` to specify a user, and ```-h``` to specify the host: ```ssh root@123.456.7.89 "pg_dump -Oc -U anna -h localhost wonderapp"```. You will be prompted for the droplet user's password. Alternatively, if configured your user's SSH login and added the SSH password to the ssh-agent keychain (see the [**Additional SSH login options**](https://github.com/Alan-Miller/digital-ocean/blob/master/README.md#connect-to-server) section from the [README](https://github.com/Alan-Miller/digital-ocean/blob/master/README.md)), you do not need the user or host options and can just use something like this: ```ssh anna "pg_dump -Oc wonderapp"```.

###### Pipe contents from one db to another
- pg_dump -O -c wonderapp | heroku pg:psql -a wonderapp
- From Digital Ocean droplet to local db: ```ssh root@123.456.7.89 "pg_dump -Oc wonderapp" | psql wonderapp```

###### Make database backup
It can be a good idea to make a backup file of your database that you can store on your own computer. If you ever needed it, you could use it to dump into a new hosted database.
- Create a backup SQL file on your Desktop with ```touch ~/Desktop/db_bkup.sql```.
- Dump the contents of a database into this file using ```pg_dump``` and the ```>``` (greater than) symbol instead of a ```|``` (pipe). Instead of piping the contents into another database you are using ```>``` to write the contents into this file, overwriting any pre-existing content in that file.
- An example of backing up a local database:
```sh
    touch ~Desktop/wonderapp_bkup.sql
    pg_dump -Oc wonderapp > ~/Desktop/wonderapp_bkup.sql
```

###### Possible issues
- **pg_dump command not found:** You may try to use the ```pg_dump``` command only to have your command line editor say something like "command not found," even though you are sure you correctly installed pg_dump. If so, you may need to find out where it was installed and point your command line editor to that path using your .bash_profile or .bashrc file. Try the following:
    - If you know exactly where pg_dump was installed, copy the full path. 
    - **find command**: If you do not know where pg_dump is installed, use ```find``` to search for pg_dump. 

        <details> <summary> how to use find command </summary>

        - The ``find`` command includes a path, options, and a search expression. For example, if you thought pg_dump was installed in your ```/Applications``` folder and wanted to search for pg_dump by its name, you might try ```find /Applications -name pg_dump``` (where ```-name``` is the search-by-name option). 
        - If you have no idea where pg_dump was installed, you might try simply ```find / -name pg_dump 2>/dev/null``` to search your entire root folder (since you are searching all your folders, use the ```2>/dev/null``` command to suppress errors, limiting your search to more useful results). 
        - Find the correct path to pg_dump in the search results and copy it. Here is an example of a search result showing where pg_dump might be installed:

        ```sh
            /Applications/Postgres.app/Contents/Versions/9.5/bin/pg_dump
        ```
        
        </details>
    
    - Once you have copied the path to pg_dump, open the .bash_profile or .bashrc file in your home folder (e.g., if you use VS Code, type ```open -a Visual\ Studio\ Code ~/.bash_profile```). Your command line editor uses this file to configure your command line. In the file, on a new line, type 

        ```export PATH="/Path/To/Folder/PgDump/Is/In/:$PATH"```. 
    
        Point the path to the folder pg_dump is in, not to pg_dump itself, and make sure to include ```export PATH=``` and ```:$PATH``` on either end. Once your .bash_profile has the updated PATH code, save and close this file. 
    - Open a new command line window (your CLI only notices changes to the .bash_profile when opening a new window) and try the ```pg_dump``` command again.

***

## Access Digital Ocean database from local computer using SSH tunnel
If you hosted your database on Digital Ocean and are noticing difficulty connecting to it from your local computer (e.g., through a GUI on your computer like PGAdmin or SQL Tabs), try an SSH tunnel to securely connect to the remote database.
- To create a secure tunnel, run ```ssh -N -L [new_port]:[host]:[remote_port] [user]``` in the command line. For example, if the server user is "anna" and the database is a Postgres database running on 5432, the user might run ```ssh -N -L 5555:localhost:5432 anna``` to create a secure tunnel and forward the local port 5555 to the remote port 5432, encrypting it in the process. For more on this, see [this article](http://www.revsys.com/writings/quicktips/ssh-tunnel.html) or [this article](https://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html).
- Connect to the database using localhost and the new port. For instance, in the situation above, the user anna could then use a GUI to connect to the database on port 5555 on localhost. 
- Keep in mind the tunnel only stays open as long as the command line window is open where the tunnel command was entered. Closing the window will disconnect you from the database.

***

## Change your db user password:
log into psql then ```\password```