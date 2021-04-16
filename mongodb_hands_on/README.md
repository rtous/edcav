# NoSQL Databases - MongoDB Hands-on

## 1.	Introduction

This session is intended to help the student to get started with MongoDB, a NoSQL (document-oriented) database management system.

## 2. Install MongoDB

### 2.1 Install a local MongoDB server with Docker

*NOTE: If you don't have Docker try installing it (instructions[here](../docker.md)). If you are not able to use Docker you can install a MongoDB server following [this instructions](https://docs.mongodb.com/manual/installation/) and jump to section 3.*

In Ubuntu:
<!--
Let's first setup a local directory to be shared with the Docker container. Let's create a "drcav" directory in your home directory. 

	mkdir $HOME/drcav


* /Users/YOUR_USER_NAME/drcav in Mac
* /home/YOUR_USER_NAME/drcav in Linux 
* C:\Users\YOUR_USER_NAME\drcav in Windows

On Mac and Linux you can check your home directory by typing "echo $HOME" in a terminal. On Windows you can type "echo %cd%" in a terminal.

Let's pull and run a MongoDB Docker container:

	docker run -it --name=mongo -v $HOME/drcav:/drcav -p 27017:27017 -d mongo
-->
Let's pull and run a MongoDB Docker container:

	docker run -it --name=mongo -p 27017:27017 -d mongo

*If you previously launched the container you can stop and remove it this way:

	docker stop mongo
	docker rm mongo*
<!--
On Windows 10, if you are running Docker Desktop, use a typical Windows path, e.g.:

	docker run -it --name=mongo -v C:\Users\YOUR_USER_NAME\drcav:/drcav -p 27017:27017 -d mongo
-->

<!--
On Windows 10 Home (Docker Toolbox):

	docker run -it --name=mongo -v //c/Users/YOUR_USER_NAME/drcav:/drcav -p 27017:27017 -d mongo
-->

Check if the container is running this way:

	docker ps -a

If you enter a wrong command by mistake you can delete the container this way:

	docker stop mongo
	docker rm mongo

## 3 Connecting with a client: MongoDB shell and MongoDB Compass

MongoDB comes with a shell client. On Docker you can execute it this way:

	docker exec -it mongo mongo

<!--
	docker exec -it mongo bash	

Then you should see the typical Docker prompt, something like this:

	root@813847d78b39:/# 

Let's now run the MongoDB shell:

	root@813847d78b39:/# mongo

-->

*NOTE: If you don't work over Docker you can run "mongo" directly from a terminal.*

We will work on the shell, but it will be convenient to install MongoDB Compass, a GUI. You can dowload it here [here](https://www.mongodb.com/download-center/compass). Using MongoDB Compass is optional. Once you have Compass installed you have to enter this connection URL:
	
	mongodb://localhost:27017


## 4. First commands 

From the MongoDB shell, the following will inform you about the current active database:

	> db

Let's create and activate a new database:

 	>use drcavdb

you can get info about the new database this way:

	> db.stats()

If you have MongoDB Compass you can check that the drcavdb database has been created.

## 5. Working with collections

The following creates a new collection with one document:

	> db.photos.insert({
	      "title" : "Photo1",
	      "dateCreated": ISODate("2020-02-01T00:00:00Z"), 
	      "coord" : {
	         "lat" : -73.9557413,
	         "long" : 40.7720266,
	         "height" : 1439.2
	      },
	      "comments" : [
	         {
	            "author" : "User1",
	            "comment" : "Nice photo"
	         },
	         {
	            "author" : "User2",
	            "comment" : "Like this one"
	         }
	      ]
	   }
	)

Let's add another document:

    > db.photos.insert({
	      "title" : "Photo2",
	      "dateCreated": ISODate("2020-02-02T00:00:00Z"), 
	      "coord" : {
	         "lat" : -73.9557413,
	         "long" : 40.7720266,
	         "height" : 1439.2
	      },
	      "comments" : [
	         {
	            "author" : "User1",
	            "comment" : "Nice"
	         }
	      ]
	   }
	)

If you have MongoDB Compass you can use it to visualize the inserted documents. You an also insert documents with Compass. Click the ADD DATA button and try the Add Document functionality adding this one:

	{
      "title" : "Photo3",
      "dateCreated":{"$date":"2020-02-03"},
      "coord" : {
         "lat" : -73.9557413,
         "long" : 40.7720266,
         "height" : 1439.2
      },
      "comments" : [
         {
            "author" : "User1",
            "comment" : "Nice"
         }
      ]
	}

Notice that dates ("dateCreated" field) work slightly different on Compass. 

Now try to add a document with a different structure;

    {
	      "title" : "Photo4"
	}


Notice that MongoDB doesn't care that the document has a different structure.

On the shell you can visualize all the documents this way:

	> db.photos.find()

On the shell you can remove all the documents this way:

	> db.photos.remove({})

## 6. Querying

On the shell you can query all documents and pretty print them this way:

     >db.photos.find().pretty()

You can find documents with title "Photo2" this way:

     >db.photos.find( { "title": "Photo2" } )

On Compass, you can do the same by typing { "title": "Photo2" } into the FILTER field.

You can apply boolean conditions:

	> db.photos.find(
	   {
	      $or: [
	         {"title": "Photo1"}, {"title": "Photo2"}
	      ]
	   }
	)

You can filter by date:

	> db.photos.find( { dateCreated: { $gt: ISODate('2000-06-22') } } )

And regular expressions:

	You can filter by date:

	> db.photos.find({"title": /Photo/})

Exit the MongoDB shell

	> exit

## 7. Accessing MongoDB from Python code (OPTIONAL)

Let's first install the required dependencies:

	root@813847d78b39:/# apt-get update
	root@813847d78b39:/# apt-get install -y python
	root@813847d78b39:/# apt-get install -y python-pip
	root@813847d78b39:/# pip install pymongo

Let's now create the program that will send requests to the MongoDB server (a client program). You can do it from the terminal (from the drcav directory):
	
	touch MyMongoClient.py

Now edit the file and copy there the following code:

	import pymongo

	myclient = pymongo.MongoClient("mongodb://localhost:27017/")
	mydb = myclient["drcavdb"]
	print("You are successfully connected to MongoDB!")

Let's extend our code to query our "photos" collection:

	import pymongo

	myclient = pymongo.MongoClient("mongodb://localhost:27017/")
	mydb = myclient["drcavdb"]
	mycol = mydb["photos"]
	myquery = { "title": "Photo1" }
	mydoc = mycol.find(myquery)
	for x in mydoc:
		print(x)

## 13.	Delivery

Deliver a text file with the output of the different commands (or some screenshots within a .pdf file) through the proper section within http://atenea.upc.edu. 

