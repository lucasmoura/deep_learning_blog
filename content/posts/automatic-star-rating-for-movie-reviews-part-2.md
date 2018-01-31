---
title: "Automatic Star Rating for Movie Reviews Part 2"
date: 2018-01-31T20:46:57-02:00
draft: true
---

In the previous part of this series, I presented the Dataset of movie reviews I
have gatheres and analyzed it a little. Now it is time to train a model using
it. The first model I tried is a
[Bag Of Words](https://en.wikipedia.org/wiki/Bag-of-words_model) model, as can
be seen on the following picture.

![image alt text](/automatic-star-rating-for-movie-reviews-part-2/bag_of_words.png)

In the above image, we can see that the model will receive a list of ids as
input. Every id in the array represents a word that can be found on one of the
movie reviews. This means that a film review is converted into a list of word
ids before being fed to the model. 

The second step of this network turning each into an array, by using the
[Word Embedding](https://en.wikipedia.org/wiki/Word_embedding) matrix. This
matrix has V rows and D columns, where V is the size of vocabulary and D is the
dimension size of the embedding. Therefore, each row in the matrix represents a
different word in the vocabaulary. This also means that each id in word list
represents a distinct row in the embedding matrix.

After turning each id into an array of size D, we sum all of these arrays and
average them, turning the whole movie review into an array of size 1 X D. This
array is then fed into a neural network model, which outputs the probability of
the movie beloging to any of the possible 5 star ratings.

