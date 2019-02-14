# Session 1. SQL and Multimedia Metadata Hands-on (2 hours)

## 1.	Introduction

This session has two goals, on the one hand to have a first contact with a (Relational) Database Management System (RDBMS), particularly MySQL. On the other hand, this session will allow you to practice what you learned about the SQL language during the theory classes.


## 2.	Environment settings

In order to be able to perform the different tasks you should:

- Boot the PC with the Linux image (preferable) or Windows 
- Log in with the user/password that you use for Atenea (or, in case it does not work, with (invitado, invitado) on Linux or (A2S105-??\invitado, without password) on Windows)


## 3.	Accessing the EDCAV’s MySQL server

At edcav.upc.es we have installed an MySQL Community Server that we will use in this lab session. In order to interact with the server execute the MySQL Workbench application: (Linux) Click the launcher (top-left corner of the screen) and select ‘workbench’. (Windows) Start menu, MySQL folder. 

Click the "New Connection" option and specify the connection parameters. We have created 15 databases named edcav1, edcav2, etc. and 15 users named edcav1, edcav2, etc. with privileges over the respective DBs and password ‘edcav’ for all of them. Ask to the professor wich DB you should use. Then specify the proper parameters, e.g.:

![alt text](mysqlworkbench.png "MySQL Workbench")

(NOTE: the MySQL Community Server at edcav.upc.es only can be accessed from UPC IP addresses. In case you want to work from your own computer [ANNEX 1][1] describes some alternatives.)

## 4. How to change your password

Within a query window of the MySQL Workbench (the one that appears after connecting to the server) type the following (replace the X for your DB number) and press the execute icon:

	SET PASSWORD FOR ‘edcavX’ = PASSWORD (‘mypassword’);

## 5. Creating tables, first examples

The first thing you must do is creating the tables that comprise your database. The creation and destruction of tables is done via the SQL CREATE and DROP commands. For example, if we want to create the table:

*users(__username__, password, email UNIQUE)*

We have to execute:

	CREATE TABLE  users(
		username VARCHAR(100) NOT NULL,
		password VARCHAR(100),
		email VARCHAR(100),
		PRIMARY KEY (username),
		UNIQUE (email)
	); ENGINE = INNODB;

Save the command into a file creates.sql.

Now create the table:

*photos(__filename__, title, description, width, height, datetime_taken, latitude, longitude, username NOT NULL)*
- username ->foreign key: users(username)

Into the same file, below the ‘users’ CREATE, copy the following:

	CREATE TABLE  photos (
		filename VARCHAR(100) NOT NULL,
		title VARCHAR(100),
		description VARCHAR(300),
		width INTEGER,
		height INTEGER,
		datetime_taken DATETIME,
		latitude DECIMAL (10,8),
		longitude DECIMAL (10,8),
		username VARCHAR(100) NOT NULL,
		PRIMARY KEY (filename),
		CONSTRAINT photo_fk_1 FOREIGN KEY (username) REFERENCES user(username) ON DELETE CASCADE
	) ENGINE = INNODB;

Note that the order in which CREATEs appear is important because 'projects' references ‘clients’. Usually when you create a DB, you also create a DROPs file to facilitate the deletion of all tables. It can also be created within the same CREATEs file. At the beginning of the file (before the CREATEs) copy the following (the order is important, because MySQL is not going to leave us to delete a
referenced table):

	DROP TABLE IF EXISTS photos;
	DROP TABLE IF EXISTS users;

## 6. Creating a DB, now your turn 	

Now, create yourself the following tables:

*follows(__follower_username__, __following_username__)*
- follower_username ->foreign key: users(username)
- following_username ->foreign key: users(username)

*likes(__username__, __filename__)*
- username ->foreign key: users(username)
- filename ->foreign key: photos(filename)

It is important that you save a file with your CREATEs and DROPs. Name it creates.sql.

## 7. Inserting values

Now your DB has tables, but they still do not contain any row. The SQL command for inserting values is INSERT. For instance:

	SET SQL_SAFE_UPDATES=0;

	DELETE FROM users;
	INSERT INTO users VALUES ("user1", "1234", "user1@gmail.com");
	INSERT INTO users VALUES ("user2", "1234", "user2@gmail.com");
	INSERT INTO users VALUES ("user3", "1234", "user3@gmail.com");

In a new query copy and execute the previous commands. The DELETE command allows us to reexecute again the query without errors. Within the same file, after the previous lines, add the necessary INSERTs and DELETEs to fill with three or four rows all the tables that we created before. You should follow these rules:

- You have to respect the specified table constraints
- Dates have the format: “yyyy-mm-dd”
- Order in which you insert rows matters (you cannot reference a user that still does not exist)

Save the file with the name inserts.sql.

### 7.1 About foreign key errors (errno = 105)

If you find foreign key errors you can get more information with SHOW ENGINE INNODB STATUS; However, you need certain privileges to do that (you have them on your local MySQL but not on the EDCAV server). If you cannot find the way to solve the problem you may ask the
lab teacher (she/he have the enough privileges to execute that query).

### 7.2 Alternative INSERT syntax

	INSERT INTO table_name (column1,column2,column3,...) VALUES (value1,value2,value3,...);


## 8. SQL Queries
Now that you have tables and rows you can execute the SQL query commands you have learned during the theory classes. Specify the SQL statement that will allow you to obtain the following results. Write your answers in a text file that you deliver to the professor.

1. Show all users and their data

	SELECT * FROM employees;

2. Show the photos uploaded on 04/02/2013
3. Show the filename and datetime_taken of photos from user "user1"
4. Show how many photos belong to "user1".
5. For each user show, the username and how many photos does she has.
6. Show the filename and datetime_taken of photos from a user with email “user2@gmail.com”.

## 9.	Delivery

The files creates.sql, inserts.sql and the answers have to be delivered in a single file (.zip or .tar.gz) through the proper section within http://atenea.upc.edu. 


## ANNEX 1.	Alternative setups {#annex1}

### A1.1 (A2S105 PCs + local MySQL server) Accessing a local MySQL server from A2S105 PCs

In case that you cannot access the EDCAV’s server you may connect to a MySQL server running on localhost: DB: edcav, user: edcav and password: edcav.

### A1.2 (your own computer + edcav.upc.es server) Accessing the server through a VPN

The MySQL Community Server at edcav.upc.es only can be accessed from UPC IP addresses. In order to access it from your own laptop you can stablish a VPN following the steps [here](https://telecos.upc.edu/ca/els-serveis/serveis-informatics/acces/connexio-vpn).

(WARNING: It seems that the current information provided by ETSETB is wrong for OSX)

(WARNING: The latest version of MySQL Workbench is not compatible with the course server, you need to download version 5.2.47 or older from [here](https://dev.mysql.com/downloads/workbench/))

### A1.3 (your own computer + local MySQL server) Working with Docker

If you want to work with your personal computer and a local MySQL server, but you don't want to mess up your OS, you may find convenient to work over Docker. You need first to install Docker in your machine. In Ubuntu you can do it this way:

    sudo apt-get update
    wget -qO- https://get.docker.com/ | sh
    sudo usermod -aG docker $(whoami)

It's necessary to LOGOUT to let the usermod command have effect.

Windows and OSX installation procedures can be found [here](https://docs.docker.com/install/).

Once you have Docker up and running you can pull and run an msql-server image:

	docker pull mysql/mysql-server:latest
	docker run --name=drcav1 -d -p 3306:3306 mysql/mysql-server:latest

In order to know which root password was given do:

	docker logs drcav1 

And take notice of the root password. Then execute:

	docker exec -it drcav1 mysql -uroot -p

And type:

	mysql>ALTER USER 'root'@'localhost' IDENTIFIED BY 'edcav';
	mysql>CREATE DATABASE IF NOT EXISTS edcav1;
	mysql>CREATE USER 'edcav1'@'%' IDENTIFIED BY 'edcav';
	mysql>GRANT ALL PRIVILEGES ON edcav1.* to 'edcav1'@'%';
	mysql>exit;

Then you can run MySQL Workbench and connect to the server:

![alt text](mysqlworkbench_docker.png "MySQL Workbench")

(WARNING: Don't try to use the old MySQL Workbench version, e.g. 5.2.47, with the mysql Docker container, it's not going to work.)

