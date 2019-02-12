# Session 2. Big Data Analytics with Spark (2 hours)

## 1.	Introduction

This session is intended to help the student to get started with Apache Spark, a powerful open source framework to process big data with high speed and easy to use.


## 2.	Environment settings

In order to be able to perform the different tasks you should:

- Boot the PC with the Linux image (preferable) or Windows 
- Log in with the user/password that you use for Atenea (or, in case it does not work, with (invitado, invitado) on Linux or (A2S105-??\invitado, without password) on Windows)


## 3.	Install Spark

Spark requires Java 6 or higher. As we are going to use the Python interactive shell we will need Python 2.6 or higher: 

	$ java –version
	$ python -V

For the examples we will need numpy. If you don't have it installed let's do a: 

	pip install numpy

(If you don't have pip installed do a "sudo apt-get install python-pip")

First of all, we need to download the Spark environment. To do that, we can just execute the following command:

	$ wget http://apache.rediris.es/spark/spark-2.2.1/spark-2.2.1-bin-hadoop2.7.tgz

Alternatively (if you want a different version or you are a Windows user) you can go to https://spark.apache.org/ and download the files. In all of this hands-on we will work with Spark v2.2.1.  Important: you have to download one of pre-built version in "Choose a package type" section (for instance we tested this hands-on with spark-2.2.1-bin-hadoop2.7).

Once we have the tarball file, we need to uncompress it:

	$ tar -xvzf spark-2.2.1-bin-hadoop2.7.tgz

Let’s execute the interactive Python shell:

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

Troubleshooting: If it reports a problem binding to localhost perform the following actions: 

*	Rename file spark-2.2.1-bin-hadoop2.7/conf/spark-env.sh.template to spark-env.sh
*	Edit spark-2.2.1-bin-hadoop2.7/conf/spark-env.sh and set SPARK_LOCAL_IP=127.0.0.1
 
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

Now that you have run your first Spark code using the shell, it’s time learn about programming in it in more detail. In Spark we express our computation through operations on distributed collections that are automatically parallelized across the cluster. These collections are called Resilient Distributed Datasets, or RDDs. In the example above, the variable called linesRDD is an RDD, created here from a text file on our local machine.

Once created, RDDs offer two types of operations: transformations and actions. Transformations construct a new RDD from a previous one. In our text file example, flatMap, map and reduceByKey are transformations. Spark only computes transformations in a lazy fashion, the first time they are used in an action (e.g. first() or saveAsTextFile(…) are actions). 


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


