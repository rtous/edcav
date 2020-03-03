# Session 2. Big Data Analytics with Spark (2 hours)

## 1.	Introduction

This session is intended to help the student to get started with Apache Spark, a powerful open source framework to process big data with high speed and easy to use.


## 2.	Environment settings

The setup required to be able to perform the diferent exercises are:

1. Java 6 or higher
2. Python 2.7 or higher
3. Python's numpy package installed

On the lab's PCs all these requirements are already satisfied. To work on the lab's PCs you should:

- Boot the PC with the Linux image (preferable) or Windows 
- Log in with the user/password that you use for Atenea (or, in case it does not work, with (invitado, invitado) on Linux or (A2S105-??\invitado, without password) on Windows)

The following sections assume that you are working on the lab's PC's. If you prefer to work with your laptop let's take a look to ANNEX 1.

## 3.	Install Spark

First of all, we need to download the Spark environment. To do that, we can just execute the following command:

	$ wget https://archive.apache.org/dist/spark/spark-2.2.1/spark-2.2.1-bin-hadoop2.7.tgz

Alternatively (if you want a different version or you are a Windows user) you can go to https://spark.apache.org/ and download the files. In all of this hands-on we will work with Spark v2.2.1.  Important: you have to download one of pre-built version in "Choose a package type" section (for instance we tested this hands-on with spark-2.2.1-bin-hadoop2.7).

Once we have the tarball file, we need to uncompress it:

	$ tar -xvzf spark-2.2.1-bin-hadoop2.7.tgz

Then, to avoid a problem binding to localhost, execute the following:

	echo "SPARK_LOCAL_IP=127.0.0.1" > spark-2.2.1-bin-hadoop2.7/conf/spark-env.sh

Let’s now execute the interactive Python shell:

	$ spark-2.2.1-bin-hadoop2.7/bin/pyspark

You should see this:

	Welcome to
	      ____              __
	     / __/__  ___ _____/ /__
	    _\ \/ _ \/ _ `/ __/  '_/
	   /__ / .__/\_,_/_/ /_/\_\   version 2.2.1
	      /_/

	Using Python version 2.7.10 (default, Jul 15 2017 17:16:57)
	SparkSession available as 'spark'.
	>>> 

You can exit the Python shell just typing:

	>>>quit()

## 3.1 Troubleshooting

If everything went fine skip this subsection. 

If, during startup, it reports a problem binding to localhost check that this file:

	cat spark-2.2.1-bin-hadoop2.7/conf/spark-env.sh

Contains this option:

	SPARK_LOCAL_IP=127.0.0.1

Otherwise, let's copy the template file and add "SPARK_LOCAL_IP=127.0.0.1" somewhere.

	cp spark-2.2.1-bin-hadoop2.7/conf/spark-env.sh.template spark-2.2.1-bin-hadoop2.7/conf/spark-env.sh
 
## 4.	Example “word count” application

Download example1.txt:

 	(you may find convenient to open another terminal window)

	$ wget https://raw.githubusercontent.com/rtous/edcav/master/spark/example1.txt

which has the following content (accents removed to avoid encoding problems):

	En un lugar de la Mancha, de cuyo nombre no quiero acordarme,
	No ha mucho tiempo que vivía un hidalgo de los de lanza en
	astillero, adarga antigua, rocín flaco y galgo corredor.

Enter the Python shell again as we did before. Let’s now count the words with Spark:

	>>> def show (x): print x

	(type return again when you see "...")

	>>> linesRDD = sc.textFile("example1.txt")
	>>> linesRDD.foreach(show)
	>>> countsRDD = linesRDD.flatMap(lambda line: line.split(" ")) \
             .map(lambda word: (word, 1)) \
             .reduceByKey(lambda a, b: a + b)
    >>> countsRDD.foreach(show)
	>>> countsRDD.first()
	>>> countsRDD.saveAsTextFile("results")

Finally, check the contents of output files within the "results" directory.

Now that you have run your first Spark code using the shell, it’s time learn about programming in it in more detail. In Spark we express our computation through operations on distributed collections that are automatically parallelized across the cluster. These collections are called Resilient Distributed Datasets, or RDDs. An RDD is a collection of elements that can be operated in parallel. The operations that you define over an RDD do not refer to a particular item, but to any partition of the RDD. In the example above, the variable called linesRDD is an RDD, created here from a text file on our local machine.   

Let's execute the previous example step by step. First we split the input file into lines:

	>>> linesRDD = sc.textFile("example1.txt")
	>>> linesRDD.foreach(show)

The obtained linesRDD is a Resilient Distributed Dataset (RDD). Once created, RDDs offer two types of operations: transformations and actions. Transformations construct a new RDD from a previous one. Let's transform linesRDD by splitting each line into words (a new RDD).

	>>> words = linesRDD.flatMap(lambda line: line.split(" "))

The flatMap transformation is not executed till an action requires it, such as the following:

	>>> words.foreach(show)

Let's map each word into a tuple (word, 1):

	>>> count = words.map(lambda word: (word, 1)) count.foreach(show)
	>>> words.foreach(show)

Finally, we apply an operation (sum) to all the tuples with the same key (word). This is the reduce operation:

	>>> counts = count.reduceByKey(lambda a, b: a + b) 
	>>> counts.foreach(show)

This may seem too complicated to count a bunch of words. The advantage is that each stage can be performed in parallel by many computers.

## 5.	Simple clustering example (K-Means)

Download example2.txt :

	$ wget https://raw.githubusercontent.com/rtous/edcav/master/spark/example2.txt

which has the following content:

	1.2 2.1
	1.1 2.2
	1.0 2.3
	1.3 2.2
	1.2 2.2
	3.3 3.2
	3.4 3.1
	3.8 3.6
	3.5 3.9
	3.2 3.2

![Alt text](/spark/example2.png?raw=true "Optional Title")

Check the data with the following:

	>>> data = sc.textFile("example2.txt")
	>>> data.foreach(show)

In the following example, after loading and parsing data, we use the K-Means object to cluster the data into two clusters. Let's first prepare our data. The initial RDD contains lines of text, we will translate each line into an array of floats.

	>>> from numpy import array
	>>> parsedData = data.map(lambda line: array([float(x) for x in line.split(' ')])).cache()
	>>> parsedData.foreach(show)

At this point we have an RDD of vectors. With this data, we will run the k-means algorithm with k=2.

	>>> from pyspark.mllib.clustering import KMeans
	>>> clusters = KMeans.train(parsedData, 2, maxIterations=10,
        runs=10, initializationMode="random")
	>>> clusters.clusterCenters

We can predict the cluster to which a new data point would belong just by doing:

	>>> clusters.predict([2.2, 2.0])
	>>> clusters.predict([3.2, 1.9])

We can compute Within Set Sum of Squared Error (WSSSE). You can reduce this error measure by increasing k.

	>>> from pyspark.mllib.clustering import KMeans
	>>> from math import sqrt
	>>> def error(point):
    	center = clusters.centers[clusters.predict(point)]
    	return sqrt(sum([x**2 for x in (point - center)]))
	>>> WSSSE = parsedData.map(lambda point: error(point)).reduce(lambda x, y: x + y)
	>>> print("Within Set Sum of Squared Error = " + str(WSSSE))

## 6.	Working with text

Download example3.txt :

	$ wget https://raw.githubusercontent.com/rtous/edcav/master/spark/example3.txt

which has the following content:

	messi barcelona madrid instagram
	barcelona paella playa sangria instagram
	paella barcelona instagram
	ramblas paella ramblas instagram
	messi madrid instagram
	barcelona messi instagram
	messi gol barcelona instagram
	sangria paella instagram

Let’s start by generating an RDD in which each data element corresponds to a line of our input file.

	>>> documents = sc.textFile("example3.txt").map(lambda line: line.split(" "))
	>>> documents.foreach(show)

Each line of text will represent an independent document (e.g. a headline). We are going to use a classic feature vectorization method (Term Frequency, TF) widely used in text mining to transform our lines of text into numeric vectors. For each term in the entire set of documents (the corpus) we will count the occurrences for a given document. The resulting vector (histogram) will represent the document. This way we will be able to cluster them with K-Means. Spark already provides functions to compute TF, so let’s use them:

	>>> from pyspark.mllib.feature import HashingTF
	>>> hashingTF = HashingTF()
	>>> tf = hashingTF.transform(documents)
	>>> tf.foreach(show)

Instead of using vectors with as many dimensions as different terms appear in the documents, Spark uses the [hashing trick](https://en.wikipedia.org/wiki/Feature_hashing). The frequency of a term is inserted in the position given by the hash of the term. The vector has as many dimensions as possible hash values, so it's extremely sparse and it's stored as a Spark SparseVector (NUM_DIMENSIONS, [POS1, POS2, ...], [VALUE_POS1, VALUE_POS2, ...]). We can get the hash of a term by calling:

	>>> hashingTF.indexOf('messi')

At this point, we have the tf variable that contains the frequencies of the document. Now, we will create two clusters with the KMeans algorithm:

	>>> from pyspark.mllib.clustering import KMeans
	>>> from numpy import array
	>>> clusters = KMeans.train(tf, 2, 1, 1)
	>>> results = documents.map(lambda x : array([x, clusters.predict(hashingTF.transform(x))]))
	>>> results.foreach(show)

Given a new document, we can check to which cluster it belongs:

	>>> clusters.predict(hashingTF.transform("paella ramblas ramblas".split(" ")))
	>>> clusters.predict(hashingTF.transform("messi madrid barcelona".split(" ")))

## 7.	Working with JSON

Download example4.txt :

	$ wget https://raw.githubusercontent.com/rtous/edcav/master/spark/example4.txt

which has the following content (JSON data representing tags for pictures):

	{"tags":["messi","barcelona","madrid"]}
	{"tags":["barcelona","paella","playa","sangria"]}
	{"tags":["paella","barcelona"]}
	{"tags":["ramblas","paella","ramblas"]}
	{"tags":["messi","madrid"]}
	{"tags":["barcelona","messi"]}
	{"tags":["messi","gol","barcelona"]}
	{"tags":["sangria","paella"]}

First, we will process the data:

	>>> photosMetadata = spark.read.json("example4.txt")
	>>> photosMetadata.foreach(show)
	>>> photosMetadataAsArray = photosMetadata.rdd.map(lambda x: array(x[0]))
	>>> photosMetadataAsArray.foreach(show)

Once we have an array with the data, we can repeat the same steps what we did in the previous section, to create the TF and clustering with KMeans.

	>>> tf = hashingTF.transform(photosMetadataAsArray)
	... (do the same you did in Section 6 to cluster the data)

## 8.	Simple regression example (Linear Regression)

Download example5.txt :

	$ wget https://raw.githubusercontent.com/rtous/edcav/master/spark/example5.txt

which has the following content:

	4.49 1:2.45
	6.25 1:3.83
	2.43 1:1.50 
	9.78 1:5.03 
	7.96 1:4.06

This file uses the libsvm data format (label dim:value dim:value...). Let's load the data:

	>>> data = spark.read.format("libsvm").load("example5.txt")

We can split the data into trainingData and testData.

	>>> (trainingData, testData) = data.randomSplit([0.7, 0.3])

Let's compute the regression model:

	>>> from pyspark.ml.regression import LinearRegression
	>>> lr = LinearRegression(maxIter=10, regParam=0.3, elasticNetParam=0.8)
	>>> lrModel = lr.fit(trainingData)
	>>> print("Coefficients: %s" % str(lrModel.coefficients))
	>>> print("Intercept: %s" % str(lrModel.intercept))

Let's use the model to do some predictions

	>>> predictions = lrModel.transform(testData)
	>>> predictions.foreach(show)

We can summarize the model over the training set and print out some metrics:

	>>> trainingSummary = lrModel.summary
	>>> print("RMSE: %f" % trainingSummary.rootMeanSquaredError)

## 9.	Simple classification example (Linear SVM)

Download example6.txt :

	$ wget https://raw.githubusercontent.com/rtous/edcav/master/spark/example6.txt

which has the following content:

	0 1:1.45 2:1.45
	0 1:1.83 2:1.45
	0 1:1.50 2:1.45
	1 1:5.03 2:4.45 
	1 1:4.06 2:3.45

Let's load the data:

	>>> data = spark.read.format("libsvm").load("example6.txt")

And split the data into trainingData and testData.

	>>> (trainingData, testData) = data.randomSplit([0.7, 0.3])
	>>> trainingData.show()

	>>> from pyspark.ml.classification import LinearSVC
	>>> lsvc = LinearSVC(maxIter=10, regParam=0.1)
	>>> lsvcModel = lsvc.fit(trainingData)
	>>> print("Coefficients: " + str(lsvcModel.coefficients))
	>>> print("Intercept: " + str(lsvcModel.intercept))
	>>> predictions = lsvcModel.transform(testData)
	>>> predictions.show()

## 10.	Delivery

A .txt file containing the output of all the commands have to be delivered through the proper section within http://atenea.upc.edu


## ANNEX 1.	Alternative setup with your own computer

### A1.1 With Docker

In order to perform this lab assignment over Docker you could run an Ubuntu container as explained [here](../docker_ubuntu.md). Additionally, you should install some dependencies:

	root@813847d78b39:/# apt-get install -y python
	root@813847d78b39:/# apt-get install -y python-pip
	root@813847d78b39:/# apt-get pip install numpy
	root@813847d78b39:/# apt-get install default-jdk

You can then jump to section 3.

### A1.2 Direct installation (without Docker)

#### Linux, Mac and WSL users 

If you prefer to just install everything in your OS let's first ensure that you have the required dependencies (Java, Python, numpy):

	$ java –version
	$ python -V
	$ python
	>>> import numpy

Then you can jump to section 3. 

#### Windows users 

Running Spark on Windows is slightly more difficult. First you need to be sure that you meet the following dependencies (we don't provide help for that):

1. Java 6 or higher
2. Python 2.7 or higher
3. Python's numpy package installed
4. Windows binaries for Hadoop versions (winutils)

If everything is ok they you can download Spark from https://spark.apache.org/. You have to download one of pre-built version in "Choose a package type" section (for instance we tested this hands-on with spark-2.2.1-bin-hadoop2.7). Unpack the ...tgz into C:/spark, move there and run bin\pyspark.

