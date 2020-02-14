# Web API Hands-on

## 1.	Introduction

A web service is a generic term for a software function that is accessible through HTTP. Nowadays the paradigm has evolved to a simplified form, usually called web APIs (RESTful APIs if they follow the REST architectural style). Web APIs normally rely only in plain HTTP (plus JSON for serializing the messages). 

The goal of this session is to learn different ways to access a web API and to know how to obtain multimedia metadata from it.  

## 2.	Setup

This hands-on tutorial basically requires a Python installation (any version), but it also uses some typical command-line tools such as cURL. This may not be a problem (specially for macOS and Linux users) but **we are going to work over Docker** to avoid messing with our laptops. 

### 2.1 Install Docker

If you don't have Docker already installed follow the instructions [here](../docker.md). 

### 2.2 Create a working directory

Open a terminal and create a directory in your laptop for DRCAV. For instance, from your home directory (this should work on any platform):

	mkdir drcav

You need to write down the absolute path of this directory. On Linux and macOS you can know it with "pwd", on Windows you can type "echo %cd%". We will tell Docker to make this directory accessible from the container that we are going to launch. 

### 2.3 Launch an Ubuntu container

Let's lauch now an Ubuntu container (replace YOUR_PATH with the local path to the drcav directory):

	docker run -it --name drcav -v YOUR_PATH/drcav:/drcav ubuntu bash

You should see something like this:

	root@813847d78b39:/#

Which means that you are now within an Ubuntu container. If you type

	cd /drcav

You should see the contents of the drcav directory, which you can also access from outside Docker. 

### 2.2 Install cURL and Python

	apt-get update
	apt-get install -y curl
	apt-get install -y python
	apt-get install -y python-pip

### 3 Checking the API with the browser

Type the following URL into your browser (e.g. Chrome):

	https://images-api.nasa.gov/search?q=apollo%2011&description=moon%20landing&media_type=image

The API will return a large JSON file with metadata. Going back to your initial request, observe how the query parameters are passed trough the URL (%20 is used to insert a white space). Try changing any of the parameters and sending the request again. For example:

	https://images-api.nasa.gov/search?q=barcelona&media_type=image

Now copy any of the image URLs found within the response JSON file (e.g. https://images-assets.nasa.gov/image/iss014e10547/iss014e10547~thumb.jpg
) and paste it into another browser tab to display it.

### 3 Checking the API with the terminal

From the Ubuntu terminal type the following:

	curl -G https://images-api.nasa.gov/search --data-urlencode "q=barcelona" --data-urlencode "media_type=image"

The result is difficult to read, you can improve that this way:

	curl -G https://images-api.nasa.gov/search --data-urlencode "q=barcelona" --data-urlencode "media_type=image" | python -m json.tool

Now you can see the JSON structure. There's a field "href" that points to another JSON, let's check it:

	curl -G https://images-assets.nasa.gov/image/iss014e10547/collection.json | python -m json.tool

The resulting JSON will let you obtain different versions of the image.


### 3 Accessing the API from a Python program

If you are working with a popular web API, you will probably find a Python library that hides all the complexity and lets you interact with the API easily. However, sometimes a Python library is not available or it is not properly updated. Here we will learn how to interact with a web API directly, without using any specific library. 

#### Setting up the Python environment

We will use the [requests library](https://requests.readthedocs.io/en/master/). You need to install it this way:

	pip install requests


#### Code

From the /drcav directory:

	touch APIClient.py

Within a terminal create a new empty text file called APIClient.py. One easy way to do it is:

	touch APIClient.py

Now edit the file with your preferred text editor (of course you can use a Python IDE too, but it's not necessary). 


	import requests

	p = {'q': 'barcelona', 'media_type': 'image'}
	r = requests.get('https://images-api.nasa.gov/search', params=p)
	print(r.json())

Or, to see it pretty printed:

	import requests
	from pprint import pprint

	p = {'q': 'barcelona', 'media_type': 'image'}
	r = requests.get('https://images-api.nasa.gov/search', params=p)
	pprint(r.json())

In order to access the different JSON fields you:

	import requests
	from pprint import pprint

	p = {'q': 'barcelona', 'media_type': 'image'}
	r = requests.get('https://images-api.nasa.gov/search', params=p)

	collection = r.json()['collection']
	print("collection with href="+collection['href'])
	print("collection's items: ")
	for item in collection['items']:
		print("\titem with href="+item['href'])	
		print("\titem's data: ")
		for datum in item['data']:
			print("\t\tdate_created="+datum['date_created'])
			print("\t\ttitle="+datum['title'])
		print("\titem's links: ")
		for link in item['links']:
			print("\t\t"+link['href'])





------------------------
	https://api.nasa.gov/planetary/apod?api_key=JYthHHrcdCrRG9cU0Pfsxe3n2hJJsFwoYEBrHONo

	- pick one image: https://apod.nasa.gov/apod/image/2002/trifid_spitzerR1024.jpg

https://images.nasa.gov/docs/images.nasa.gov_api_docs.pdf



As 

SCIENCE

https://api.nasa.gov/
	- API Key: JYthHHrcdCrRG9cU0Pfsxe3n2hJJsFwoYEBrHONo



With curl:

curl -G https://images-api.nasa.gov/search --data-urlencode "q=apollo 11" --data-urlencode "description=moon landing" --data-urlencode "media_type=image"

curl -G https://images-api.nasa.gov/search --data-urlencode "q=apollo 11" --data-urlencode "description=moon landing" --data-urlencode "media_type=image" | python -m json.tool




