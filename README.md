# Predicting-Recipes-Ratings
# Predicting Recipe Ratings
A UCSD DSC80 project predicting the average recipe ratings.

Contributors: Adalina Ma and Jenna Canicosa 


## Table of Contents
- [Introduction](#introduction)
    - [Dataset Statistics and Definitions](#dataset-statistics-and-definitions)
- [Data Cleaning](#data-cleaning)
- [Framing the Problem](#framing-the-problem-problem-identification)
- [Baseline Model](#baseline-model)
- [Final Model](#final-model)
- [Fairness Analysis](#fairness-analysis)


## Introduction
Welcome to Recipe and Ratings Analysis! We used 2 datasets to conduct this analysis, one containing recipes and the other ratings from Food.com which was last updated in 2008. Bodhisattwa Prasad Majumder, Shuyang Li, Jianmo Ni, and Julian McAuley originally scrapped and used this data. The original recipes dataset held over 83,000 unique recipes and the ratings dataset held over 731,000 recipe reviews. With the large amount of data and information, there were many types of approaches to this project. We decided to conduct our data analysis on checking if we can **predict the rating of recipes based on other columns of the dataset.** People might be interested in our conclusion because they can figure out what recipes people seemed to approve of based on other columns of the dataset. Someone might be interested in the dataset because they enjoy cooking and want to know what recipes people seem to like and why. 


### Dataset Statistics and Definitions:
As stated above, the original recipes dataset has a total of 83,782 unique recipes and the ratings dataset has a total of 731,927 recipe reviews. We merged the two datasets and cleaned it so it only includes the necessary columns for our analysis. Below are the definitions of the columns we used from this dataset.
Recipes: Information found on [food.com](food.com)

| **Column Name**     | **Definition**                                               |
| ------------------- | ------------------------------------------------------------ |
| `name`            | Recipe name                                                    |
| `id`  | Recipe ID |
| `minutes`          | Minutes to prepare recipe              |
| `contributor_id`             | User ID who submitted this recipe                                      |
| `submitted`            | Date recipe was submitted                                      |
| `tags`           | Food.com tags for recipe                                     |
| `nutrition` | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value”                       |
| `n_steps`         | Number of steps in recipe                                       |
| `steps`          | Text for recipe steps, in order                                |
| `description `          | User-provided description                             |

Ratings

| **Column** | **Description** |
| -------------------------- | ----------- |
| *user_id*                      | User ID         |
| *recipe_id*                      | Recipe ID      |
| *date*                      | Date of interaction      |
| *rating*                      | Rating given      |
| *review*                      | Review text     |


### Data Cleaning
For this project, we followed the same procedure for data cleaning as Project 3. Here is a clear outline of the steps we took to obtain our dataset:

The first step of our data cleaning process was to merge our two datasets together. We performed a left merge on the recipes and interactions datasets together. 

The second step of our data cleaning process was to fill all ratings of 0 with` np.nan`. We performed this step, because the lowest rating that you can give is one star, so we can automatically assume that if a rating contains zero stars, no rating was given.  

The third step of our data cleaning process was to find the average rating per recipe, as a Series. We grouped by `id` before finding the mean of `ratings`. We made sure to rename the columns to `ratings` and `avg_rating`. After adding this Series containing the average rating per recipe back to the dataset.  

The fourth step of our data cleaning process was to filter out recipes that contained less than 5 reviews. We grouped by `id` before finding the count of the recipe `name` and then sorted the values and getting all recipe names with 5 or more reviews. We then merged this to our dataset and named it `count`. 

The fifth step of our data cleaning process was to filter out recipes that took over 120 minutes because according to the data below, we noticed almost 90 percent of the data was within 120 minutes. And this additionally helped filter out any outliers within the data. 

<p float = 'left'> 
    <iframe src="assets/univariate-plots/uni-fig-mid.html" width=410 height=275 frameBorder=0></iframe>
    <iframe src="assets/univariate-plots/uni-fig-bot.html" width=410 height=275 frameBorder=0></iframe> 
<p float = 'left'> 
    <iframe src="assets/univariate-plots/uni-fig-top.html" width=410 height=275 frameBorder=0></iframe>
    <iframe src="assets/univariate-plots/uni-fig-sup.html" width=410 height=275 frameBorder=0></iframe> 
<p style="text-align:center"><iframe src="assets/univariate-plots/uni-fig-jng.html" width=410 height=275 align='center' frameBorder=0></iframe></p>
</p>
</p>

For the last step to clean the dataset, we grouped the data based on recipes and put all reviews of each recipe into lists separated by ‘user_id’ and ’ratings’.

The final step specifically for answering our question was choosing to 
The first five rows of our dataframe are included below:
<table border="1" class="dataframe" width='100%'> <thead>    <tr style="text-align: center;">      <th></th>      <th>name</th>      <th>id</th>      <th>user_id</th>      <th>ratings	</th>      <th>n_steps</th>      <th>avg_rating</th>      <th>minutes</th>    <th>contributor_id</th>    </tr>  </thead>  <tbody>    <tr>      <th>0</th>      <td>0 carb 0 cal gummy worms</td>      <td>283618</td>      <td>[824257.0, 871801.0, 2000269452.0, 2001637319.0, 1803681343.0]</td>      <td>[5.0, 4.0, 5.0, nan, 5.0]</td>      <td>15</td>      <td>4.75</td>      <td>45</td>     <td>134414</td>    </tr>    <tr>      <th>1</th>      <td>0 point soup ww</td>      <td>391705</td>      <td>[166642.0,1535.0,228458.0,464080.0,4470.0,1754326.0,204024.0,37779.0,2000225933.0]
</td>      <td>[5.0, 5.0, 5.0, 5.0, 5.0, 4.0, 5.0, 4.0, 5.0]</td>      <td>5</td>      <td>4.777777777777778</td>      <td>55</td>     <td>39835</td>    </tr>    <tr>      <th>2</th>      <td>1 minute cake</td>      <td>290187</td>      <td>[227652.0,
 693373.0, 646093.0, 765161.0, 879045.0, 872425.0, 213139.0, 299046.0, 1383715.0, 308765.0, 1993336.0]</td>      <td>[4.0, 4.0, 4.0, 4.0, 4.0, 5.0, 4.0, 5.0, 1.0, 4.0, 5.0]</td>      <td>20</td>      <td>4.0</td>      <td>2</td>    <td>584365</td>    </tr>    <tr>      <th>3</th>      <td>10 calorie chocolate miracle noodle cookies</td>      <td>478546</td>      <td>[2249984.0, 1802657711.0, 2000301575.0, 2000920973.0, 2001773359.0]</td>      <td>[3.0, 3.0, 4.0, nan, nan]</td>      <td>26</td>      <td>3.3333333333333335</td>      <td>16</td>    <td>2247203</td>    </tr>    <tr>      <th>4</th>      <td>10 minute baked halibut with garlic butter sauce</td>      <td>359203</td>      <td>[4470.0, 90633.0, 653438.0, 369715.0, 242188.0, 1800042302.0, 1803231273.0]</td>      <td>[5.0, 5.0, 5.0, 5.0, 5.0, 5.0, 5.0]</td>      <td>16</td>      <td>5.0</td>      <td>35</td>   <td>37779</td>    </tr>  </tbody></table>


## Framing the Problem: Problem Identification
**Prediction problem:** Can we predict the ‘rating’ of recipes based on other columns in the dataset?

We approached this prediction problem by building a regression model to predict the ‘avg_rating’ which represents the average ratings based on reviews by users. The goal is to predict the average rating of recipes based on other columns of the dataset. Predicting ratings can be valuable for understanding user preferences and recommending recipes.

At the time of prediction, we would have access to the features available in the cleaned dataset, ‘name’, ‘id’, ‘user_id’, ‘ratings’, ‘n_steps’, ‘avg_rating’, ‘minutes’, ‘contributor_id’, and ‘n_ingredients’. However, not all of these features will be used for the prediction. For example, the id of a user does not tell us anything about the recipe nor the rating it received. The relevant features can be used to train a regression model to predict the average rating of a recipe.

To evaluate the model's performance, we will use the root mean squared error (RMSE) as the metric. RMSE measures the average deviation between the predicted ratings and the actual ratings. It is suitable for regression tasks and provides a measure of how well the model predicts the continuous response variable. It is important to note that the lower values of RMSE indicate better model performance. RMSE is a suitable metric for capturing the accuracy of ratings prediction because it gives more weight to larger deviations by effectively penalizing them and considering the magnitude of errors. 

At the time of prediction, we would know the values of the features used for training the model. These features include 'n_steps', 'minutes', 'contributor_id', and any additional engineered features. The model should only use these features to make predictions and not rely on any future or external information that would not be available during the prediction phase.


## Baseline Model
The model we created is a linear regression model. It aims to predict the average rating of recipes based on three features: 'n_steps', 'minutes', and 'n_ingredients'. 

The model includes the following quantitative features:
 'n_steps': The number of steps in the recipe.
 'minutes': The total time necessary to make the recipe.
‘'n_ingredients': The number of ingredients used in the recipe.

The numerical features are preprocessed using the StandardScaler, which standardizes the features by removing the mean and scaling to unit variance. This step ensures that all features are on a similar scale. 

The model's performance is evaluated using two metrics: root mean squared error (RMSE) and R-squared score. 

The RMSE measures the average distance between the predicted ratings and the actual ratings. For the training set, the RMSE is 0.3389, and for the test set, it is 0.3145. A lower RMSE indicates better performance and the values we obtained suggest a reasonable level of accuracy in predicting the average ratings of recipes.

R-squared score measures the proportion of the variance in the target variable that is predictable from the features. A higher R-squared score indicates a better fit of the model to the data. However, it's important to note that R-squared can be negative when the model performs worse than a horizontal line. For the training set, the R-squared score is 0.0024, and for the test set, it is -0.0026. 

Considering the model's performance, the RMSE values indicate a relatively low prediction error. However, the low R-squared scores indicate that the model may not capture a significant amount of variance in the target variable based on the given features.

It's important to consider the specific requirements and context of the problem to determine whether the model is considered "good." While the RMSE values are relatively low, the low R-squared scores suggest that there may be other factors not captured by the current features, leading to poor predictive performance.

Further analysis, feature engineering, or exploring different models may be necessary to improve the model's performance and capture more meaningful patterns in the data.

