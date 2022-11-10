# Installing WordPress via Docker
Here is a link to my repo: [https://github.com/rhk7913/docker.github.io](url)

This page will outline how to install WordPress through Docker and Docker Compose

# Pre-installation
You will need to download the latest version of Ubuntu to install and use WordPress on VM.

I used the following link to install a Ubuntu 22.04 LTS .iso file: [https://releases.ubuntu.com/22.04/](url)

After the .iso file downloads (it might take a while since it had a size of 3.9 GB), open up VMware Workstation.

Locate "File" in the upper left hand corner.

Select "New Virtual Machine".

Select the "Typical (recommended)" option for the type of configuration.

Select the "Installer disc image file (iso):" option for the guest operating system installation step

Fill out the Easy Install Information.

**Note:** Full name refers to the name of the machine, while user name refers to the name of your user.

Name your virtual machine, preferably something that references Ubuntu.

Set the maximum disk size to 25 GB and select "Split virtual disk as a single file" for the specified disk capacity step.

Select "Customize Hardware..." and set the memory to 6 GB.

Hit finish and power on the VM.

After powering on the VM (it might take a while), complete the following steps:

1. On the Choose the correct keyboard layout page, select your desired keyboard (I used English (US)) and hit Continue.

2. On the Updates and Other Software page, select Normal Installation for "What apps would you like to install to start with?" and Download updates while installing Ubuntu for "Other options" and hit Continue. 

3. On the Installation Type page, select Erase disk and install Ubuntu and hit Install Now. (**Note:** Erase disk and install Ubuntu will only erase the content on the VM since it is isolated.)

4. On the Write the changes to disks page, hit Continue.

5. On the Where are you? page, click the region closest to your time zone (I selected Chicago) and hit Continue.

6. On the Who are you? page, enter information for each field and hit Continue.
**Note:** It is recommended to require your password to log in on each attempt and not to login automatically.

7. Let the installation process finish (it may take a while). Once the Installation Complete page appears, hit Restart Now.

8. After the VM restarts, you will now be able to login into your VM!

# Install Docker

Docker is a platform that allows for operating-system level virtualization in packages called containers.

Before installing Docker, it is good practice to update your already existing packages.

The following command will update your already existing packages:
```
sudo apt update
```

The following commands will install Docker:
```
sudo apt install docker.io
```

# Install Docker Compose

**Reference:** [https://docs.docker.com/compose/install/](url)

Docker Compose is a tool that allows for running multi-container applications on Docker. 

The following command will install the latest version of Docker Compose:
```
sudo apt install docker-compose-plugin
```

The following command will verify that Docker Compose was properly installed:
```
docker compose version
```

This was my Docker Compose version:

![Ubuntu 64-bit (2)-2022-11-10-10-07-22](https://user-images.githubusercontent.com/114512130/201163472-062faeb6-04d0-4208-9c48-6d84b25f64ec.png)

**Note:** I used the following references, https://linuxiac.com/wordpress-with-docker/ and https://www.linode.com/docs/guides/wordpress-with-docker-compose/, for the remaining steps. 

# Create a directory

A directory is created to store the data from WordPress for Docker Compose.

The following command will create a WordPress directory:
```
sudo mkdir -p /srv/wordpress
```

In order to collect data from WordPress, you will need to change directories to the newly created WordPress directory.

The following command will allow you to enter the WordPress directory:
```
cd /srv/wordpress
```

# Create a YAML file
YAML files are used for configuration processes, including applications that deal with storing and transmitting data. Here, a YAML file is used to deal with the transmission of data for the WordPress application.

While in the WordPress directory, you will need to install a text editor (nano, vim, etc). I chose to use vim.

The following command will install vim:
```
sudo apt install vim
```

You will need to switch to the root user to configure the YAML file.

The following command will switch you to the root user:
```
sudo -s
```

The following command will create a docker-compose.YAML file for you to edit:
```
vim docker-compose.yaml
```

Insert the following to the docker-compose.YAML file using the Insert key:
```
version: '3'
services:
  mysql:
    image: mysql:latest
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: my_password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress_user
      MYSQL_PASSWORD: wordpress_password
    volumes:
      - mysql_data:/var/lib/mysql
  wordpress:
    image: wordpress:latest
    depends_on:
      - mysql
    ports:
      - 8080:80
    restart: always
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: wordpress_user
      WORDPRESS_DB_PASSWORD: wordpress_password
    volumes:
      - ./wp-content:/var/www/html/wp-content
volumes:
  mysql_data:
```

Save the YAML file by hitting ESC and exit the file by typing :wq.

After exiting the file, you will need to check if the YAML file was saved properly.

The following command will check to see if the YAML file was saved properly:
```
cat docker-compose.yaml
```

After ensuring that the YAML file was saved properly, you will need to exit from the root user.

The following command will exit you out of the root user:
```
exit
```

# Running WordPress with Docker Compose

In order to run WordPress, you will need to be in your created WordPress directory.

The following command will enter the WordPress directory:
```
cd /srv/wordpress
```

After changing into the WordPress directory, the following command will install the WordPress image from the newly created YAML file:
```
sudo docker-compose up -d
```

**Note:** After the running the above command, there will be a series of downloading and waiting messages for the WordPress image. In addition, a MySQL image for a MySQL container will be downloaded. 

# Accessing WordPress

To access WordPress, open up a web browser of your choice and enter either http://localhost:8080 or [http://your_ip_address:8080](url) in your url bar.

This is what my WordPress page looked like for both choices (they have the same interface):

http://localhost:8080: 

![Ubuntu 64-bit (2)-2022-11-08-10-12-34](https://user-images.githubusercontent.com/114512130/201168213-16f98f37-b38f-411e-aa70-6ee82cf423a6.png)

[http://your_ip_address:8080](url) (My ip address was 192.168.246.135): 

![Ubuntu 64-bit (2)-2022-11-08-10-15-18](https://user-images.githubusercontent.com/114512130/201168249-da873852-0229-42be-a1bc-97dd372edc29.png)

After navigating to the WordPress page, follow the instructions on the screen and make a WordPress account. 

After creating a WordPress account, you should have access to a Admin Interface.

This was my Admin Interface:

![Ubuntu 64-bit (2)-2022-11-08-10-17-33](https://user-images.githubusercontent.com/114512130/201168986-198c963f-cc65-40ab-8c11-eeddf301c231.png)

Afterwards, you should be able to make a WordPress page!

This was my WordPress page on the VM:

![Ubuntu 64-bit (2)-2022-11-10-14-56-18](https://user-images.githubusercontent.com/114512130/201204212-6f8f0582-ada6-4670-a5e4-c295fdbc6eac.png)

This was my WordPress page outside of my VM:

![Website](https://user-images.githubusercontent.com/114512130/201203264-a95f1ffe-f2de-425f-adc3-f0b6d730da5a.png)
