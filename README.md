# Sentiment-Analysis-Amazon-US-Customer-Reviews

Introduction:

In this project we are extracting the data using Python language for the provided dataset and doing preprocessing work using Spark. We then go into building a machine learning model to predict bad reviews in the provided dataset using deep neural networks and output the accuracy. Our objective is to predict which reviews/keywords gives a bad review.

We also compare the accuracy of this model with SVM.

Description:

To accomplish the above-mentioned task, we have taken the following steps:
Building model using Deep Neural Network:
We built 2 models, the first model using just the words in the review and second model using the review body as well as other headers such as verified purchase, star_rating, helpful_votes, total_votes.

1) As part of model one - First step involved loading the dataset and pre-processing using Spark. As part of data cleaning we selected only the required column(review_body) and dropped other columns. We also did operation like converting to lowercase and filtering using delimiter for the data in the selected column.
2) Ways we optimized using pyspark :
a) We used cache to keep dataframes we reused in memory
b) We dropped columns of dataframes after we no longer used them
c) We used depersist when we wanted to remove dataframes from memory before executing demanding tasks
d) We saved models. Such as idf models, that took a long time to compute.
e) Gathered small datasets that we'll reuse on one partition of the hdfs so we can execute tasks involving it faster. Used coalesce(1) to accomplish this.
f) Counting all of the distinct words, which will be the input size for the neural network
g) Used the libraries HashingTF and IDF to convert set of words into fixed-length feature vectors and to evaluate relevancy of a word in a dataset
The following pipelines of operations were done - documentAssembler, sentenceDetector, tokenizer, lemmatizer, stopWordscleaner.
Once the data transformation was done, we stored the result of this action.
4) We then start building our model using Keras and compiled using appropriate optimizer. Since the data is in vector form, we then converted to pandas, so we can pass the data in a tensorflow.
5) Then we defined the test and train parameters. We also split the data into 80% for training and 20% testing, fitted to 20 epochs and built our model. The accuracy of the model built using first neural network was found to be 89%
7) We then went onto build second neural model using more variables and data cleaning was done as like first model(Steps 2 and 3) and stored the resulting data. As part of this process, we also did the below actions :
a) converted verified purchase column in the dataset to binary (from y and n to 0 and 1)
b) converted star_rating column in the dataset to binary. If star rating is 1, value is changed to 0. Otherwise, the value is changed to 1
c) converted helpful_votes column in the dataset to int
d) converted total_votes column in the dataset to int
8) We built our second neural network (process same as step5) and the accuracy was found as 88%
Comparison with SVM:
Here we make predictions using SVM classifier on the train and test set and found the accuracy as 88%
Challenges:
1) We faced memory constraints, so we needed to limit our dataset to first 1000 rows.
Workaround - We were able to use the full dataset and preprocess it. Save it to main memory in the server as a csv file which we can load as a pandas file. This will get around our memory constraint. However, we couldn't find a way to make this run as one python file. We'd have to run a python file to preprocess, save the results in a csv file. Go in the terminal to get the names of the csv files if they are partitioned and use getmerge to load them into 1 csv file. Then, we'd load this csv file into another python application that will use pandas to load the data and we can use this dataframe to train our neural network because we won't have to use functions like collect() and toPandas() that makes us use all of our memory.
However, we thought loading big data into local server may not be a good idea because it may make the server crash. Saving the dataframes as one csv file wasn't a problem for our dataset
because it's not that big so coalesce function gathers all of the data into one output csv file. But it'd be an issue for larger datasets so we avoided it.
2) Starting Hadoop took a long time and intermittent server down issues.

Dataset: https://www.kaggle.com/datasets/cynthiarempel/amazon-us-customer-reviews-dataset/code?select=amazon_reviews_us_Mobile_Electronics_v1_00.tsv
