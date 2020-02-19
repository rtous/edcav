# Web API Hands-on (API of the NASA Image and Video Library)

## 1.	Introduction

A web service is a generic term for a software function that is accessible through HTTP. Nowadays the paradigm has evolved to a simplified form, usually called web APIs (RESTful APIs if they follow the REST architectural style). Web APIs normally rely only in plain HTTP (plus JSON for serializing the messages). 

The goal of this session is to learn different ways to access a web API and to know how to obtain multimedia metadata from it.  

## 2	Setup

In order to ensure that all of us have the same development environent we will work over Docker. 

### 2.1 Install Docker 

If you don't have Docker already installed you will need to install it. 

#### Ubuntu

In Ubuntu you install Docker this way:

    sudo apt-get update
    wget -qO- https://get.docker.com/ | sh
    sudo usermod -aG docker $(whoami)

It's necessary to LOGOUT to let the usermod command have effect.

#### Mac

Mac installation procedure can be found [here](https://docs.docker.com/docker-for-mac/install/). 

#### Windows

Installing on Windows may not be so easy. If you have Windows 10 Pro, Enterprise, or Education you can follow the instructions [here](https://docs.docker.com/docker-for-windows/install/). 

However, if you have Windows 10 Home you can only install [Docker Toolbox](https://docs.docker.com/toolbox/toolbox_install_windows/). 

*Additional information on how to use Docker can be found [here](../docker.md).*

### 2.2 Create a working directory

Open a terminal and create a directory in your laptop for DRCAV. For instance, from your home directory (this should work on any platform):

	mkdir drcav

You need to write down the absolute path of this directory. On Linux and macOS you can know it with "pwd", on Windows you can type "echo %cd%". We will tell Docker to make this directory accessible from the container that we are going to launch. 

*NOTE: If you are using Docker Toolbox on Windows 10 please create the directory within your home directory (C:\Users\YOUR_USER_NAME\drcav)*

### 2.3 Launch an Ubuntu container

Let's lauch now an Ubuntu container (replace YOUR_PATH with the local path to the drcav directory):

	docker run -it --name drcav -v YOUR_PATH/drcav:/drcav ubuntu bash

On Windows 10 Home (Docker Toolbox):

	docker run -it --name drcav -v //c/Users/YOUR_USER_NAME/drcav:/drcav ubuntu bash

You should see something like this:

	root@813847d78b39:/#

Which means that you are now within an Ubuntu container. If you type

	root@813847d78b39:/# cd /drcav

You should see the contents of the drcav directory, which you can also access from outside Docker. Check that the shared folder works before moving on.

*NOTE: If you are running Docker Toolbox on Windows 10 and you have problems with the shared folder let's open VirtualBox (Docker Toolbox runs over it). Go to configuration/shared folders. You should see a shared folder "c/Users". If not, create this shared folder (persistent, auto-mount) and link it to your home directory*

### 2.4 Install cURL and Python

	root@813847d78b39:/# apt-get update
	root@813847d78b39:/# apt-get install -y curl
	root@813847d78b39:/# apt-get install -y python
	root@813847d78b39:/# apt-get install -y python-pip

Let's leave the Ubuntu container for now:

	root@813847d78b39:/# exit

From the terminal, you can check the status of the containers with

	docker ps -a

You can stop or start the container with (you can do it to try it):

	docker stop drcav
	docker start drcav

You can keep working with the container (if it's running) this way:

	docker exec -it drcav bash

If necessary, you can delete the container with "docker rm drcav" (don't do it now).

### 3 NASA APIs

For this tutorial we will use the [NASA APIs](https://api.nasa.gov/). They provide access to NASA data, including multimedia. The APIs are free and some of them are even accessible without an API Key (though it's very easy to obtain one).
<p align="center"><img src="nasa.png" alt="nasa logo" width="200"/></p>

Specifically we will access the API of the [NASA Image and Video Library](https://images.nasa.gov/). Take a look to the [NASA Image and Video Library API documentation](https://images.nasa.gov/docs/images.nasa.gov_api_docs.pdf) to see how a web API specification looks like.

### 4 Checking the API with the browser

Type the following URL into your browser (e.g. Chrome):

	https://images-api.nasa.gov/search?q=apollo%2011&description=moon%20landing&media_type=image

The API will return a large JSON file with metadata. Going back to your initial request, observe how the query parameters are passed trough the URL (%20 is used to insert a white space). Try changing any of the parameters and sending the request again. For example:

	https://images-api.nasa.gov/search?q=barcelona&media_type=image

Now copy any of the image URLs found within the response JSON file (e.g. https://images-assets.nasa.gov/image/iss014e10547/iss014e10547~thumb.jpg
) and paste it into another browser tab to display it.

### 5 Checking the API with Postman (optional)

Install [Postman](https://www.postman.com/downloads/) and create a request with the following GET command:

	https://images-api.nasa.gov/search?q=barcelona&media_type=image

Take a look to the JSON response to get an idea of the data structre.

### 6 Checking the API with the terminal

Let's go back into our Ubuntu Docker container and type the following:

	curl -G https://images-api.nasa.gov/search --data-urlencode "q=barcelona" --data-urlencode "media_type=image"

The result is difficult to read, you can improve that this way:

	curl -G https://images-api.nasa.gov/search --data-urlencode "q=barcelona" --data-urlencode "media_type=image" | python -m json.tool

Now you can see the JSON structure. There's a field "href" that points to another JSON, let's check it:

	curl -G https://images-assets.nasa.gov/image/iss014e10547/collection.json | python -m json.tool

The resulting JSON will let you obtain different versions of the image.


### 7 Accessing the API from a Python program

If you are working with a popular web API, you will probably find a Python library that hides all the complexity and lets you interact with the API easily. However, sometimes a Python library is not available or it is not properly updated. Here we will learn how to interact with a web API directly, without using any specific library. 

We will use the [requests library](https://requests.readthedocs.io/en/master/). You need to install it this way:

	pip install requests

Let's now create the program that will send requests to the API (a client program). You can do it from the terminal (from the drcav directory):

	touch APIClient.py

Now edit the file and copy there the following code:

	import requests

	p = {'q': 'barcelona', 'media_type': 'image'}
	r = requests.get('https://images-api.nasa.gov/search', params=p)
	print(r.json())

To run the program in the terminal just type:

	python APIClient.py

You will see the result but it will be difficult to read. To obtain the JSON pretty printed let's change you program this way and run it again:

	import requests
	from pprint import pprint

	p = {'q': 'barcelona', 'media_type': 'image'}
	r = requests.get('https://images-api.nasa.gov/search', params=p)
	pprint(r.json())

Printing all the resulting JSON is ok, but you may probably want to access specific data. Working with JSON in Python is very easy, as illustrated by the following example: 

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

As the JSON contains the link to the jpg file, we can easily download it this way: 

	import os
	[...]
		for link in item['links']:
			print("\t\t"+link['href'])
			myfile = requests.get(link['href'])
			open(os.path.basename(link['href']), 'wb').write(myfile.content)



