# Create an alpine container in interactive mode and install python
------------------------------------------------------

* Launch an Ec2 instance.
* Install docker by using below steps.
  
### steps
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker <username>
#exit and relogin
docker info
```
* Create container by using the below command ``docker container -it --name container1 alpine /bin/sh``
* List out the container ``docker container ls``
* Logon into the container ``docker exec -it container1 /bin/sh``
* Install python3 ``apk update && apk add python3 && python3 --version``
![preview](Images/td2.png)
![preview](Images/td1.png)



# Create an ubuntu container with sleep 1d and the login using exec. Install python.
-------------------------------------------------------

* Launch an Ec2 instance.
* Install docker by using below steps.
  
### steps
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker <username>
#exit and relogin
docker info
```
* Create container by using the below command ``docker container -it --name container2 ubuntu /bin/bash``
* List out the container ``docker container ls``
* Logon into the container ``docker exec -it container2 /bin/bash``
* Install python3 ``apt update && apt install python3 && python3 --version``
![preview](Images/td3.png)
![preview](Images/td4.png)


# Create a postgress container with user panoramic and password trekking. Try logging in and show the  database
-----------------------------------------------------

* Launch an Ec2 instance.
* Install docker by using below steps.
  
### steps
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker <username>
#exit and relogin
docker info
```
To create a container 

`` docker container run -d --name psqldb  -P  -e POSTGRES_DB=employees -e POSTGRES_USER=panoromic -e POSTGRES_PASSWORD=trekking postgres``

Login to the container with the databse above mentioned "employees"
 ``psql -U panoromic --password -d employees``

Crate table in the employees database

``CREATE TABLE Persons`` ( PersonID int, LastName varchar(255), FirstName varchar(255) );``

Insert the values into the table

`` Insert into Persons values (1 , 'Gattu' , 'shravani');``

To see the data

``Select * from Persons;``

![preview](Images/td6.png)
![preview](Images/td8.png)


# Creating docker file which runs on Php page on apache server
-------------------------------------------------------
* Launch an Ec2 instance.
* Install docker by using below steps.
  
### steps
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker <username>
#exit and relogin
docker info
```
To create image 
 ``docker build -t php:v3.0 .``
To create a container 
 ``docker container run --name contaninerphp -d -p 30700:80 php:v3.0``

Create file with the below content

``<?php
 phpinfo()
?>``

# Dockerfile
```docker
FROM ubuntu
RUN apt update && apt install apache2 -y
ARG DEBIAN_FRONTEND=noninteractive
RUN apt install php -y
RUN apt install libapache2-mod-php -y
WORKDIR /var/www/html
COPY /php.file /var/www/html/info.php
EXPOSE 80
CMD ["apache2ctl", "-D", "FOREGROUND"]
```
![preview](Images/td11.png)
![preview](Images/td10.png)
![preview](Images/td9.png)

# Docker file for jenkins
------------------------

* Launch an Ec2 instance.
* Install docker by using below steps.
  
### steps
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker <username>
#exit and relogin
docker info
```


```docker
FROM ubuntu
RUN apt update && apt install openjdk-11-jdk -y && apt install curl -y
RUN curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
RUN echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ |  tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
RUN apt update
RUN apt install jenkins -y
CMD /usr/bin/jenkins
USER jenkins
EXPOSE 8080
```

To create image 
 ``docker build -t jenkins:v7.0 .``
To create a container 
 ``docker container run --name cont34 -d -P jenkins:v7.0``

After cfreating the container enter inside the container by using ``docker exec -it cont34 /bin/sh`` 
enter ``cat /var/lib/jenkins/.jenkins/secrets/initialAdminPassword.`` It will display the passwrd.Enter the password and login in the jenkins page.

![preview](Images/td15.png)
![preview](Images/td16.png)
![preview](Images/td12.png)
![preview](Images/td14.png)

# Create mysql and nopcmmerce containers and try to make them work by configuring.
----------------------------------------------------------------------------------
```docker
FROM mcr.microsoft.com/dotnet/sdk:7.0
LABEL author="shravani" organization="sr" project="learning"
ADD https://github.com/nopSolutions/nopCommerce/releases/download/release-4.60.2/nopCommerce_4.60.2_NoSource_linux_x64.zip /nop/nopCommerce_4.60.2_NoSource_linux_x64.zip
WORKDIR /nop
RUN apt update && apt install unzip -y && \
    unzip /nop/nopCommerce_4.60.2_NoSource_linux_x64.zip && \
    mkdir /nop/bin && mkdir /nop/logs
EXPOSE 5000
CMD [ "dotnet", "Nop.Web.dll","--urls", "http://0.0.0.0:5000" ]
```

* Launch an Ec2 instance.
* Install docker by using below steps.
  
### steps
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

sudo usermod -aG docker <username>
#exit and relogin
docker info
```

``docker image build -t nop:latest``
![preview](Images/td19.png)
``docker network create -d bridge nop_bridge``
``docker volume create nop_db``

``docker container run -d --name mysql -e MYSQL_ROOT_PASSWORD=shravani@143 -e MYSQL_DATABASE=employees -e MYSQL_USER=shravani -e MYSQL_PASSWORD=shravani@143 --network nop_bridge -v nop_db:/var/lib/mysql mysql:5.6``

``docker container run -d -P --name nopcommerce -e MYSQL_SERVER=mysql --network nop_bridge nop:latest``
![preview](Images/td20.png)
![preview](Images/td17.png)
we need to start the container again by using ``docker container start nopcommerce``
![preview](Images/td18.png)

Creating docker file which runs on Php page on apache server
------------------------------------------------------------

* Launch an Ec2 instance.
* Install docker by using below steps.
  
### steps
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo usermod -aG docker <username>
#exit and relogin
docker info
```


```docker
FROM ubuntu
RUN apt update && apt install nginx -y
ARG DEBIAN_FRONTEND=noninteractive
RUN apt install php-fpm -y
RUN mkdir /var/www/html
RUN chown -R $root:$root /var/www/html
COPY /sites /etc/nginx/sites-available/html
RUN ln -s /etc/nginx/sites-available/html /etc/nginx/sites-enabled/
RUN unlink /etc/nginx/sites-enabled/default
RUN nginx -t
RUN service nginx reload
COPY /php.file /var/www/html/index.html
COPY /php /var/www/html/info.php
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
```
php
<?php
 phpinfo()
?>
```
```
Sites

server {
    listen 80;
    server_name html www.html;
    root /var/www/html;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```
```
php.file

<html>
  <head>
    <title>html website</title>
  </head>
  <body>
    <h1>Hello World!</h1>

    <p>This is the landing page of <strong>html</strong>.</p>
  </body>
</html>
```

To create image 
 ``docker build -t ngn:1.0 .``
To create a container 
 ``docker container run --name contaninerphp -d -p 30700:80 ngn:1.0``
 ![preview](Images/td22.png)

 Info.php getting error.....









