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

	>db.restcol.insert({
	      "address" : {
	         "street" : "2 Avenue",
	         "zipcode" : "10075",
	         "building" : "1480",
	         "coord" : [ -73.9557413, 40.7720266 ]
	      },
	      "borough" : "Manhattan",
	      "cuisine" : "Italian",
	      "grades" : [
	         {
	            "date" : ISODate("2014-10-01T00:00:00Z"),
	            "grade" : "A",
	            "score" : 11
	         },
	         {
	            "date" : ISODate("2014-01-16T00:00:00Z"),
	            "grade" : "B",
	            "score" : 17
	         }
	      ],
	      "name" : "Vella",
	      "restaurant_id" : "41704620"
	   }
	)

Let's add another document:

    >db.restcol.insert(
   {
      "address" : {
         "street" : "Sor Eulalia dAnzizu, 45",
         "zipcode" : "08034",
      },
      "borough" : "Pedralbes",
      "cuisine" : "Spanish",
      "grades" : [
         {
            "date" : ISODate("2014-10-01T00:00:00Z"),
            "grade" : "A",
            "score" : 11
         },
         {
            "date" : ISODate("2014-01-16T00:00:00Z"),
            "grade" : "B",
            "score" : 17
         }
      ],
      "name" : "Casa Cantabria",
      "restaurant_id" : "10000"
   }
)

If you have MongoDB Compass you can use it to visualize the inserted documents.

## 5. Querying

     > db.restcol.find()
     > db.restcol.find().pretty()
     > db.restcol.find( { "borough": "Manhattan" } )
     > db.restcol.find( { "address.zipcode": "10075" } )
     > db.restcol.find( { "cuisine": "Italian", "address.zipcode": "10075" } )
     > db.restcol.find( { "cuisine": "Spanish" } )


     > db.restcol.find(
	   {
	      $or: [
	         {"cuisine": "Italian"}, {"cuisine": "Spanish"}
	      ]
	   }
		).pretty()

## 6. Updating and removing

     > db.restcol.update(
		    { "name" : "Casa Cantabria" },
		    {
		      $set: { "cuisine": "Cantabric" },
		      $currentDate: { "lastModified": true }
		    }
		)
    
    >db.restcol.remove( { "borough": "Manhattan" } )


