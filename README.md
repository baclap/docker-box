# Prerequisites
This tutorial assumes you have [Vagrant](http://vagrantup.com/) installed and working.

# Create Project Directory
- `mkdir docker-box`
- `cd docker-box`

# Setup Vagrant Virtual Machine

## Initialize
- `vagrant init ubuntu/xenial64`
    - Ubuntu 16.04

## Configure and Provision
- Edit Vagrantfile
    - uncomment line 29 and 40
        - replace line 40 with `config.vm.synced_folder ".", "/home/vagrant/docker-box"`
- `vagrant up`

## SSH Into Virtual Machine
- `vagrant ssh`
    - `whoami`
        - shows you what user you are logged in as on the machine
        - should say "ubuntu"
    - `groups ubuntu`
        - shows you the groups the "ubuntu" user belongs to
        - the list includes "sudo", meaning you can execute commands as a super user, which we will do quite often

# Docker

## Install Docker on the Virtual Machine
- Follows the official instructions from Docker docs: https://docs.docker.com/engine/installation/linux/ubuntu/
- `sudo apt-get update`
- `sudo apt-get install curl \
    linux-image-extra-$(uname -r) \
    linux-image-extra-virtual`
- `sudo apt-get install apt-transport-https \
    ca-certificates`
- `curl -fsSL https://yum.dockerproject.org/gpg | sudo apt-key add -`
- `apt-key fingerprint 58118E89F3A912897C070ADBF76221572C52609D`
    - You should see:
        ```
        pub     4096R/2C52609D 2015-07-14
        Key fingerprint = 5811 8E89 F3A9 1289 7C07  0ADB F762 2157 2C52 609D
        uid     Docker Release Tool (releasedocker) <docker@docker.com>
        ```
- `sudo apt-get install software-properties-common`
- `sudo add-apt-repository \
    "deb https://apt.dockerproject.org/repo/ \
    ubuntu-$(lsb_release -cs) \
    main"`
- `sudo apt-get update`
- `sudo apt-get -y install docker-engine`
- Test the installation was successful:
    - `sudo docker run hello-world`

## Install Docker Compose on the Virtual Machine

- `sudo -i`
    - become root
- `curl -L https://github.com/docker/compose/releases/download/1.11.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose`
- `chmod +x /usr/local/bin/docker-compose`
- `exit`
    - exit root
- `sudo usermod -aG docker ${USER}`
    - add self to docker group






# Create Laravel App

## Install PHP 7.0
- `sudo apt-get install php7.0 php-xml`
    - TODO: Are the composer deps below all necessary? Should some be moved up here?

## Install Composer
- `cd /home/vagrant/docker-box`
    - change into the directory on the vm that syncs with our local docker-box directory
- Loosely following instructions from Digital Ocean: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-composer-on-ubuntu-16-04
- `sudo apt-get install curl php-cli php-mbstring git unzip`
- `curl -sS https://getcomposer.org/installer -o composer-setup.php`
- `php -r "if (hash_file('SHA384', 'composer-setup.php') === '55d6ead61b29c7bdee5cccfb50076874187bd9f21f65d8991d46ec5cc90518f447387fb9f76ebae1fbbacf329e583e30') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"`
    - ensure the installer signature (that long string starting with "55d6") is accurate according to https://composer.github.io/pubkeys.html, if this check does not verify correctly the composer-setup.php file downloaded in the previous step will be deleted
        - if the check does fail you can re-download it by running the previous `curl` command again
- `php composer-setup.php`
    - creates a `composer.phar` file which we can use to execute composer commands without installing composer globally
- `./composer.phar`
    - test installation was successful

## Create `laravel-app`
- `./composer.phar create-project --prefer-dist laravel/laravel laravel-app`
- `cd laravel-app`
- `php artisan serve --host 0.0.0.0`
    - http://stackoverflow.com/questions/23999704/cant-view-laravel-project-in-browser-with-vagrant
    - go to http://192.168.33.10:8000 in your browser to test that it's working

## Remove `composer-setup.php` and move `composer.phar` into `laravel-app/`
- `cd ../`
    - `pwd` should show that you are in `/home/vagrant/docker-box`
- `rm composer-setup.php`
    - this script created `composer.phar`, we don't need it anymore
- `mv composer.phar ./laravel-app/composer.phar`
    - composer will only be used for the laravel app so keep the phar in that dir









NEXT UP: DOCKER... https://kyleferg.com/laravel-development-with-docker/
