# Ranked Retrieval on Twitter Dataset

### Steps to setup 
Step 1: Clone the Repo
 ```git clone https://github.com/manju1201/IR_Project_Ranked_Retrieval.git```
 
#### Data Collection
* Dataset1  --  [worldcup tweets](https://drive.google.com/file/d/1lJyVxGjitPm6wZALw6grQiJIcGUqKgMM/view?usp=sharing)
* Dataset2  --  [Food reviews](https://drive.google.com/file/d/1_pSz9AeNHbDuLE6k_nuivAeXDOr2IAxO/view?usp=sharing)
* Dataset3  --  [US Elections](https://drive.google.com/file/d/1FgooBMnj_aVzt0nQyREDP-P1oMfYpzsg/view?usp=sharing)

#### Merged dataset 1,2,3
* Dataset used for search and training -- [IR_domain_custom_dataset](https://drive.google.com/file/d/1A84bJNMIDKvyMi6ws5xct9wCXOxs3F2M/view?usp=sharing)

The basic procedure followed here is
* Collecting the data set.
* Preprocessing
* Classify based on domain.
* Indexing.
* Search for a query.

#### 1.Collecting the Dataset
* I have taken Twitter as my domain so I considered three different categories of data. The
three categories are Fifa world cup, US elections, Food reviews.
* Fifa world cup(943169 examples):
  * Required field : tweet_text
  * Added a new column, domain = 1
* Food Reviews(568454):
  * Required field : Text
  * Added a new column, domain = 2
* US Elections:
  * Required field : tweet_text
  * Added a new column, domain = 3
* I have filtered each example of word count to be a minimum of 12 and taken 33333 from each
category and merged them to get 100K examples which will be used as a dataset for the
classification and ranked retrieval.

#### 2.Preprocessing:
* All the examples we have are converted into strings and undergo processing.
* In the data we have emojis and emoticons that are also converted into words eg: 😀
converted to ‘Happy’ .
* Also converted mentions to tags eg: #BarackObama #Obama Obama converted into "<tag1><tag2>" .
* Also removed Html tags, whitespaces, accented characters, contractions like don’t into
do not, then converted all characters into lowercase(normalization).
* Now, the words are tokenized and the stop words, special characters, punctuation marks
like “United Stated” being converted to “us” and numbers are removed and number
words are converted into numeric numbers like 100 dollars to hundred dollars .
* If we have two words like ‘see’ and ‘seeing’ then they would be stored in the index as
two different terms whereas in actual sense we wouldn’t need them as two different
words. So we take into consideration millions of documents in real time. This would
create a huge amount of memory and take huge process time. So, the best way to index
only necessary terms is to stem them. So, for the above example, after lemmatization
the terms stored in the index is just ‘see’.
* All the preprocessing is done and the cleaned tokens are combined back into
sentences.


#### Classification
After the preprocessing the entire dataset is divided into train and validation sets. The
method used to give weights to each word is Ngram Level TF-IDF. The TF-IDF vectors are given
to the Multinomial naive bayes model for training.
#### TF-IDF
TF-IDF stands for “Term Frequency — Inverse Document Frequency” . This method is to
quantify a word in documents, we generally compute a weight to each word which signifies the
importance of the word in the document and corpus.
##### Term Frequency
This measures the frequency of a word in a document. This highly depends on the length of the
document and the generality of the word. TF is individual to each document and word, hence we
can formulate TF as follows.
* tf(t,d) = count of t in d
##### Document Frequency
This measures the importance of a document in the whole set of corpus, this is very similar to
TF. The only difference is that TF is frequency counter for a term t in document d, whereas DF
is the count of occurrences of term t in the document set N. In other words, DF is the number
of documents in which the word is present. We consider one occurrence if the term consists in
the document at least once, we do not need to know the number of times the term is present.
* df(t) = occurrence of t in documents
##### Inverse Document Frequency
* IDF is the inverse of the document frequency which measures the informativeness of term t.
When we calculate IDF, it will be very low for the most occurring words such as stop words. This
finally gives what we want, a relative weightage.
  * idf(t) = N/df
* Now there are few other problems with the IDF, in case of a large corpus, say 10,000, the IDF
value explodes. So to dampen the effect we take log of the IDF. 
  * idf(t) = log(N/df)
*Finally, by taking a multiplicative value of TF and IDF, we get the TF-IDF score,
  * tf-idf(t, d) = tf(t, d) * log(N/df)
##### N-gram Level TF-IDF:
N-grams are the combination of N terms together. This Matrix represents tf-idf scores of N-grams.
Now these N-gram tf-idf vectors are given to the Multinomial naive bayes model for training.
##### Evaluation
* Precision = Total no. of docs retrieved that are relevant / Total no. of docs that are retrieved
* Recall = Total no. of docs retrieved that are relevant / Total no. of relevant docs in database


#### Ranked Retrieval(Search)

##### 4.Indexing and Ranked-Retrieval
 
* After preprocessing the doc IDs and their indexes are created.
* We are using trie as a data structure where this builds an inverted index of documents.
* Text is given to Trie data structure where the Node() class will create the nodes. Later the CollectionNode() class will add docID to documents at the end of suffix then return the list of documents containing suffixes.
* Here the nodes are nothing but the tokens we have after preprocessing. 
* Now we will compute df and idf values for each node given in the query.
* Based on the tf-idf score we will rank the documents.

