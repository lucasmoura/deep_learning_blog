---
title: "Automatic Star Rating for Movie Reviews Part 2"
date: 2018-01-31T20:46:57-02:00
---

In the [previous part](https://lucasmoura.github.io/blog/2018/01/31/automatic-star-rating-for-movie-reviews-part-2/)
of this series, I presented the Dataset of movie reviews I
gathered and analyzed it a little. Now it is time to train a model using
it. The first model I tried is a
[Bag Of Words](https://en.wikipedia.org/wiki/Bag-of-words_model) model, as can
be seen in the following picture.

![image alt text](/automatic-star-rating-for-movie-reviews-part-2/bag_of_words.png)

In the above image, we can see that the model receives an array of word-ids.
An id represents a unique word that can be found in a movie review.
This means that a film review is converted into a list of word-ids before being fed to the model

The first step of this network is turning each word-id into an embedding array using a
[Word Embedding](https://en.wikipedia.org/wiki/Word_embedding) matrix. This matrix has V rows and D
columns, where V is the size of vocabulary (number of unique words that can be found in the
reviews). D is the dimension size of the embedding. Therefore, each row in the matrix represents
a different word in the vocabulary, meaning that each word-id in the input represents a distinct
row in the Embedding matrix.

After turning each id into an array of size D, we sum all of these arrays and
average them, turning the whole movie review into an array of size 1 X D. This
array is then fed into a neural network model, which output the probability of
the movie belonging to any of the possible 5 star ratings.

## Data Preprocessing

Every review is turned into a list of word-ids before being fed to the
model. Before making that conversion, I have applied two distinct preprocessing
techniques. The first one is tokenization, which means that I have turned the
film review into a list of tokens, where a token is a word. For example, the
word "good!!!" would be turned into the following list of tokens ["good",
"!", "!", "!]. After that, I remove common Portuguese
[stop words](https://gist.github.com/alopes/5358189), since I am using the 
Bag Of Words technique.

Finally, each token is converted into a word-id. This conversion is achieved by loading a pre-trained
Embedding matrix and verifying if that token can be found in the matrix. If that is the case, I
assign the row of the Embedding matrix that represents this word as its word-id.

## Word Embeddings

I have chosen the pre-trained Embeddings from [fastText](https://fasttext.cc/)
to use in my model. The reason behind that is that fastText provide Portuguese Embeddings and uses
subword information to train the Embeddings. This means that the model is trained based on the
words itself and the ngrams that compose them. Since they are also training ngrams Embeddings, we
can use the ngrams Embeddings to create Embedding arrays for words the model has never seen. To achieve
that, we break the word into its ngrams and combine the embeddings of the individuals ngrams into
the word Embedding array.

Therefore, when we turn the training data into a list of tokens, we guarantee
that all tokens in the training data have a valid word-id. However, since we just use the training
set to create the word-ids, there may be words that appear in the
validation/test set that do not have a valid word-id. In that case, we turn this
word into an unknown word-id, that maps to an Embedding array containing only zeros.

## Dataset splits

Every review source (Omelete, Cineclick and Cinema Em Cena) is divided into a 5 categories.
Each category represents a movie rating and stores all movies with that rating.

After that, I have removed 20% of the data from each of these categories. 50% of this data is used
as validation data and the other half for test data. This ensures that both validation and test
sets possess a full range of ratings. The 80% remaining data is used as training data. 

Once that separation is completed, I create 5 distinct datasets:

| Dataset                  | Description |
| :---------------------: |:---------------- |
|**Full**                 |A dataset that combines the training dataset of all data sources, as well as combining the dataset for both the validation and test dataset.|
|**Full (undersampling)** |Similar to **Full**, but I have applied the [undersampling](https://en.wikipedia.org/wiki/Oversampling_and_undersampling_in_data_analysis) technique to guarantee that all ratings in the training dataset have the same amount of data. This is not true to the validation and test dataset.|
|**Omelete**              |A dataset where all train, validation and test files comes uniquely from Omelete reviews.|
|**Cineclick**            |A dataset where all train, validation and test files comes uniquely from Cineclick reviews.|
|**Cec**                  |A dataset where all train, validation and test files comes uniquely from Cinema Em Cena reviews.|


I have not used the undersampling technique for **Omelete**, **Cineclick** and
**Cec** because the training dataset generated would be too small for training the
model.

## Model definitions

I have trained two distinct Bag Of Words models to handle this task. The first
model, is a Neural Network with no hidden layers, which I call **bownh**.
The second model has a hidden layer with 150 units, which I call
**bowsh**.

Both models were trained for 15 epochs. Hyper-parameters configuration was done
using the validation dataset, leading to the following choices:

* **Optimization**: [Adam](https://arxiv.org/abs/1412.6980) algorithm with an initial learning rate
of 0.001. Stochastic Gradient Descent (SGD) was also used, with a batch size of 32.
* **Regularization**: L2 regularization with weight
decay of 0.001 and dropout on each layer, with keep probability of 0.5.

Finally, I have used [TensorFlow Estimator
API](https://www.tensorflow.org/programmers_guide/estimators) to implement both
of these models.

## Model results

The model with greatest accuracy was the **bownh** 
using the  **Full** dataset. This model achieves 47%
accuracy for the test set. However, since we are dealing with an unbalanced
dataset, the accuracy metric can be misleading. We can look at the confusion
matrix produced by the model to better understand this metric:

![image alt text](/automatic-star-rating-for-movie-reviews-part-2/best_confusion_matrix.png)

We can see that the model is doing really good on performing predictions for
both 3 and 4 ratings. However, let's look how the test set is divided:

| Label    | Number of reviews |
| :------: |:----------------: |
|   1      |        55         |
|   2      |       152         |
|   3      |       241         |
|   4      |       194         |
|   5      |        60         |

We can see that the number of 3 and 4 star reviews are way bigger in comparison
with other reviews. Therefore, the reason this model achieves the best accuracy
is simply because it is labelling more reviews as being 3 or 4 stars. It
practically doesn't generate predictions for 1 and 5 star reviews, which is
really bad.

The next best model is the **bownh** using the **Full (undersampling)** dataset.
This model achieves an accuracy of 39% and has produced the following
confusion matrix:

![image alt text](/automatic-star-rating-for-movie-reviews-part-2/undersampling_confusion_matrix.png)

Although this model has a worst accuracy, it produces best accuracies for least
represented ratings, such as 1 and 5 star reviews. Which is an improvement over
the model with best accuracy. In my opinion, this ability makes this model superior the
model with best accuracy. This fact makes me believe that the undersampling strategy was partly
successful on generating better predictions for the mode, but can still be
improved. For example, when undersampling is performed, we don't leave out a lot
of 3 and 4 star reviews. I believe it will be possible to use this extra data,
but still use the undersampling strategy, but this will require further
experimentation.

## Results for Single Source Datasets

When using these models on single source datasets(**Omelete**, **Cineclick** and
**Cec**), the results are not very positive. For both Bag Of Word models 
we have confusion matrices with this pattern:

![image alt text](/automatic-star-rating-for-movie-reviews-part-2/single_source_confusion_matrix.png)

*Confusion matrix generated from bownh using the Omelete dataset*

We can see that the model is not making many different predictions,
concentrating the predictions for 3 and 4 rating while ignoring the rest of the
possible ratings.

## Next steps

I have seen that the small and unbalanced dataset will be a real problem and
that the Bag Of Words model is not enough to handle the data. However, I have
established a baseline that can be used for comparison reasons, when I develop
new models. Therefore, in my next post I will focus on better techniques to handle this dataset
while also exploring new and more complex model architectures, for example, 
Recurrent Neural Networks. Keep tuned for more :)

*(If you want to better understand how am I implemented the model and how I
generated the results presented in this article, please take a look
at the [Github](https://github.com/lucasmoura/movie_critic_stars) page for this
project. PRs are always welcome too)*
