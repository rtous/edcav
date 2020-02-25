# NoSQL Databases - MongoDB Hands-on

## 1.	Introduction

This session is intended to help the student to get started with MongoDB, a NoSQL (document-oriented) database management system.

## 2.	Install MongoDB

### 2.1 Install Docker

If you want to work with your personal computer and a local MongoDB server, but you don't want to mess up your OS, you may find convenient to work over Docker. If you don't have Docker already installed follow the instructions [here](../docker.md).

### 2.2 Install a local MongoDB server

If you don't have Docker you need to install a MongoDB server following [this instructions](https://docs.mongodb.com/manual/installation/). If you have Docker you can just pull and run an MongoDB server image:

	docker pull mongo
	docker run -it --name=mongo -p 27017:27017 -d mongo

You don't need a shared folder (a volume) this time. 

### 2.3 Install a local MongoDB Compass (OPTIONAL)

A convenient way to interact with your MongoDB server is MongoDB Compass, a GUI. You can dowload it here [here](https://www.mongodb.com/download-center/compass).

However, if you don't want to install Compass you can directly use the MongoDB shell this way:

	docker exec -it mongo bash
	root@813847d78b39:/# mongo

## 3. Selecting the database to work with

The following will inform you about the current active database:

	>db

Let's create and activate a new database:

 	>use drcavdb

you can get info about the new database this way:

	>db.stats()

## 4. Working with collections

The following creates a new collection:

	>db.restcol.insert(
   {
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


