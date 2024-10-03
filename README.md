# A Step-by-Step Guide to Deploying a LEMP Stack on AWS EC2

What is a LEMP Stack?
A LEMP stack is a group of software tools used to develop and deploy web applications. Each letter in "LEMP" represents a different component that helps deliver dynamic websites and web applications. These components work together to serve your web pages:

L: Linux – The operating system that acts as the base layer.
E: Nginx (pronounced "Engine X") – The web server that handles HTTP requests.
M: MySQL (or MariaDB) – The database that stores the data.
P: PHP – The programming language that processes the dynamic content.

# How the LEMP Stack Works:

Linux is the foundation of the LEMP stack. It’s an open-source operating system that is known for its stability and security. All other components run on top of Linux.

Nginx is the web server that receives requests from users (like opening a website in their browser). It processes those requests and sends the appropriate response back to the user’s browser. Nginx is known for its speed and efficiency.

MySQL (or MariaDB) is the database system that stores all the dynamic data on your website, such as user information, posts, and other structured data. When a user requests information, PHP retrieves it from the database.

PHP is the scripting language that generates dynamic content by processing data from the database. It works with Nginx to serve dynamic web pages instead of static ones.

# LAUNCHING MY EC2 INSTANCE 
I started with launching  my ec2 instance on aws 

![image](https://github.com/user-attachments/assets/5571069a-92d9-496a-aca7-381314b7e40f)

# Connecting to EC2 Instance

I launched Git Bash and ran the following command:
` ssh -i <Your-private-key.pem> ubuntu@<EC2-Public-IP-address> `
![image](https://github.com/user-attachments/assets/87607c71-8d78-466b-a45d-543689414944)


# Installing Nginx
To install Nginx i ran the following command 
`
sudo apt update
sudo apt install nginx
sudo systemctl status nginx
`
i ensured that i opened TCP port 80 in EC2 Security Group settings to allow web traffic.
![image](https://github.com/user-attachments/assets/43155c16-c6b0-43ea-a607-d9b00476077e)

# Configuring Nginx
i checked if Nginx is accessible usinf this command 
`curl http://localhost:80`

i visited http://<Public-IP-Address>:80 to verify that my Nginx is working properlywell 
![image](https://github.com/user-attachments/assets/cc1ec33f-bab3-4e43-9c1e-bce9901107c1)

# Installing MySQL
i ran the following command to install Mysql 
`
sudo apt install mysql-server
sudo mysql
`
![image](https://github.com/user-attachments/assets/c53fcb5b-db6c-4a41-b10a-be3ba7ab6c0f)

I ran the following command to set root passwords
` ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1'; `
![image](https://github.com/user-attachments/assets/59749c1b-c962-49a8-8918-bb87efc7ab2f)

# Securing MySQL

i ran this security script to sesure mysql 
` sudo mysql_secure_installation `

i login into mysql to test using this command 
` sudo mysql -p `

![image](https://github.com/user-attachments/assets/9752c2ac-daf7-4b29-8439-e5a5237f4241)


# Installing PHP 
i used this command to install php 
` sudo apt install php-fpm php-mysql ` 
![image](https://github.com/user-attachments/assets/88d699d3-fcc9-4fc0-8d90-407951407bc3)

# Configuring Nginx to Use PHP Processor

i created the root web directory for my project:
`
sudo mkdir /var/www/projectLEMP
sudo chown -R $USER:$USER /var/www/projectLEMP
`
Create and edit the configuration file:
` sudo nano /etc/nginx/sites-available/projectLEMP `
i pasted the configuration inside my nano editor 


![image](https://github.com/user-attachments/assets/7ee2833a-1c1b-48b8-8566-01ad007c183f)

i Activated and tested the configuration using the following commands:
`
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
sudo nginx -t
sudo unlink /etc/nginx/sites-enabled/default
sudo systemctl reload nginx
`

![image](https://github.com/user-attachments/assets/c14de8cc-0897-4552-b08d-970b35d39c5b)

then i ran this command 
`
sudo echo 'Hello LEMP from hostname ' $(TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600") && curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP ' $(TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600") && curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
`
![image](https://github.com/user-attachments/assets/aebafdba-76a9-46d7-a0e1-095fc8c687ca)

# Testing PHP with Nginx

i created a PHP info file using the command below 

` sudo nano /var/www/projectLEMP/info.php `
I added the following code inside my nano file 
`<?php
phpinfo();
?>`
![image](https://github.com/user-attachments/assets/e67b515e-5719-4f16-a725-3164af2bb04b)

I accessed http://<Public-IP-Address>/info.php in my browser.

![image](https://github.com/user-attachments/assets/627946ae-1bfb-4a1c-bbe1-0e1f2c1c8a3f)

# Creating and Accessing MySQL Database
To do this, i Loged in to MySQL and create a database and user using the following command 
`
sudo mysql -p
CREATE DATABASE example_database;
CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
GRANT ALL ON example_database.* TO 'example_user'@'%';
exit
`
![image](https://github.com/user-attachments/assets/f9b2546b-3666-40e8-9c94-2d5dda8b0e6e)

i loged in as the new user and create a table:
`
mysql -u example_user -p
CREATE TABLE example_database.todo_list (
    item_id INT AUTO_INCREMENT,
    content VARCHAR(255),
    PRIMARY KEY(item_id)
);
`
i Inserted data into the table:
`
INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
INSERT INTO example_database.todo_list (content) VALUES ("My second important item");
`
![image](https://github.com/user-attachments/assets/e8c93a11-ecd0-4753-9c50-afa7d2d697a4)



![image](https://github.com/user-attachments/assets/e6826fa6-3d71-48ee-b95f-94862896f132)


![image](https://github.com/user-attachments/assets/b5f2cb36-a14e-4b70-9377-c03a72976314)


You can now open `http://<Your-EC2-Public-IP>/todo_list.php` in your browser to confirm that your "TODO" list is displayed properly.

## 11. Final Check

Visit `http://<Your-EC2-Public-IP>/todo_list.php` to see the data fetched from the MySQL database.

![WhatsApp Image 2024-10-03 at 21 30 45_18b5c079](https://github.com/user-attachments/assets/3e4c5839-f7d0-40e3-b87d-7f8b80b950c3)
