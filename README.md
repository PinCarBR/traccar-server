# Traccar server
This is the repository to create the traccar-server with a standalone MySQL database and Traefik reverse proxy via docker-compose file.
## Minimum VPS configuration
- OS: Ubuntu 16.04 x64
- RAM: 512 MB
- CPU: 1
- Disk space: 10 GB
- 1GB swap: see https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-16-04
## Setup Instructions
### Install Docker
1. First, in order to ensure the downloads are valid, add the GPG key for the official Docker repository to your system:  
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
2. Add the Docker repository to APT sources:  
`sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"`
3. Next, update the package database with the Docker packages from the newly added repo:  
`sudo apt-get update`
4. Make sure you are about to install from the Docker repo instead of the default Ubuntu 16.04 repo:  
`apt-cache policy docker-ce`  
 You should see output similar to the follow:  
```
docker-ce:
    Candidate: 18.06.1~ce~3-0~ubuntu
    Version table:
        18.06.1~ce~3-0~ubuntu 500
            500 https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
```
5. Finally, install Docker:  
`sudo apt-get install -y docker-ce`
6. Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it's running:  
`sudo systemctl status docker`
7. If you want to avoid typing sudo whenever you run the docker command, add your username to the docker group:  
`sudo usermod -aG docker ${USER}`
8. To apply the new group membership, log out of the server and back in.
9. Afterwards, you can confirm that your user is now added to the docker group by typing:  
`id -nG`  

- For more info, visit: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04
### Install Docker Compose
1. We'll check the current release and if necessary, update it in the command below:  
`ssudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
2. Next we'll set the permissions:  
`sudo chmod +x /usr/local/bin/docker-compose`
3. Then we'll verify that the installation was successful by checking the version:  
`docker-compose -v`

- For more info, visit: https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-16-04
### Install the repository into the VPS
1. Clone the repository.
1. Step into the just created folder 

### Configure environment variables
1. Set your mysql username and password parameters as environment variables:  
`export MYSQL_USER=YOUR_USERNAME` `export MYSQL_ROOT_PASSWORD=YOUR_ROOT_PASSWORD` `export MYSQL_PASSWORD=YOUR_USER_PASSWORD` `export MYSQL_DATABASE=YOUR_DATABASE_NAME`
2. Set your domain:  
`export DOMAIN=YOUR_DOMAIN.com`
2. Alternatively you can set all these variables in `.env` file and export all at once on the system startup by adding the following command to your .bashrc:  
`export $(<PATH_TO_THE_REPOSITORY/.env)`

### Customize your volumes in the `docker-compose.yml` file according to the customization you want to have
1. Set your mysql username and password parameters as environment variables:  
`export MYSQL_USER=YOUR_USERNAME` `export MYSQL_ROOT_PASSWORD=YOUR_ROOT_PASSWORD` `export MYSQL_PASSWORD=YOUR_USER_PASSWORD` `export MYSQL_DATABASE=YOUR_DATABASE_NAME`
2. Set your domain:  
`export DOMAIN=YOUR_DOMAIN.com`
2. Alternatively you can set all these variables in `.env` file and export all at once on the system startup by adding the following command to your .bashrc:  
`export $(<PATH_TO_THE_REPOSITORY/.env)`

### Create the traccar configuration file
1. Create a `traccar.xml` file with your configuration inside the folder `traccar/conf/`
1. A sample configuration file is provided on `traccar/conf/sample_traccar.xml`
1. Replace `[DATABASE]` with your MySQL database name
1. Replace `[USER]` with your MySQL username
1. Replace `[PASSWORD]` with your MySQL passoword
1. For more details on the available options fro traccar configuration file, pelase check: https://www.traccar.org/configuration-file/

### Create the traefik configuration file
1. Create a `traefik.toml` file with your configuration inside the folder `traefik/`
1. A sample configuration file is provided on `traefik/traefik_sample.toml`. The standard login\username for the Web UI is admin\admin
1. For configuration examples, including Let's Encrypt support please check https://docs.traefik.io/v1.7/user-guide/examples/

### Restore the database
#### IF YOU HAVE A DATABASE BACKUP, ENSURE YOU DO THIS BEFORE STARTING TRACCAR-SERVER
1. In the machine and the respective folder where the backup is located, transfer the backup file to the VPS:  
`rsync -avzhe ssh ./backup.sql user@VPS_ip:~/backup.sql`
2. Connect to the VPS via SSH:  
`ssh user@VPS_ip`
2. Step into the clonned repository:  
`cd traccar-server`
2. Run the MySQL database:  
`docker-compose up -d db`
2. Check if everything is working via the commands:  
`docker-compose ps` or `docker ps`  
`docker-compose logs`
3. Transfer the last backup to the database:  
`docker exec -i db sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD" "$MYSQL_DATABASE"' < ~/backup.sql`

### Aditional customization
1. Customize your volumes in the `docker-compose.yml` file according to the customization you want to have

### Execute all the services
`docker-compose up -d` or `docker-compose up -d --build --force-recreate` to force recreation of image and container  
Check if everything is working via the commands:  
`docker-compose ps` or `docker ps`  
Check the logs of each container via the command:  
`docker-compose logs container_name`

### Other useful repositories
- **[mysql-autobackup](https://github.com/RafaelMiquelino/mysql-autobackup):** Perform periodic backup of your traccar MySQL database.  
- **[rclone-autobackup](https://github.com/RafaelMiquelino/rclone-autobackup):** Sync your database backups to the cloud.  
- **[flask-text-reader](https://github.com/RafaelMiquelino/flask-text-reader):** Make your Traccar log file reachable from the browser  


To run all the services together, clone all the repositories to your machine, define the same compose project name for all of them at the `.env` file located on each repository and run each service through the `docker-compose up -d` command on each repository.

