# NoSQL Databases - MongoDB Hands-on

## 1.	Introduction

This session is intended to help the student to get started with MongoDB, a NoSQL (document-oriented) database management system.

## 2.	Install MongoDB

### 2.1 Install a local MongoDB server

If you don't have Docker you need to install a MongoDB server following [this instructions](https://docs.mongodb.com/manual/installation/). If you have Docker you can just pull and run an MongoDB server image (instructions for installing Docker [here](../docker.md)):

	docker pull mongo
	docker run -it --name=mongo -p 27017:27017 -d mongo

You don't need a shared folder (a volume) this time. 

### 2.2 Connecting with a client: MongoDB shell and MongoDB Compass

MongoDB comes with a shell client. On Docker you can execute it this way:

	docker exec -it mongo bash
	root@813847d78b39:/# mongo

If you don't work over Docker you can run "mongo" directly from a terminal.

We will work on the shell, but it will be convenient to install MongoDB Compass, a GUI. You can dowload it here [here](https://www.mongodb.com/download-center/compass). Using MongoDB Compass is optional. Once you have Compass installed you have to enter this connection URL:
	
	mongodb://localhost:27017

## 3. Selecting the database to work with

From the MongoDB shell, the following will inform you about the current active database:

	>db

Let's create and activate a new database:

 	>use drcavdb

you can get info about the new database this way:

	>db.stats()

If you have MongoDB Compass you can check that the drcavdb database has been created.

## 4. Working with collections

The following creates a new collection with one document:

	>db.photos.insert({
	      "title" : "Photo1",
	      "dateCreated":{"$date":"2020-02-01"}, 
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

    >db.photos.insert({
	      "title" : "Photo2",
	      "dateCreated":{"$date":"2020-02-02"}, 
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
	      "dateCreated":{"$date":"2020-02-03"} 
	}

If you are not using Compass let's try inserting that document with the shell. Notice that MongoDB doesn't care that the document has a different structure.

On the shell you can visualize all the documents this way:

	db.photos.find()

On the shell you can remove all the documents this way:

	db.photos.remove({})

## 5. Querying

On the shell you can query all documents and pretty print them this way:

     > db.photos.find().pretty()

You can find documents with title "Photo2" this way:

     > db.photos.find( { "title": "Photo2" } )

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

	> db.photos.find( { dateCreated: { $gt: Date('2000-06-22') } } )

And regular expressions:

	You can filter by date:

	> db.photos.find({"title": /Photo/})
     


