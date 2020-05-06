# Docker-Compose for Pimcore 5 and Pimcore 6
Simple and easy Docker-Compose configuration for Pimcore 5 and Pimcore 6.

Docker-Compose consists of the following images:
 - Redis
 - MariaDB 10.4
 - httpd (Apache 2.4) & PHP-FPM with PHP7.2 and all Pimcore required dependencies (LibreOffice, FFMPEG, Image Libraries, etc)
 - PHP-FPM with PHP7.2 and all Pimcore required dependencies (LibreOffice, Image Libraries, etc) (except FFMPEG)

## Acknowledgment
Credit to [Dominik](https://github.com/dpfaffenbauer), this repo was bases off of [Dominik's pimcore-docker-compose](https://github.com/dpfaffenbauer/pimcore-docker-compose).
 
## Getting Started
### Requirements
* git
* docker
* docker-compose

### *Windows Setup
#### Setup Docker on Windows WSL2
https://www.hanselman.com/blog/HowToSetUpDockerWithinWindowsSystemForLinuxWSL2OnWindows10.aspx

When composing the pimcore stacks make sure you are running it from your Linux distro, the Windows file system does not play nice with this install (it takes forever so install and download the files and the install will not be successful).
There will also be performance improvements running on WSL.

### Checkout Repo
```bash
git clone https://github.com/dpfaffenbauer/pimcore-docker-compose.git
cd pimcore-docker-compose/
 ```
### Run Containers
```bash
# initialize and startup containers
docker-compose up -d
```
### Install Pimcore 
Choose which package to install
#### Pimcore 5 
https://pimcore.com/docs/5.x/Development_Documentation/Getting_Started/Installation.html#page_Choose-a-package-to-install
#### Pimcore 6
https://pimcore.com/docs/6.x/Development_Documentation/Getting_Started/Installation.html#page_Choose-a-package-to-install

#### Skeleton
```bash
# get shell in running container
docker exec -it pimcore-skeleton-php bash
# inside pimcore-skeleton-php container
# get code
COMPOSER_MEMORY_LIMIT=-1 composer create-project pimcore/skeleton tmp-skeleton
# move code
mv tmp-skeleton/.[!.]* .
mv tmp-skeleton/* .
rmdir tmp-skeleton
#increase the memory_limit to >= 512MB as required by pimcore-install
echo 'memory_limit = 512M' >> /usr/local/etc/php/conf.d/docker-php-memlimit.ini;
service apache2 reload
#run installer, if you need to reinstall you can add --ignore-existing-config
./vendor/bin/pimcore-install --admin-username admin --admin-password admin --mysql-host-socket=db-skeleton --mysql-username=pimcore-skeleton --mysql-password=pimcore-skeleton --mysql-database=pimcore-skeleton --no-interaction --ignore-existing-config
# fix permissions
chown www-data: . -R 
exit

```

#### Demo
```bash
# get shell in running container
docker exec -it pimcore-demo-php bash
# inside pimcore-demo-php container
# get code
COMPOSER_MEMORY_LIMIT=-1 composer create-project pimcore/demo tmp-demo
# move code
mv tmp-demo/.[!.]* .
mv tmp-demo/* .
rmdir tmp-demo
#increase the memory_limit to >= 512MB as required by pimcore-install
echo 'memory_limit = 512M' >> /usr/local/etc/php/conf.d/docker-php-memlimit.ini;
service apache2 reload
#run installer, if you need to reinstall you can add --ignore-existing-config
./vendor/bin/pimcore-install --admin-username admin --admin-password admin --mysql-host-socket=db-demo --mysql-username=pimcore-demo --mysql-password=pimcore-demo --mysql-database=pimcore-demo --no-interaction --ignore-existing-config
# fix permissions
chown www-data: . -R 
exit

```

### Use
After the installer is finished, you can open in your Browser:
#### Skeleton
* Frontend: http://localhost:2000
* Backend: http://localhost:2000/admin
#### Demo
* Frontend: http://localhost:2010
* Backend: http://localhost:2010/admin

### Common Errors 

#### File permissions 
On some machines docker has problems with the relative symlinked (static) files. Run those commands in your `pimcore-php` container 

```bash 
docker-compose exec php bash 
chown www-data: . -R 
```

This could take a while because of the amount of files inside the directory (especially because of the `vendor` folder). There is no guarantee that those commands on all machines and operating systems. 
