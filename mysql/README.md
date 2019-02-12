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

(NOTE: If, for any reason, you cannot access this server this way, ANNEX 1 describes some alternatives.)


## 4. How to change your password

Within a query window of the MySQL Workbench (the one that appears after connecting to the server) type the following (replace the X for your DB number) and press the execute icon:

	SET PASSWORD FOR ‘edcavX’ = PASSWORD (‘mypassword’);

## 5. Creating tables, first examples

The first thing you must do is creating the tables that comprise your database. The creation and destruction of tables is done via the SQL CREATE and DROP commands. For example, if we want to create the table:

*clients(__id_cli__, name_cli, nif, adress, city, phone)*

We have to execute:

	CREATE TABLE clients(
		id_cli INTEGER,
		name_cli CHAR(30) NOT NULL,
		nif CHAR (9),
		adress CHAR(30),
		city CHAR(20),
		phone INTEGER,
		PRIMARY KEY (id_cli),
		UNIQUE (nif)
	) ENGINE = INNODB;

Save the command into a file creates.sql.

Now create the table:

*projects(__id_proj__, name_proj, price, date_start, date_prev_end date_end, id_client)*

Into the same file, below the ‘clients’ CREATE, copy the following:

	CREATE TABLE projects(
		id_proj INTEGER,
		name_proj CHAR(20),
		price REAL,
		date_start DATE,
		date_prev_end DATE,
		date_end DATE,
		id_client INTEGER,
		PRIMARY KEY (id_proj),
		FOREIGN KEY (id_client) REFERENCES clients (id_cli)
	) ENGINE = INNODB;

Note that the order in which CREATEs appear is important because 'projects' references ‘clients’. Usually when you create a DB, you also create a DROPs file to facilitate the deletion of all tables. It can also be created within the same CREATEs file. At the beginning of the file (before the CREATEs) copy the following (the order is important, because MySQL is not going to leave us to delete a
referenced table):

	DROP TABLE IF EXISTS projects;
	DROP TABLE IF EXISTS clients;

## 6. Creating a DB, now your turn 	

Now, create yourself the following tables:

*departments(__name_dpt__, __city_dpt__, phone)*

*employees(__id_empl__, name_empl, surname_empl, sou, name_dpt, city_dpt, num_proj)*
- name_dpt, city_dpt ->foreign key: departments(name_dpt, city_dpt)
- num_proj ->foreign key: projects (id_proj)

It is important that you save a file with your CREATEs and DROPs. Name it creates.sql.

## ANNEX 1.	Accessing the EDCAV’s MySQL server from your own computer

The MySQL Community Server at edcav.upc.es only can be accessed from UPC IP addresses. In order to access it from your own laptop you can stablish a VPN following the steps [here](https://telecos.upc.edu/ca/els-serveis/serveis-informatics/acces/connexio-vpn).



