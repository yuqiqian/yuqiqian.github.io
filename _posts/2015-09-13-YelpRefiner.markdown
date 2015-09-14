---
layout: default
modal-id: 4
date: 2015-09-13
img: yelprefiner.png
alt: image-alt
project-date: Feb 2015
category: Web Development
---

Yelp Refiner
============

1. Introduction
---------------

Yelp does not provide keywords from its reviews. In a typical webpage of a store
in Yelp, wordy reviews form a large part of the page. It might meet the
expectation of the keen sharer that their opinion is presented at a eye-catching
plot, however, those who only want to get the general impression of the store in
a limited time will find this layout too time-consuming. And compared to
Yelp.com, Amazon.com provides much better user experience. Therefore, in our
project, we create a review refiner to find the keywords from yelp reviews. It
offers the visitors with succinct description about this store, and saves time
for those who has limited time to visit the website. We use different algorithms
to generate keywords (TF-IDF and NLP), then the generated keywords can be
presented on our website.

2. Problem Definition
---------------------

Yelp is a very useful tool for users to find shops they need. Users need to get
the information from yelp reviews down below the shop website. However, yelp
shops usually contain hundreds of reviews and sometimes the review could be very
long. As a result, it takes a lot of time for users to get a brief impression on
a yelp shop since so many long reviews need to be read. Therefore, we think the
efficiency of yelp can be highly improved. In our project, we try to create a
review refiner to generate keywords from yelp reviews to improve the efficiency
of yelp.

3. Method
---------

Our project use Django to build the web framework, and choose python as
programming language. For text mining, the TF-IDF algorithm and application of
NLP are used to satisfy our main goal for extraction the keywords. Here’s our
brief introduction of our method.

### Intuition

We choose TF-IDF algorithm to calculate and weight all candidates’ keywords we
generated. The most popular method now is just using the TF (Term Frequency) to
weight the keywords. Although this is a fast way to generate keywords, it may
reduce the accuracy to some extent since when comparing with a set of documents,
the words, which are popular in most of this data set, will also be chosen as
keywords of certain documents. However, our usage of TF-IDF solves such problem
since the algorithm checks all the dataset and returns the answers.

### Algorithms: 

1)  Set up building environment

For the implement of the algorithms, first we need to setup some libraries on
python. For python version 2.7.9, we setup scipy(version 0.15.1), numpy(version
1.9.2) and NLTK(version 3.0) for further coding.

2)  Extract comments information from .txt file

In the test, we generate a .txt file of a set of comments information which
containing elements such as user id, review id, date and stars. The first step
is to extract comments text from this file. This method uses re library for
implement.

3)  TF-IDF algorithm

TF-IDF is shorten for Term Frequency–Inverse Document Frequency, a wideused
method for text analysis. It is implemented by TF and IDF separately. While in
use of extraction keywords, basically we can easily assume that the more times
the word appear, the more importance the word is for a certain document. The
appearance times of words, of course excluding all the stop words, is defined as
**Term Frequency** for documents. It, however, may cause another problem that
there’re some wide-used words always appear many times in different documents
such as “I”, “we” and etc. In other words, the words, which are not common in
other documents, with a high appearance times in certain document can represent
the main idea of the text. That’s called **Inverse Document Frequency**. Thus
the combination of TF and IDF generate sorted words from the most importance to
the least. Here’s a sample equation for TFIDF.

$$
TFIDF_{i,j} = (\frac{N_{i,j}}{N_{*,j}})*\log(\frac{D}{D_i})
$$

$N_{i,j}$ = the number of times word I appears in document j (the original cell
count)

$N_{*,j}$ = the number of total words in document j (just add the counts in
column j)

$D$  = the number of documents (the number of columns)

$D_i$  = the number of documents in which word I appears (the number of non-zero
columns in row i) 

For application, the code is written as below. “float (v)” indicates the
appearance times of candidate keywords in certain text(topic), “sum\_tf”
represents the total words in text(topic), “float(sum\_topics)” indicates the
total texts(topics) for analysis and “df[k]” represents number of the texts
containing the certain candidate keywords.

![](<file:///C:\Users\Yuqi\AppData\Local\Temp\msohtmlclip1\01\clip_image006.jpg>)

 

4)  Phrase generation by NLTK

The above TF-IDF algorithm is the main method for our project. However, it can
just analyze words in documents separately without the combination of adjective
and noun. In our test, this will arise confusion for users so this step is
mainly about how to generate phrases instead of words as keywords. Here are
several functions we write.

The remove\_signals function basically removes the useless signals in text since
these signals will make the spitted words a mass. The list of signals excludes “
. ” which is used to separate sentences and “ ‘ ” which is used in many words
such as “I’m”, “We’ve” and etc.

![](<file:///C:\Users\Yuqi\AppData\Local\Temp\msohtmlclip1\01\clip_image008.jpg>)

 

The \_generate\_candidate\_keywords function generates keywords as phrases and
words. The basic idea of this function is, for the sentences without signals
above, to replace all stop words with “ / ” and based on this signal generate
phrases and words as candidate.

![](<file:///C:\Users\Yuqi\AppData\Local\Temp\msohtmlclip1\01\clip_image010.jpg>)

 

5)  Output system

For test we output our results in different .txt files that each review
generates its own file to store the keywords.

![](<file:///C:\Users\Yuqi\AppData\Local\Temp\msohtmlclip1\01\clip_image012.jpg>)

#### Data 

We first tried to access reviews via Yelp API, but it only provides one random
review per store, which is far from enough to generate the keywords. Therefore,
we can only get a static data set from Yelp Data set Challenge program.

The data set provided by the program is as large as 1.4 GB. We use Python and
Regular Expression to clean the data and keep useful data, including name, id,
location, and corresponding reviews for each store. Then we use SQLite software
to convert data format to JSON for further treatment. The final data set is 1.09
GB and has 1.6 million records of reviews. The JSON format is {“id”: “xxx”,
“review”: “xxx”} for each.

![](<file:///C:\Users\Yuqi\AppData\Local\Temp\msohtmlclip1\01\clip_image002.jpg>)

**Approach**

We use NLP Algorithm to generate a group of keywords, by splitting sentences
into words and phases with help of Nature language Toolkit (NLTK). Then we apply
TF-IDF Algorithm on these words and phases to pick out the most unique ones
according to the relevance to text.

This is an improved method comparing with just the frequency of words in a
certain documents since it can split some unique words always appearing in most
documents. However, there is an obvious disadvantage of this method, the running
time of this greedy algorithm will increase rapidly.

5. Experiment & Evaluation 
---------------------------

We have got access to a static data set of Yelp’s reviews rather than by dynamic
request via Yelp’s API. It is not flexible, but it is low cost and can run fast.
The data is very big, which contains 1.6 million records of reviews after data
cleaning, which is enough to generate keywords with high credibility.

Our project is mainly about text mining. Therefore, text-mining algorithms have
been implemented on our data and they can generate the keywords and phrases from
reviews. The main algorithms are NLP algorithm (based on nature language
toolkit) and TF-IDF algorithm (based on the keywords generated by NLP
algorithm). NLP algorithm works well since it is powerful enough to generate
keywords as well as phrases. Additionally, we applied TF-IDF algorithm, which is
an accurate algorithm. However, there exists an obvious drawback according to
it’s time cost. Therefore, we applied several simplifications on our algorithm
to reduce the running time, for example, randomly choose a smaller data set
while generating the keywords. The webpage is deployed locally, which is
available in the package as a demo.

Also there are something we can do to improve our project, while because of the
limited time, we just list them below.

1)   To import the data into server database. It may speed up the algorithm and
apply a steadier environment to design a webpage.

2)  To do sentiment analysis based on the literature review. Based on our
research, within many ways to do sentiment analysis on the keywords, the most
suitable one for us is the usage of Python with package “TextBlob”. Actually, we
plan to extract the number of certain keywords and show if it’s positive or not.
It’s a text-mining tool built on the base of NLTK.

3)  To eliminate some confusing keywords and phrases, algorithm can be improved
by combining more functions provided by natural language toolkit.

After optimizing the algorithm part, we have plugged the python program into our
webpage and finished the interface between web framework and database. We
designed a webpage for users to search the shop. Here is the interface of our
webpage.

![](<file:///C:\Users\Yuqi\AppData\Local\Temp\msohtmlclip1\01\clip_image004.jpg>)

 

As it’s shown above, users need to type in the name of a shop with its zip code
for our yelp refiner to locate the specific shop. Then, it will automatically
run keyword generation function back-end for a while. The result with ten
keywords and phrases will pop out.

![](<file:///C:\Users\Yuqi\AppData\Local\Temp\msohtmlclip1\01\clip_image006.jpg>)

 

Besides, we also want to present results from a commercial nature language
processing service, AlchemyAPI as comparison, so the connection to AlchemyAPI
has been built.

![](<file:///C:\Users\Yuqi\AppData\Local\Temp\msohtmlclip1\01\clip_image008.jpg>)

 

AlchemyAPI can get keywords in very short time, since it only choose the most
frequent keywords. Besides, it only generates keywords, not phrases. Compare
with the result of AlchemyAPI, our result is mixed with single words with
phrases, which is a adjective with a noun. That is the evaluation of our
experiment above usual keyword generation methods, which is to pick out the most
unique, not just the most frequent, keywords and phrases.

Comparing our results with the result from AlchemyAPI, it is clearly that
AlchemyAPI just selects noun words and phrases instead of adjective ones while
our project results can return some non-noun keywords.

6. Conclusions and discussion 
------------------------------

We deploy a website to present the keywords. The website is built by Django
which can use the keyword-generating function in the back-end. By typing in a
part of the store name and its zip code, users can get 10 keywords of the store.
Also, if the store cannot be found in data set, the website also provides a list
of relevant stores each with link to its keyword page.

The website, however, has to take approximately 30 s\~2 min, according to the
programming environment, to present the keywords. It is slightly beyond the
tolerance of a visitor, but there are still improvements we can do, such as
generating keywords before query and storing them in a database, or conduct the
algorithm in parallel. Furthermore, we can still improve the algorithm to 1)
generate the sentiment tendency of a keyword or phrase, and 2) eliminate the
confusing keywords to improve the user experience.  

 

**Distribution of Team Members:**

Xiangyu Liu: algorithm and main function, contributing 25% of total work.

Yuqi Qian: website deploy, contributing 25% of total work.

Yongkai Wang: data collection, data cleaning, 25% of total work

Shenshen Wu: data cleaning, presentation, 25% of total work.
