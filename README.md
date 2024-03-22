# Recipe Ratings
This is the repository for DSC80's final project at UCSD

By Howard Chen (hyc004@ucsd.edu), Jialin Wu (jih101@ucsd.edu)

---
## Introduction
Ah food~ What's not to like about food? But we can't just eat right? We have to cook! How do we do that? We look at recipes! And,in fact, that is exactly what we will be doing in our project. In this project, we aim to unveil possible trends and/or patterns between each recipe's ratings and it's characteristics.

The raw data set that we were provided with consists of 2 DataFrames contained in `RAW_recipes.csv` and `RAW_interactions.csv` sourced from food.com. 

`RAW_recipes.csv` is a DataFrame with each row being a unqiue recipe and columns including the recipe's name, id, time needed, contributor, when it was submitted, tags, nutrition information, number of steps, steps, description, number of ingredients, and ingredients. `RAW_recipes.csv` has 83782 rows and 12 columns.

`RAW_interactions.csv`, on the contrary, is a DataFrame with each row representing an 'opinion' or an 'interaction' on a specific recipe. The DataFrame has columns reflecting the user's id, recipe's id, date of when this interaction was posted, rating (numerical 1-5) and review (literal description). `RAW_interactions.csv` has 731927 rows and 5 columns. Note that there might be multiple interactions on the same recipe.

---
## Data Cleaning and EDA

### Data Cleaning
To ensure that we will be able to access relevant information from the two DataFrames effectively, we have to do some data cleaning. 

The first step we did was left merging `RAW_recipes.csv` and `RAW_interactions.csv` on the recipe's id. 

We then replaced 0s in `ratings` with `np.nan` as when someone fails select a rating between 1 and 5, it defaults to 0, when it in fact should not be considered.

After replacing the ratings, we were able to add a new column that is the average rating of that specific recipe. Because we have multiple interactions per recipe, we averaged the ratings of those interactions and added it to a new column of the corresponding rows (recipes).

We then realized that columns `nutrition`, `tags`, `steps`, and `ingredients` are stored as strings when it should have been stored as lists for easier access. So for each of these columns, we used the `eval()` function to ensure that the values are lists. Additionally, we used the `nutrition` list we just created and made 7 more columns by extracting the values of each `nutrition` list, of which correspond to `"[calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), and carbohydrates (PDV)]"` of each recipe. Therefore the `nutrition` column is now redundant so we will drop it. 

Finally, the last thing to clean is to make sure that the values in `submitted` and `date` are correctly stored as DateTime objects rather than strings, so we used `pd.to_datetime()` to complete the data cleaning process. The resulting DataFrame has 234428 rows and 24 columns

The first 5 rows of our cleaned DataFrame is showed below:

| name                                 |     id |   minutes |   contributor_id | submitted           | tags                                                                                                                                                                                                                        |   n_steps | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | description                                                                                                                                                                                                                                                                                                                                                                       | ingredients                                                                                                                                                                    |   n_ingredients |   user_id |   recipe_id | date                |   rating | review                                                                                                                                                                                                                                                                                                                                           |   recipe_avg_rating |   calories |   total_fat |   sugar |   sodium |   protein |   sat_fat |   carbs |
|:-------------------------------------|-------:|----------:|-----------------:|:--------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|----------:|------------:|:--------------------|---------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------:|-----------:|------------:|--------:|---------:|----------:|----------:|--------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil', 'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted', 'remove from heat and let cool to room temperature', 'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated', 'add cooled chocolate and mix until uniform in color', 'add flour and stir until just incorporated', 'transfer batter to the prepared baking dish', 'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes', 'remove from the oven and cool completely before cutting']                                                  | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven!                                                                                                              | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 |    386585 |      333281 | 2008-11-19 00:00:00 |        4 | These were pretty good, but took forever to bake.  I would send it ended up being almost an hour!  Even then, the brownies stuck to the foil, and were on the overly moist side and not easy to cut.  They did taste quite rich, though!  Made for My 3 Chefs.                                                                                   |                   4 |      138.4 |          10 |      50 |        3 |         3 |        19 |       6 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                               |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder', 'set aside', 'in another mixing bowl , blend together the sugars , margarine , and salt until light and fluffy', 'add the eggs , water , and vanilla to the margarine / sugar mixture and mix together until well combined', 'add in the flour mixture to the wet ingredients and blend until combined', 'scrape down the sides of the bowl and add the chocolate chips', 'mix until combined', 'scrape down the sides to the bowl again', 'using an ice cream scoop , scoop evenly rounded balls of dough and place of cookie sheet about 1 - 2 inches apart to allow for spreading during baking', 'bake for 10 - 15 minutes or until golden brown on the outside and soft & chewy in the center', 'serve hot and enjoy !'] | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just use butter (softened) instead.                                                                                                                                            | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |              11 |    424680 |      453467 | 2012-01-26 00:00:00 |        5 | Originally I was gonna cut the recipe in half (just the 2 of us here), but then we had a park-wide yard sale, & I made the whole batch & used them as enticements for potential buyers ~ what the hey, a free cookie as delicious as these are, definitely works its magic! Will be making these again, for sure! Thanks for posting the recipe! |                   5 |      595.1 |          46 |     211 |       22 |        13 |        51 |      26 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |     29782 |      306168 | 2008-12-31 00:00:00 |        5 | This was one of the best broccoli casseroles that I have ever made.  I made my own chicken soup for this recipe. I was a bit worried about the tsp of soy sauce but it gave the casserole the best flavor. YUM!                                                                                                                                  |                   5 |      194.8 |          20 |       6 |       32 |        22 |        36 |       3 |
|                                      |        |           |                  |                     |                                                                                                                                                                                                                             |           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                |                 |           |             |                     |          | The photos you took (shapeweaver) inspired me to make this recipe and it actually does look just like them when it comes out of the oven.                                                                                                                                                                                                        |                     |            |             |         |          |           |           |         |
|                                      |        |           |                  |                     |                                                                                                                                                                                                                             |           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                |                 |           |             |                     |          | Thanks so much for sharing your recipe shapeweaver. It was wonderful!  Going into my family's favorite Zaar cookbook :)                                                                                                                                                                                                                          |                     |            |             |         |          |           |           |         |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |   1196280 |      306168 | 2009-04-13 00:00:00 |        5 | I made this for my son's first birthday party this weekend. Our guests INHALED it! Everyone kept saying how delicious it was. I was I could have gotten to try it.                                                                                                                                                                               |                   5 |      194.8 |          20 |       6 |       32 |        22 |        36 |       3 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30 00:00:00 | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |    768828 |      306168 | 2013-08-02 00:00:00 |        5 | Loved this.  Be sure to completely thaw the broccoli.  I didn&#039;t and it didn&#039;t get done in time specified.  Just cooked it a little longer though and it was perfect.  Thanks Chef.                                                                                                                                                     |                   5 |      194.8 |          20 |       6 |       32 |        22 |        36 |       3 |

### Univariate Analysis
<iframe
  src="assets/univariate.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In the histogram above, we see the distribution of ratings for each interaction in our merged DataFrame. On the X-axis, we have the ratings (ranging from 1-5) and on the Y-axis we have the number of interactions that gave that specific rating. We can observe that the histogram is heavily left-skewed, meaning that of the people who decided to leave an interactions, most gave a rating of 5.

### Bivariate Analysis
<iframe
  src="assets/bivariate_rating_vs_prep.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The scatterplot above allows us to vizualize the relationship between a recipe's ratings and its preperation time. We see that most points land in the left most fifth of the graph grid, however this does not mean that all of those recipes have short preperation times. We'd like to emphasize the range of the X-axis, which, in this case, is in minutes, yet we see that the right most label on the X-axis is 1M. This is due to our data set having an outlier in terms of preperation time, having a time of 1051200 minutes and a recipe title of "how to preserve a husband". We can still see, if we zoom in, that most interactions are made on recipes that requires less than 1000 minutes to prepare.

### Interesting Aggregates

|   rating |     mean |   size |
|---------:|---------:|-------:|
|        1 | 10.63    |   2870 |
|        2 | 10.6976  |   2368 |
|        3 |  9.99205 |   7172 |
|        4 |  9.57743 |  37307 |
|        5 |  9.9849  | 169676 |


In the grouped table above, we were able to group interactions by their ratings and we see that lower ratings (1-2) have, on average, more steps in the recipe than those with higher ratings. However, we remain cautious when considering whether this difference in steps is significant due to the difference in sizes of each rating group.

---
## Assessment of Missingness

### NMAR Analysis
We believe that in our DataFrame, the column that most resembles a missingness of NMAR is `description`. Missing values in `description` might be because of the rarity of its necessary ingredients. If all ingredients are common for a specifc recipe, it would likely not have a description as there are nothing to elaborate upon. On the other hand, if a recipe requires a rare ingredient, `description` would likely be filled with some information about how and where to find said ingredient(s) also some reminders if needed. So, to make `description`'s missingness MAR, we can add a column with boolean values that uses TF-IDF to find whether a recipe involves a rare ingredient.

### Missingness Dependency

We first test the missingness dependency of the `minutes` column and `rating` column.

Null: Distribution of `minutes` when `rating` is missing is the same as the distribution when `rating` is not missing.


Alternative: Distribution of `minutes` when `rating` is missing is different from the distribution when `rating` is not missing.


Significance value: 0.05

We will conduct a permutation test with 5000 iterations, shuffling the `rating` column, and will be looking at the absolute difference in mean minutes for these two distributions.

<iframe
  src="assets/minutes_vs_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From the histogram above with an extra line representing the observed difference, we can see that the observed difference lies between a good amount of data points. This means that from our permutation test, we found times where the absolute differece is greater than the observed. To verify this, we calculated the p-value, which came out to be 0.1188 which is higher than the 0.05 significance value we plan to use, therefore we fail to reject the null hypothesis, so we say the distributions are the same and missingness in `rating` does not depend on `minutes`.

Next, we will test the missingness dependency of the `n_steps` columns and `rating` column.

Null: Distribution of `n_steps` when `rating` is missing is the same as the distribution when `rating` is not missing.


Alternative: Distribution of `n_steps` when `rating` is missing is different from the distribution when `rating` is not missing.


Significance value: 0.05

We will conduct a permutation test with 5000 iterations, shuffling the `rating` column, and will be looking at the absolute difference in mean n_steps for these two distributions.

<iframe
  src="assets/n_steps_vs_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Unlike `minutes`, we see that the observed difference line lies all the way to the right, with no points beyond it, which means that in our 5000 permutations, we did not see a single instance where the absolute difference for the sample is greater than the observed difference. In other words, we can say it is very likely that missingness in `rating` does depend on `n_steps`. To verify this, we calculated the p-value which was 0.0, which is less than 0.05, so we can reject our null hypothesis concluding that the distributions are different.

---
## Hypothesis Testing

Next up, we will be conducting a hypothesis test to find whether recipes with rare ingredient(s) have lower ratings than those with only common ingredient(s). We created a function that gives us a list of the top 30 most common ingredients (using TF-IDF) in a list from the dataset and we added a new column `has_rare_ingredient` with boolean values, True if it contains ingredients not in the top 30, False otherwise. 


Null Hypothesis: Recipes that include rare ingredient(s) have the same mean ratings than those with common ingredient(s)


Alternative Hypothesis: Recipes that include rare ingredient(s) have lower mean ratings than those with common ingredient(s)


Test statistic: mean rating for recipes with rare ingredient(s) - mean rating for recipes without rare ingredient(s)


Significance value: 0.05

<iframe
  src="assets/hypothesis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

From our test, we ended up with a p-value of 1.0 which is higher than our significance level of 0.05, which means that we don't have sufficient evidence to reject the null hypothesis, therefore we can conclude that recipes with rare ingredient(s) tend to have the same ratings than those without.

---
## Prediction Problem

### Problem Identification
We will be using 4 columns, specifically `minutes`, `sugar`, `has_rare_ingredient`, to create a regression that predicts `calories`. We decided to choose `calories` as the response variable as we believe that it best summarizes a recipe. For the metrics that we will use to assess our model, we decided to use root mean squared error (RMSE) and the model's accuracy (R^2). The use of both accuracy and RMSE over other metrics like F-1 score is because our model will be a regression rather than a classification, hence RMSE and accuracy will give us a more complete understanding of our model's predicting performance.

---
## Baseline Model
Our model is a regression that uses `minutes`, `sugar`, and `has_rare_ingredient` to ultimately predict `calories`. Values in `minutes` (time needed to follow the recipe), and `sugar` (percent daily value) are quantitative while `has_rare_ingredient` is a nominal (boolean) column. To encode these columns, we created a `ColumnTransformer` that takes in the quantitative columns and standardizes them using `StandardScaler()` and we also one-hot encoded `has_rare_ingredients`. Overall, we believe that our model is mediocre at predicting `calories`, with our RMSE for our training set being slightly lower than the RMSE for the testing set (419.13, 435.67 respectively), not overfitting, while maintaining an accuracy of 0.417.

---
## Final Model
To improve upon our baseline model, we decided to add 3 more features. For `carbs`, we will split it into 3 categories low, med, high, and one-hot encode that. For `n_steps`, we decided to binarize this using `Binarizer()` setting a threshold of 9 as we found out that around half of the recipes in our dataset has less than 9 steps. We also decided to add `protein` to our `StandardScaler()`. We know that there's likely a very high correlation between `total_fat`, `sat_fat`, and `calories`, which is why we decided to avoid these columns as we want to assess other columns' effects on predictions. Our new and improved model's has a lower RMSE for both training and testing sets (290.84, 312.23 respectively) which is around 100 less than our baseline model while its accuracy/score improved to 0.700. We also added `PolynomialFeatures(d)` to our pipeline and we tried different (hyper)parameters for `d`, also known as the degree of the polynomial and from the graph below, found that a degree of 3 is our optimal hyperparameter.

<iframe
  src="assets/hyperparameter.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---
## Fairness Analysis
The two groups we decided to test fairness on are recipes that includes rare ingredients and recipes that don't.

Null Hypothesis: Our model is fair. Its precision for recipes including rare ingredients and recipes not including rare ingredients are roughly the same, and any differences are due to random chance.

Alternative Hypothesis: Our model is unfair. Its precision for recipes including rare ingredients than its precision for recipes not including rare ingredients.

Test Statistic: RMSE

Significance Value: 0.05

We conducted a permutation test, permutating the `has_rare_ingredient` column and compared the distribution of the permutated sample with the original distribution by using RMSE. We ended up with a p-value of 0.85 which is higher than the significance value of 0.05, hence we fail to reject our null hypothesis, therefore we can say that our model is fair.
