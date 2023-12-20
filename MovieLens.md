In this project, I will develop a movie recommendation system using the MovieLens dataset. The latest version of the MovieLens dataset is accessible through this [link](https://grouplens.org/datasets/movielens/latest/). To facilitate the task, I will use the [10M version of the MovieLens dataset](http://grouplens.org/datasets/movielens/10m/) and apply all available tools to construct my recommendation system.

I'll download the MovieLens data, run the code to generate the datasets, and then train a machine learning algorithm using the inputs in one subset to predict movie ratings in the validation set.

*Disclaimer: The information used in this content is sourced from [edx.org](edx.org). For more details on terms and conditions, please visit EDX [Terms of Service](https://www.edx.org/edx-terms-service).*

## Introduction 

You will use the following code to generate your datasets.

```r 
if(!require(tidyverse)) install.packages("tidyverse", repos = "http://cran.us.r-project.org")
if(!require(caret)) install.packages("caret", repos = "http://cran.us.r-project.org")

library(tidyverse)
library(caret)

# MovieLens 10M dataset:
# https://grouplens.org/datasets/movielens/10m/
# http://files.grouplens.org/datasets/movielens/ml-10m.zip

options(timeout = 120)
​
dl <- "ml-10M100K.zip"
if(!file.exists(dl))
  download.file("https://files.grouplens.org/datasets/movielens/ml-10m.zip", dl)
​
ratings_file <- "ml-10M100K/ratings.dat"
if(!file.exists(ratings_file))
  unzip(dl, ratings_file)
​
movies_file <- "ml-10M100K/movies.dat"
if(!file.exists(movies_file))
  unzip(dl, movies_file)
​
ratings <- as.data.frame(str_split(read_lines(ratings_file), fixed("::"), simplify = TRUE),
                         stringsAsFactors = FALSE)
colnames(ratings) <- c("userId", "movieId", "rating", "timestamp")
ratings <- ratings %>%
  mutate(userId = as.integer(userId),
         movieId = as.integer(movieId),
         rating = as.numeric(rating),
         timestamp = as.integer(timestamp))
​
movies <- as.data.frame(str_split(read_lines(movies_file), fixed("::"), simplify = TRUE),
                        stringsAsFactors = FALSE)
colnames(movies) <- c("movieId", "title", "genres")
movies <- movies %>%
  mutate(movieId = as.integer(movieId))
​
movielens <- left_join(ratings, movies, by = "movieId")
​
# Final hold-out test set will be 10% of MovieLens data set.seed(1, sample.kind="Rounding") # if using R 3.6 or later
# set.seed(1) # if using R 3.5 or earlier

test_index <- createDataPartition(y = movielens$rating, times = 1, p = 0.1, list = FALSE)
edx <- movielens[-test_index,]
temp <- movielens[test_index,]
​
# Make sure userId and movieId in final hold-out test set are also in edx set
final_holdout_test <- temp %>% 
  semi_join(edx, by = "movieId") %>%
  semi_join(edx, by = "userId")
​
# Add rows removed from final hold-out test set back into edx set
removed <- anti_join(temp, final_holdout_test)
edx <- rbind(edx, removed)
​
rm(dl, ratings, movies, test_index, temp, movielens, removed)
```
