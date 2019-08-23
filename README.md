# low_mem_mysql
This is the repository with a docker-compose file and a mySQL configuration file to run mySQL on low VPS.
## Recomended VPS configuration
- OS: Ubuntu 16.04 x64
- RAM: 512 MB
- CPU: 1
- Disk space: 10 GB
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
`sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose`
2. Next we'll set the permissions:  
`sudo chmod +x /usr/local/bin/docker-compose`
3. Then we'll verify that the installation was successful by checking the version:  
`docker-compose --version`

- For more info, visit: https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-16-04
### Install the repository into the VPS
1. Create a new directory in a common place:  
`sudo mkdir /low_mem_mysql`
1. Step into the just created folder:  
`cd /low_mem_mysql/`
1. Clone the repository:  
`git clone https://github.com/RafaelMiquelino/low_mem_mysql.git`

### Restore the database
1. In the machine and the respective folder where the backup is located, transfer the backup file to the VPS:  
`rsync -avzhe ssh ./backup.sql user@VPS_ip:~/backup.sql`
2. Connect to the VPS via SSH:  
`ssh user@VPS_ip`
2. Step into the clonned repository:  
`cd low_mem_mysql`
2. Run the MySQL database:  
`docker-compose up -d`
3. Transfer the last backup to the database:  
`cat ~/backup.sql | docker exec -i db /usr/bin/mysql -u user --password=password database`
### Run the complete set of services
1. Step into the working tree destination folder:  
2. Check if everything is working via the commands:  
`docker-compose ps` or `docker ps`  
`docker-compose logs`
