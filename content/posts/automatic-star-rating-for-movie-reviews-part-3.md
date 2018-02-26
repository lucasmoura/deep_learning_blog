---
title: "Automatic Star Rating for Movie Reviews Part 3"
date: 2018-02-19T14:32:41-03:00
---

In the [previous part](https://lucasmoura.github.io/blog/2018/01/31/automatic-star-rating-for-movie-reviews-part-2/)
of this series, I created a Bag Of Words model to classify
the reviews. In this part, I will present two other models I have implemented, a 
Recurrent and Convolutional model.


## Data Preprocessing

The data pre-processing used in the [previous part](https://lucasmoura.github.io/blog/2018/01/31/automatic-star-rating-for-movie-reviews-part-2/)
of this series was applying tokenization to the movie reviews, removing the
common Portuguese stop words and turning each token into a word-id that
references a row in the word embedding matrix (This row represents the original
token). This was to feed the movie reviews into the **Bag Of Words** model.

For both new models, I adopted the same preprocessing approach as the Bag Of Words model,
except that the stop words are not removed from the reviews.

## The Recurrent Model

A Recurrent Neural Network is an specialized model used, mostly, to handle text
data. That is because this model uses as input not only a word to process, but
also the last state of the model, that was produced by all the other words
before the current word that is being fed to the model. This can be better
visualized in the following picture:

![](/automatic-star-rating-for-movie-reviews-part-3/rnn.png)
*Figure 1: Representation of a Recurrent Neural Network. Take a look the second
timestep. We can see that the network will take as input not only x<sup>(2)</sup>, but
also h<sup>(1)</sup>, which was generated based on all the past x inputs in the
network*

Therefore, this model will read the whole movie review, token by token. At the
end, it will have generated a final vector representation of the text.
Afterwards, this vector is then used to classify the review into the 5 possible
star ratings.

I employed the [LSTM cell](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) with 128 units to
process the hidden step of this network (the vector represetation of the review).
Since I have a small dataset, I applied dropout[1] to the embeddings matrix and the hidden states of the LSTM.
The dropout mask must be the same along all the timesteps of the LSTM calculation[2].

Finally, I have also added L2 regularization to the weights of my model, as
another way to prevent overfitting. However, this was not enough, since the best
accuracy for the recurrent model was 30% using the **Full** dataset. The
overfitting result can be observed in the following confusion matrix:

![image alt text](/automatic-star-rating-for-movie-reviews-part-3/rnn_confusion_matrix.png)

We can see that the features the model learned to classify 3 and 4-star reviews
do not generalize well, the other classes (1, 2 and 5) are also mostly
classified as 3 and 4-star reviews.

We can notice that the model's learned features do not generalise well. 
The other classes (1, 2 and 5) are also mostly ranked as 3 and 4-star reviews.

Another problem with this model is the average size of the reviews, as seen in the
[first part](https://lucasmoura.github.io/blog/2018/01/05/automatic-star-rating-for-movie-reviews---part-1/)
of this series. The review size creates a heavy burden on the LSTM memory cell since it must 
now handle long term dependencies in the text.

## The Convolutional Model

The convolutional model I have created is based on the work of
Kim, Y.[4], which can be best visualized in the following picture:

![image alt text](/automatic-star-rating-for-movie-reviews-part-3/kim_cnn.png)
*Representation of the Convolutional Model for sentence classification. The
model applies a convolutional filter over the sentence, where each filter size
represents a different type of n-gram. The information of the
collected n-grams is summarized by a pooling layer. Afterward, the summarized vector is
fed into a neural network that generates the classification. Image from [3]*

The idea of this model is to apply a 1-D convolutional filter over the word
embeddings, where each filter corresponds to a different n-gram filter. For
example, if we a have a size 2 filter, we would apply it to two consecutive
words and generate a new value; If we have 20 size 2 filters, we
would be computing 20 different computations for bigrams. One
filter could look at two consecutive positive words, while a separate filter
might look at a negation word followed by an adjective. Therefore, it is useful
to have different filter size to distinct type of n-grams(trigrams, 4-grams, ...).

In my model I have created 32 different filters for each distinct size (3, 4 and 5 in my
model). After applying the convolutions, we use the pooling layer summarize the
information provided by the convolutions. This will generate a vector
representation of the sentence that will used to fed a fully connected network,
which is responsible for creating the predictions.

The best accuracy achieved by this model is 34% using the **Full** dataset.
This result can be best visualized through the confusion matrix:

![](/automatic-star-rating-for-movie-reviews-part-3/cnn_confusion_matrix.png)

We can see that the model suffers from the same problems as the ones presented
by the Recurrent Model, where it overfits for 3 and 4-star reviews. This
problem is present in the model trained with the **Full (underflow)** dataset as
well.

I believe that the filters learned by the model do not generalize because of the
small dataset I have, allowing the filters to overfit for the traning data.

## Single Source Datasets

The results for single source datasets resembles the results of both models using
the **Full** dataset. For example, looking at the confusion matrix produced
by the Convolutional model for the **Cineclick** dataset, we can see that the
model clearly overfits.

![](/automatic-star-rating-for-movie-reviews-part-3/cnn_cineclick.png)


## Back to Bag Of Words

After a close look at the results, the Bag Of Words model obtained the best result, as can be seen by the last 
[past post](https://lucasmoura.github.io/blog/2018/01/31/automatic-star-rating-for-movie-reviews-part-2/)
of this series. I tried to improve this model by restricting the vocabulary for
words that appeared in the training data more than 10 times ( A
behavior already implemented in both Recurrent and Convolutional models).
However, this approach did not provide any improvements to the model.

I also tried to apply the oversampling technique to the datasets used for the
Bag Of Words model, to handle ratings with fewer reviews;
but again, no improvement to the model
was perceived. Instead, the model was worse at generalizing to the validation
dataset of all available datasets.

## Conclusion

There are other approaches which I could try to use to make improvements in the models.
I could create a language model based on movie reviews from different
sources (e.g., reviews which do not have a rating) and try to check if the
model generates a "sentiment unit" as has happened with
[OpenAI](https://blog.openai.com/unsupervised-sentiment-neuron/). However, I
still believe that I will not have enough data to do that.

Another approach I could try is to add sentiment information to the word
embeddings. Since word embeddings are generated with the idea to capture
semantic information, there is no guarantee that they carry sentiment
information already. Although I believe this would be beneficial to the Bag Of Words
model, I don't think that the model accuracy would radically improve. That's
because the dataset used is too small and there are few examples of each
classification type. Furthermore, the reviews are complex text
structures, making finding patterns that generalize a difficult task.

In conclusion, I realized that I underestimated this task. I did not think that the collected dataset would be
that much problem as my models have proven me. However, this was still a great experience to have.
I have learned a lot not only about Natural Language Processing; but also about Tensorflow and different models
for text classification. I have confidence that I will
have better judgement on my next NLP task and also be faster to implement new solutions.

*(If you want to better understand how I implemented the models and how I
generated the results presented in this article, please take a look
at the [Github](https://github.com/lucasmoura/movie_critic_stars) page for this
project.)*

## Bibliography

* [1](https://www.cs.toronto.edu/~hinton/absps/JMLRdropout.pdf) Dropout: A Simple Way to Prevent Neural Networks from Overfitting
* [2](https://arxiv.org/abs/1512.05287): A Theoretically Grounded Application of Dropout in Recurrent Neural Networks
* [3](http://www.aclweb.org/anthology/D14-1181): Convolutional Neural Networks for Sentence Classification
