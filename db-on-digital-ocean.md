# Hosting your database on Digital Ocean

## Change your db user password:
log into psql then ```\password```

## Copy database to server
Whether you have a local database you want to copy to your server (e.g., local to Heroku) or a hosted database you want to move to a different server (e.g., Heroku to Digital Ocean), the ```pg_dump``` command is your friend.

###### Possible issues
- **pg_dump command not found:** You may try to use the ```pg_dump``` command only to have your command line editor say something like "command not found," even though you are sure you correctly installed pg_dump. If so, you may need to find out where it was installed and point your command line editor to that path using your .bash_profile or .bashrc file. Try the following:
    - If you know exactly where pg_dump was installed, copy the full path. 
    - If you do not know where pg_dump is installed, use ```find``` to search for pg_dump. 

        <details> <summary> find command </summary>
                - The ```find``` command includes a path, options, and a search expression. For example, if you thought pg_dump was installed in your ```/Applications/``` folder and wanted to search for pg_dump by its name, you might try ```find /Applications/ -name pg_dump``` (where ```-name``` is the search-by-name option). 
                - If you have no idea where pg_dump was installed, you might try simply ```find / -name pg_dump 2>/dev/null``` to search your entire root folder (since you are searching all your folders, use the ```2>/dev/null``` command to suppress errors, limiting your search to more useful results). 
                - Find the correct path to pg_dump in the search results and copy it.
        
        </details>

    - Once you have copied the path to pg_dump, open the .bash_profile or .bashrc file in your home folder (e.g., if you use VS Code, type ```open -a Visual\ Studio\ Code ~/.bash_profile```). Your command line editor uses this file to configure your command line. In the file, on a new line, type 

        ```export PATH="/Path/To/Folder/PgDump/Is/In/:$PATH"```. 
    
        Point the path to the folder pg_dump is in, not to pg_dump itself, and make sure to include ```export PATH=``` and ```:$PATH``` on either end. Once your .bash_profile has the updated PATH code, save and close this file. 
    - Open a new command line window (your CLI only notices changes to the .bash_profile when opening a new window) and try the ```pg_dump``` command again.