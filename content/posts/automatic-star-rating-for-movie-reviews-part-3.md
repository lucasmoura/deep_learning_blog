---
title: "Automatic Star Rating for Movie Reviews Part 3"
date: 2018-02-19T14:32:41-03:00
draft: true
---

In the [previous part](https://lucasmoura.github.io/blog/2018/01/31/automatic-star-rating-for-movie-reviews-part-2/)
of this series, I created a Bag Of Words model to classify
the reviews. In this part I will present two other models I have implemented, a 
Recurrent and Convolutional model.


## Data Preprocessing

For both new models, I have used the same preprocessing approach as the Bag Of Words model,
except that the stop words are not removed from the reviews.

## The Recurrent Model

The recurrent model is Recurrent Neural Network that reads the entire review,
generating a final vector representation of the text that is then used to
perform the classification over the 5 possible star ratings.

![image alt text](/automatic-star-rating-for-movie-reviews-part-3/rnn.png)

I have used a LSTM cell with 128 units to process the hidden step of this network. The LSTM
cell has 128 units.  Since I have a small dataset, I have applied
dropout to the embeddings matrix and to the hidden states of the LSTM.
The dropout mask must be the same one during all the timesteps of the LSTM calculation,
as stated in the [paper](https://arxiv.org/abs/1512.05287) that proposes the technique.

Finally, I have also added L2 regularization to the weights of my model, as
another way to prevent overfitting. However, this was not enough, since the best
accuracy for the recurrent model was 30% using the **Full** dataset. The
overfitting result can be observed in the following confusion matrix:

![image alt text](/automatic-star-rating-for-movie-reviews-part-3/rnn_confusion_matrix.png)

We can see that the features the model learned to classify 3 and 4 star reviews
do not generalize well, the other classes (1, 2 and 5) are also mostly
classified as 3 and 4 star reviews.

Another problem of this model is the average size of the reviews, as seen in the
[first part](https://lucasmoura.github.io/blog/2018/01/05/automatic-star-rating-for-movie-reviews---part-1/)
of this series. This creates a heavy burden on the LSTM memory cell, since the
cell must now handle long term dependencies in the text.

I believe that the combination of a small dataset and long reviews caused the
poor performance of the this model.

## The Convolutional Model

The convolutional model I have created is based on the work of
[Kim, Y.](http://www.aclweb.org/anthology/D14-1181), which can be best
visualized in the following picture:

![image alt text](/automatic-star-rating-for-movie-reviews-part-3/kim_cnn.png)
*Kim, Y. (2014). Convolutional Neural Networks for Sentence Classification*

The idea of this model is to apply a 1-D convolutional filter over the word
embeddings, where each filter corresponds to a different n-gram filter. For
example, if we a have a filter of size 2, we would apply it over two consecutive
words and generate a new value. If we have 20 filters of size 2, we
would be computing 20 different computations for bigrams. One
filter could look at two consecutive positive words, while a different filter
might look at a negation word followed by an adjective. Therefore, it is usefull
to have different filter size to distinct type of n-grams(trigrams, 4-grams, ...).

In my model I have created 32 different filters for each distinct size (3, 4 and 5 in my
model). After applying the convolutions and using the pooling layer to join
them, a fully connected layer is responsible for creating the predictions.
The best model uses the **Full** dataset and achieves an accuracy of 34%.
This result can be best visualized through the confusion matrix:

![](/automatic-star-rating-for-movie-reviews-part-3/cnn_confusion_matrix.png)

We can see that the model suffers from the same problems as the ones presented
by the Recurrent Model, where it overfitts for 3 and 4 stars reviews. This
problem is present in the model trained with the **Full (underflow)** dataset as
well.

I believe that the filters learned by the model do not generalize because of the
small dataset I have, allowing the filters to overfit for the traning data.

## Single Source Datasets

The results for single source datasets do not differ much from the model results
for the **Full** dataset. For example, looking at the confusion matrix produced
by the Convolutional model for the **Cineclick** dataset, we can see that the
model clearly overfitts.

![](/automatic-star-rating-for-movie-reviews-part-3/cnn_cineclick.png)


## Back to Bag Of Words

Looking at these results, clearly the Bag Of Words model obtained the best
performance, as can be seen by the 
[past post](https://lucasmoura.github.io/blog/2018/01/31/automatic-star-rating-for-movie-reviews-part-2/)
of this series. I have tried to further improve this model by allowing in the
vocabulary only words that appeared in the training data more than 10 times ( A
behavior already implemented in both Recurrent and Convolutional models).
However, this approach did not provide any improvements for the model.

I have also tried to apply the oversampling technique to the datasets used for the
Bag Of Words model, in order to handle ratings with fewer reviews,
but again, no improvement to the model
was perceived, instead, the model was worse at generalizing to the validation
dataset of all available datasets.

## Conclusion

Currently, there are still other approaches I could try to make the models
better. I could create a language model based on movie reviews from different
sources (For example, reviews which do not have a rating) and try to see if the
model creates a "sentiment unit" as has happened with
[OpenAI](https://blog.openai.com/unsupervised-sentiment-neuron/). However, I
still believe that I will not have enough data to do that.

Another approach I could try is to add sentiment information to the word
embeddings. Although I believe this would be benefitial to the Bag Of Words
model, I don't believe that the model accuracy would improve by much. That's
because the dataset used is too small and there are few examples of each
classification type. Not only that, but also the reviews are complex text
structures, making finding patterns that generalize a difficult task.

Therefore, I strongly believe I have underestimated this task. I did not thought
that the collected dataset would be that much problem as my models have proven
me. However, this was still a great experience to have. I have learned a lot not
only about Natural Language Processing but also about Tensorflow and different
models for text classification. I have confidence that I will have better
judgmenent on my next NLP task and also be faster to implement new solutions.
