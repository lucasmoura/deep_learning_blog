---
title: "Automatic Star Rating for Movie Reviews - Part 1"
date: 2018-01-05T22:55:14-02:00
---

One of my favorite hobbies in the world is watching movies. Usually, if I stay
one week without watching a new movie I feel that something is wrong, After
watching a movie, I almost read reviews from different critics on the Internet,
to look into different opinions about the movie I just saw or just to learn
something new about movie in general. Most of the critics that I read rate their
movies using the "star" rating, where a movie get a rate ranging from one to
five stars.

However, this rating system has some problems. I have seen some critics
complaining that the text they wrote do not reflect the grade the movie
received. This is a perfect example from an Brazilian critic:

<blockquote class="twitter-tweet" data-lang="en"><p lang="pt" dir="ltr">&quot;Você deu 3 estrelas pra La La Land e 5 pro (filme do qual não gostei)&quot;. <br><br>1) Não &quot;dei estrelas&quot;. Escrevi 1600 palavras<br>2) ODEIO cotações.</p>&mdash; Pablo Villaça (@pablovillaca) <a href="https://twitter.com/pablovillaca/status/821713080518381568?ref_src=twsrc%5Etfw">January 18, 2017</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The tweet can be translated to:

```
"Why did you give 3 starts to La La Land and 5 start to (movie which I didn't
like)"

1)I did not *give stars*. I wrote 1600 words.
2) I HATE ratings.
```

It is easy to understand why some critics may find this task difficult. Not only
comprising the whole review into a single grade is difficult, but there is also
the requirements of some readers about *consistency*, as can be seen on the
above tweet.

With that problem in hands, I have started a project to see if you could
generate this kind of rating from a raw movie review using some Machine Learning
techniques.

However, in order to use machine learning, we need data. For this problem, our
data was obtained from three different websites that publish movie reviews with
this type of rating (All of the websites are in Brazilian Portuguese):

* [Cinema Em Cena](http://cinemaemcena.cartacapital.com.br/)
* [Omelete](http://omelete.uol.com.br/)
* [Cineclick](http://cineclick.com.br/)

After crawling these websites for reviews, I reached this amount of data:

![image alt text](/automatic-star-rating-for-movie-reviews-part-1/total_number_of_reviews.png)

Which is a really small dataset. But there is also a second issue with this
dataset, as can be seen on the following graph:

![image alt text](/automatic-star-rating-for-movie-reviews-part-1/reviews_vs_ratings.png)

The rating's classes are not balanced. There are way more reviews with "3" stars
than ones with "1" and "5" stars. But that is kind of expected. There will be
way more "okay" movies than awful or excellent ones. This trend can also be
perfectly observed when we look at the distribution of ratings on each website:

![image alt text](/automatic-star-rating-for-movie-reviews-part-1/omelete_number_of_reviews.png)
![image alt text](/automatic-star-rating-for-movie-reviews-part-1/cinema_em_cena_number_of_reviews.png)
![image alt text](/automatic-star-rating-for-movie-reviews-part-1/cineclick_number_of_reviews.png)

*(The review's ratings from **Cineclick** have float values, such as 2.5 stars.
In that case I have rounded the review upward)*

We can see that most websites follow this trend on distributing the reviews
ratings, with the exception of *Cinema Em Cena*, which has a higher rating of
"4" stars than "3" stars.

Finally, we must also look to the average size of the reviews when considering
the model we will choose:


![image alt text](/automatic-star-rating-for-movie-reviews-part-1/omelete_sentences_size.png)
![image alt text](/automatic-star-rating-for-movie-reviews-part-1/cinema_em_cena_sentences_size.png)
![image alt text](/automatic-star-rating-for-movie-reviews-part-1/cineclick_sentences_size.png)

We can see that the average number of words in each review is kind of big,
specially for the reviews from *Cinema Em Cena*.

We now have a small and unbalanced dataset and with reviews with a relatively
high number of words. Therefore, I don't believe that a simple model and a
simple training approach will be enough to solve this problem.

However, before throwing Deep Learning into the problems, in the second post of
this project I will create a simple baseline with a *bag of words* model and try some approaches on how to handle
the unbalanced dataset. Stay tuned :)

*(If you want to better understand how am I downloading the files, the format of
each review file and how all of these graphs were generated, please take a look
at the [github](https://github.com/lucasmoura/movie_critic_stars) page for this
project. PR are always welcome too)*
