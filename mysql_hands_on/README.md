# SQL Hands-on 

## 1.	Introduction

This hands-on tutorial provides an overview of MySQL, a (Relational) Database Management Systems (RDBMS). 

## 2.	Setup

### 2.1 Install Docker

If you want to work with your personal computer and a local MongoDB server, but you don't want to mess up your OS, you may find convenient to work over Docker. If you don't have Docker already installed follow the instructions [here](../docker.md).

### 2.2 Install a local MySQL Community Server

Once you have Docker up and running you can pull and run a MySQL image:

	docker run --name=mysql01 -d -p 3306:3306 mysql/mysql-server:latest

Wait some seconds and then type_

	docker logs mysql01

Write down the GENERATED ROOT PASSWORD and run the mysql console:

	docker exec -it mysql01 mysql -u root -p

Change the root password:
	
	>ALTER USER 'root'@'localhost' IDENTIFIED BY 'edcav';

## 3. Creating a new database and a new user

Create a new database:

	>CREATE DATABASE IF NOT EXISTS edcav1;

Create a new user and give him access to the new database

	>CREATE USER 'edcav1'@'%' IDENTIFIED BY 'edcav';
	>GRANT ALL PRIVILEGES ON edcav1.* to 'edcav1'@'%';

Set the new database as the target database:

	>use edcav1;

You can see the tables this way (now empty):

	>show tables;

## 4. Creating tables

Let's create a table users:

	>CREATE TABLE  users(       
		username VARCHAR(100),  
		email VARCHAR(100),     
		PRIMARY KEY (username),  
		UNIQUE (email)          
	);

Let's create a table photos:

	>CREATE TABLE  photos (
		filename VARCHAR(100),
		datetime_taken DATETIME,
		username VARCHAR(100) NOT NULL,
		PRIMARY KEY (filename),
		CONSTRAINT photo_fk_1 FOREIGN KEY (username) REFERENCES users(username) ON DELETE CASCADE
	);

You can check the existing tables this way:

	show tables;

## 5. Deleting tables

You can delete your tables this way:

	>DROP TABLE IF EXISTS photos;
	>DROP TABLE IF EXISTS users;

## 6. Inserting values

	>INSERT INTO users VALUES ("user1", "user1@gmail.com");
	>INSERT INTO users VALUES ("user2", "user2@gmail.com");
	>INSERT INTO users VALUES ("user3", "user3@gmail.com");

	>INSERT INTO photos VALUES ("photo1.jpg", '2019-02-02 10:10:10', "user1");
	>INSERT INTO photos VALUES ("photo2.jpg", '2019-02-02 10:10:10', "user1");
	>INSERT INTO photos VALUES ("photo3.jpg", '2019-02-02 10:10:10', "user2");

## 7. Deleting values

	>SET SQL_SAFE_UPDATES=0;

	>DELETE FROM photos;
	>DELETE FROM users;

## 8. Basic SQL queries

#1. Show all users and their data

	>SELECT * FROM users;

2. (projection) Show the username and email of all users

	>SELECT username, email FROM users;

3. (selection) Show the photos with datetime_taken > '2018-02-02 10:10:10' and '2020-02-02 10:10:10' < 200

	>SELECT * FROM photos WHERE datetime_taken > '2018-02-02 10:10:10' AND datetime_taken < '2020-02-02 10:10:10';

4. (cartesian product) Show all the possible pairs of (photo, user) regardless of the values of the username field (show only the photo's filename and the user's username).

	SELECT p.filename, u.email FROM photos p, users u;

5. (join) For all photos, show their filename and the email address of the user that created them.

	SELECT p.filename, u.email FROM photos p, users u WHERE p.username = u.username;

6. (aggregate functions) Show how many photos belong to "user1".

	SELECT count(*) FROM photos WHERE username = "user1";

7. (grouping) For each user, show the username and how many photos does she has.

	SELECT username, count(*) FROM photos GROUP BY username;

## 9. Finishing

	>exit;

	docker stop mysql01

## 10. Troubleshooting

docker start mysql01
docker exec -it mysql01 mysql -uroot -p

