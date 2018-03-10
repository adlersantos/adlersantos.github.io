---
layout: post
title:  "Quick and Easy Collaborative Filters using Pandas"
date:   2018-02-09
---

Here's a quick and easy way to implement collaborative filters using a dataset of movies, users, and users' ratings, although the stuff below can be generalized to similar use cases as well. It's a pretty simple implementation that we only need Pandas to get the job done. You can fire up a Jupyter notebook and follow along the discussion below if you'd like. <!--more-->

### Dataset: Movies, Users, Ratings

The data comes from the [MovieLens dataset](https://grouplens.org/datasets/movielens/). We'll use `movies.csv` and `ratings.csv` found in the [ml-latest-small.zip](http://files.grouplens.org/datasets/movielens/ml-latest-small.zip) file.

First, we load the movies and ratings data into dataframes. We only care about certain columns, so we'll make use of the `names` parameter which accepts a list of column names to use{% sidenote 'names-param' 'The files use camelcase for the column names, we map them to snakecase as our naming convention' %}, and the `usecols` parameter that accepts the range of columns we're interested in loading, e.g., `usecols=range(3)` tells Pandas to load only the first three columns.

{% highlight python %}
import pandas as pd
import numpy as np

ratings_colnames = ['user_id', 'movie_id', 'rating']
ratings = pd.read_csv('ratings.csv', names=ratings_colnames, usecols=range(3), header=0)

movies_colnames = ['movie_id', 'title']
movies = pd.read_csv('movies.csv', names=movies_colnames, usecols=range(2), encoding="ISO-8859-1", header=0)

# Merge the two dataframes into one, joining on the movie_id column
movie_ratings = pd.merge(movies, ratings)
{% endhighlight %}

The `ratings` dataframe looks like


recommend the movies with the highest predicted ratings to the user.

