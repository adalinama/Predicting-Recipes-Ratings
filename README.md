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
Welcome to Recipe and Ratings Analysis! We used 2 datasets to conduct this analysis, one containing recipes and the other ratings from Food.com which was last updated in 2008. Bodhisattwa Prasad Majumder, Shuyang Li, Jianmo Ni, and Julian McAuley originally scrapped and used this data. The original recipes dataset held over 83,000 unique recipes and the ratings dataset held over 731,000 recipe reviews. With the large amount of data and information, there were many types of approaches to this project. We decided to conduct our data analysis on checking if we can **predict the rating of recipes based on other columns of the dataset.** People might be interested in our conclusion because they can figure out what recipes people seemed to approve of based on other columns of the dataset such as the number of steps or how long the recipe is predicted to take. Someone might be interested in the dataset because they enjoy cooking and want to know what recipes people seem to like and why. 


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
| `description`          | User-provided description                             |

Ratings

| **Column** | **Description** |
| -------------------------- | ----------- |
| *user_id*                      | User ID         |
| *recipe_id*                      | Recipe ID      |
| *date*                      | Date of interaction      |
| *rating*                      | Rating given      |
| *review*                      | Review text     |


### Data Cleaning
For this project, we followed the same procedure for data cleaning as in Project 3. Here is a clear outline of the steps we took to obtain our dataset:

The first step of our data-cleaning process was to merge our two datasets together. We performed a left merge on the recipes and interactions datasets together. 

The second step of our data cleaning process was to fill all ratings of 0 with` np.nan`. We performed this step, because the lowest rating that you can give is one star, so we can automatically assume that if a rating contains zero stars, no rating was given.  

The third step of our data-cleaning process was to find the average rating per recipe, as a Series. We grouped by `id` before finding the mean of `ratings`. We made sure to rename the columns to `ratings` and `avg_rating`. After adding this Series containing the average rating per recipe back to the dataset.  

The fourth step of our data-cleaning process was to filter out recipes that contained less than 5 reviews. We grouped by `id` before finding the count of the recipe `name` and then sorted the values and got all recipe names with 5 or more reviews. We then merged this to our dataset and named it `count`. 

The fifth step of our data cleaning process was to filter out recipes that took over 120 minutes because according to the data below, we noticed almost 90 percent of the data was within 120 minutes. And this additionally helped filter out any outliers within the data. 

<p float = 'left'> 
    <iframe src="assets/uni-fig-all.html" width='100%' height= 435 align='center' frameBorder=0></iframe>
</p>

For the last step to clean the dataset, we grouped the data based on recipes and put all reviews of each recipe into lists separated by `user_id` and `ratings`.

The final step specifically for answering our question was choosing to 
The first five rows of our dataframe are included below:
<table border="1" class="dataframe" width='100%'> <thead>    <tr style="text-align: center;">      <th></th>      <th>name</th>      <th>id</th>      <th>user_id</th>      <th>ratings	</th>      <th>n_steps</th>      <th>avg_rating</th>      <th>minutes</th>    <th>contributor_id</th>    </tr>  </thead>  <tbody>    <tr>      <th>0</th>      <td>0 carb 0 cal gummy worms</td>      <td>283618</td>      <td>[824257.0, 871801.0, 2000269452.0, 2001637319.0, 1803681343.0]</td>      <td>[5.0, 4.0, 5.0, nan, 5.0]</td>      <td>15</td>      <td>4.75</td>      <td>45</td>     <td>134414</td>    </tr>    <tr>      <th>1</th>      <td>0 point soup ww</td>      <td>391705</td>      <td>[166642.0,1535.0,228458.0,464080.0,4470.0,1754326.0,204024.0,37779.0,2000225933.0]
</td>      <td>[5.0, 5.0, 5.0, 5.0, 5.0, 4.0, 5.0, 4.0, 5.0]</td>      <td>5</td>      <td>4.777777777777778</td>      <td>55</td>     <td>39835</td>    </tr>    <tr>      <th>2</th>      <td>1 minute cake</td>      <td>290187</td>      <td>[227652.0,
 693373.0, 646093.0, 765161.0, 879045.0, 872425.0, 213139.0, 299046.0, 1383715.0, 308765.0, 1993336.0]</td>      <td>[4.0, 4.0, 4.0, 4.0, 4.0, 5.0, 4.0, 5.0, 1.0, 4.0, 5.0]</td>      <td>20</td>      <td>4.0</td>      <td>2</td>    <td>584365</td>    </tr>    <tr>      <th>3</th>      <td>10 calorie chocolate miracle noodle cookies</td>      <td>478546</td>      <td>[2249984.0, 1802657711.0, 2000301575.0, 2000920973.0, 2001773359.0]</td>      <td>[3.0, 3.0, 4.0, nan, nan]</td>      <td>26</td>      <td>3.3333333333333335</td>      <td>16</td>    <td>2247203</td>    </tr>    <tr>      <th>4</th>      <td>10 minute baked halibut with garlic butter sauce</td>      <td>359203</td>      <td>[4470.0, 90633.0, 653438.0, 369715.0, 242188.0, 1800042302.0, 1803231273.0]</td>      <td>[5.0, 5.0, 5.0, 5.0, 5.0, 5.0, 5.0]</td>      <td>16</td>      <td>5.0</td>      <td>35</td>   <td>37779</td>    </tr>  </tbody></table>

## Framing the Problem: Problem Identification
**Prediction problem:** Can we predict the ‘rating’ of recipes based on other columns in the dataset?

We approached this prediction problem by building a regression model to predict the `avg_rating` which represents the average ratings based on reviews by users. The goal is to predict the average rating of recipes based on other columns of the dataset. Predicting ratings can be valuable for understanding user preferences and recommending recipes.

At the time of prediction, we would have access to the features available in the cleaned dataset, `name`, `id`, `user_id`, `ratings`, `n_steps`, `avg_rating`, `minutes`, `contributor_id`, and `n_ingredients`. However, not all of these features will be used for the prediction. For example, the id of a user does not tell us anything about the recipe or the rating it received. The relevant features can be used to train a regression model to predict the average rating of a recipe.

To evaluate the model's performance, we will use the root mean squared error (RMSE) as the metric. RMSE measures the average deviation between the predicted ratings and the actual ratings. It is suitable for regression tasks and provides a measure of how well the model predicts the continuous response variable. We aim to have a low RMSE value because a lower score indicates a better model performance.

## Baseline Model
The model we created is a linear regression model. It aims to predict the average rating of recipes based on three features: `n_steps`, `minutes`, and `n_ingredients`. 

The model includes the following quantitative features:
`n_steps`: The number of steps in the recipe.
`minutes`: The total time necessary to make the recipe.
`n_ingredients`: The number of ingredients used in the recipe.

The numerical features are preprocessed using the StandardScaler, which standardizes the features by removing the mean and scaling to unit variance. This step ensures that all features are on a similar scale. 

The model's performance is evaluated using two metrics: root mean squared error (RMSE) and R-squared score. 

The RMSE measures the average distance between the predicted ratings and the actual ratings. For the training set, the RMSE is 0.3389, and for the test set, it is 0.3145. A lower RMSE indicates better performance and the values we obtained suggest a reasonable level of accuracy in predicting the average ratings of recipes.

The R-squared score measures the proportion of the variance in the target variable that is predictable from the features. A higher R-squared score indicates a better fit of the model to the data. However, a R-squared can be negative when the model performs worse than a horizontal line. For the training set, the R-squared score is 0.0024, and for the test set, it is -0.0026. Because of this, one of our goals for the Final Model is to improve the R-squared score and RMSE. 


## Final Model
For our final model, we chose to include 2 new features: 
`low_calorie`: We established a dish as “low calorie” when it is less than 500 calories. We created a new column to differentiate recipes with under 500 calories with a 1, otherwise, it has a 0.
`ingredient_time_ratio`: The minutes of a recipe over the number of ingredients.
These additional features provide insight into the data by revealing how the model can improve based on the information and other factors. Low calorie can affect the ratings of recipes because some people may prefer lower-calorie recipes and find higher-calorie recipes as “unhealthy” so they would give low-calorie recipes a higher rating. The ingredient-to-time ratio measures the ratio of time required to prepare a recipe to the number of ingredients used in the recipe. It quantifies the complexity of a recipe. Higher values suggest more complex recipes vs. lower values require less time and tend to be more simple. 

At first, we tried using Polynomial Features to test our data to see if we improved the model’s performance. We began this process by using a StandardScaler() to standardize the numerical features. We then created a pipeline using PolynomialFeatures() and a linear regression model LinearRegression(). To find the best degree parameter, we choose to use GridSearchCV() as our hyperparameter to minimize the mean squared error. Below is the result:

Degree 1:
Training Set:
Root Mean Squared Error: 0.33888976058534387
R-squared Score: 0.002496892225647418

Test Set:
Root Mean Squared Error: 0.31460135810083106
R-squared Score: -0.0033411305297359473

Degree 2:
Training Set:
Root Mean Squared Error: 0.3380502705966843
R-squared Score: 0.007432755054219697

Test Set:
Root Mean Squared Error: 0.3152351960667313
R-squared Score: -0.0073881336693362165

Degree 3:
Training Set:
Root Mean Squared Error: 0.33742809172638727
R-squared Score: 0.011083017271622086

Test Set:
Root Mean Squared Error: 0.3163067068364738
R-squared Score: -0.014248165668983237

For all three degrees (1, 2, and 3), the model exhibits a low R-squared score, indicating that `avg_rating` is not well explained by the features in the dataset. The R-squared scores are close to zero or slightly negative, revealing that the model still has a lot of room for improvement. Additionally, the root mean squared error (RMSE) values are relatively high meaning that there is a significant difference between the actual target values and the predicted values. 

Overall, we noticed that the polynomial regression model with degrees 1, 2, and 3 did not improve the model’s performance and is not a great indicator of the relationship between the features and the `avg_rating`. In fact, as the degree increases, the model’s performance decreases slightly. Using GridSearchCV() indicated that our best polynomial degree was 1.

We proceeded to explore alternative approaches to improve the model's performance. Next, we explored how a RandomForestRegressor impacted the model. The best hyperparameter found using grid search revealed a maximum depth of 3 for each decision tree using 100 estimators. After running this, the training set RMSE was 0.3365 and R-squared value was 0.0166. For the test set, we got an RMSE of 0.3144 and R-squared score of -0.0019. We were still unsatisfied with these results because there was not much improvement from the Baseline Model.

At this point, we hit a roadblock where we couldn’t improve the regression model because we kept getting about the same RMSE and R-squared values with different approaches. We had to go back to brainstorm other ways to tackle this problem. Ultimately, we removed the low-calorie feature because we found that it did not improve the model. Our model lacked categorical values so we explored how other columns in our original cleaned dataset can help us improve the result of our model. 

We decided to use the features `minutes`, `n_ingredients`, `tags`, `calories`, `avg_rating`, `recipe_age`, `n_steps`,  and `ingredient_time_ratio`. All of these features are numerical except `tags` which are categorical. For `avg_rating`, we created a new column titled `rating_category` which sorts the ratings into 4 bins. Another feature we created was `recipe_age` which takes the date that each recipe was submitted and subtracts it from the current date.

The features are standardized using StandardScaler for  `minutes`, `n_ingredients`,  `calories`, `avg_rating`, `recipe_age`, `n_steps`,  and `ingredient_time_ratio`. 

How each feature improved the model:

`minutes`: This column can provide insights into how the cooking time affects the rating category. Recipes that require longer preparation time might have different characteristics and appeal, leading to variations in the rating category.

`n_ingredients`: This feature represents the number of ingredients used in the recipe. Recipes with a higher number of ingredients might offer more complex flavor and have a different impact on the rating category.

`calories`: This feature represents the number of calories in the recipe. People's preferences for dishes with different calorie levels can affect the rating category.

`avg_rating`: This feature allows the model to consider past ratings as a predictor for the rating category. Recipes with higher average ratings might be more popular and receive better ratings overall. 

`recipe_age`: This feature represents the age of the recipe since it was submitted. It captures the notion of recipe popularity and its relevance over time. Recipes that have been around for a longer time may have gained more reviews and feedback, which can influence the rating category.

`n_steps`: This feature represents the number of steps or instructions in the recipe which can provide insights into the complexity and level of detail in the cooking process. Recipes with more steps might require additional effort, potentially affecting the rating category.

`ingredient_time_ratio`: This feature represents the ratio of cooking time to the number of ingredients. Recipes with lower ratios may be perceived as easier or quicker to prepare and could impact the rating category.

OneHotEncoder is used for  `tags`. The hyperparameters are from the RandomForestRegressor: {`regressor__max_depth`: 3, `regressor__min_samples_split`: 10, `regressor__n_estimators`: 200}. The resulting training set RMSE of 0.0009 suggests that the model's predictions on the training set are very close to the actual values. The R-squared score of 0.9999 tells us the model is effective at predicting the rating category. The test set had an RMSE of 0.0012 and R-squared score of 0.9999 suggesting that the model's predictions on the test set are also very close to the actual values. 

In conclusion, we decided to use the RandomForestRegressor model as it produced results with the highest RSME and R-squared score improvement among all models. 

## Fairness Analysis
Fairness analysis refers to the process of assessing whether a predictive model is biased or not against certain groups in a population. To evaluate the fairness of our final model, we assigned Group X: Recipes with calories less than 500
Group Y: Recipes with calories greater than or equal to 500
 We set up a null and alternative hypothesis to explore the possibility of bias:

**Null hypothesis:** Our model is fair. Its RSME for recipes with calories less than 500 (Group X) and recipes with calories greater than or equal to 500 (Group Y) are roughly the same, and any differences are due to random chance.

**Alternative hypothesis:**Our model is unfair. Its RSME for recipes with calories less than 500 (Group X) is lower than its RSME for recipes with calories greater than or equal to 500 (Group Y).

For our fairness analysis, we choose to use RMSE (Root Mean Squared Error) as our evaluation metric, with the test statistic being the difference in RMSE scores between the two groups: recipes with calories less than 500 (Group X) and recipes with calories greater than or equal to 500 (Group Y). Our significance level was set at 0.05, and after running 1000 permutation tests, we got a p-value of 0.837. 

<p style="text-align:center"><iframe src="assets/file-name.html" width=800 height=425 align='center' frameBorder=0></iframe></p>

In conclusion, based on the resulting p-value of 0.837, being greater than our significance level of 0.05, we fail to reject the null hypothesis. There is no evidence of unfairness in the model and its RMSE for recipes with calories less than 500 (Group X) is lower than its RSME for recipes with calories greater than or equal to 500 (Group Y). Our conclusion seems to indicate that our model is fair and any observed differences in RMSE scores between the two groups are due to random chance.

