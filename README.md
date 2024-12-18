# Are your online recipes even healthy?
### An Analysis led by Ryan Stephen (ryanstep@umich.edu)

## Introduction

Many people turn to recipes tagged as "healthy" when cooking at home to be more conscious of what they are eating. But how "healthy" are these recipes really? This analysis dives into Food.com's extensive recipe database to investigate whether recipes tagged as 'healthy' actually have better nutritional profiles, and how they're received by the cooking community through ratings.

Using a dataset of 83,782 recipes from Food.com, we explore various nutritional metrics including calories, protein, fat, and carbohydrate content, along with user ratings to understand if the 'healthy' tag is a reliable indicator of nutritional quality. The analysis leverages the following key columns:

- `name`: Recipe name
- `tags`: Food.com tags for recipe (used to identify 'healthy' tagged recipes)
- `nutrition`: Nutritional information including calories, fat, sugar, sodium, protein, saturated fat, and carbohydrates (we will be paying special attention to these values)
- `average_rating`: Average user rating for the recipe
- `ingredients`: List of ingredients used in the recipe
- `n_ingredients`: Number of ingredients in the recipe

Through this analysis, we aim to help home cooks make more informed decisions about their recipe choices and understand the relationship between the 'healthy' tag, nutritional content, and recipe popularity.

## Data Cleaning and Exploratory Data Analysis

### Initial merged dataset (food.com's raw reviews and interactions):
```py
print(recipes_with_ratings[['name', 'tags', 'is_healthy', 'calories', 'protein_pdv']].head().to_markdown(index=True))
```
After processing and cleaning our dataset, we have 83,782 recipes with 22 columns, including:
- Basic recipe information (name, steps, ingredients)
- Nutritional data (calories, protein, fat, etc.)
- Recipe metadata (submission date, ratings)
- Health indicators (is_healthy flag, health_score)

Here's a glimpse of our processed data, focusing on recipe names, health status, calories, and protein content:

| Name | Is Healthy | Calories | Protein (% DV) |
|------|------------|----------|----------------|
| 1 brownies in the world best ever | False | 138.4 | 3 |
| 1 in canada chocolate chip cookies | False | 595.1 | 13 |
| 412 broccoli casserole | False | 194.8 | 22 |
| millionaire pound cake | False | 878.3 | 20 |
| 2000 meatloaf | False | 267.0 | 29 |

The full dataset includes the following columns:
- Recipe details: name, minutes, n_steps, steps, ingredients, n_ingredients
- User interaction: contributor_id, submitted, average_rating
- Nutritional information: calories, total_fat_pdv, sugar_pdv, sodium_pdv, protein_pdv, saturated_fat_pdv, carbohydrates_pdv
- Health metrics: is_healthy, health_score

### Univariate Analysis
Let's first examine how recipes are rated on Food.com:

<iframe
  src="assets/rating_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
The distribution of recipe ratings shows a strong positive skew, with an average rating of 4.68. Most recipes receive ratings between 4 and 5 stars, suggesting users tend to rate recipes quite favorably or may only rate recipes they enjoyed.
<iframe
  src="assets/num_ingredients.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
Most recipes on Food.com use between 8-12 ingredients, with a mean of 9.2 ingredients per recipe. Interestingly, the distribution is slightly right-skewed, showing that while some complex recipes use over 20 ingredients, they are relatively rare on the platform. This insight helps us understand recipe complexity and sets a baseline for comparing healthy versus non-healthy recipes.

### Bivariate Analysis

let's examine the relationship between calories and protein content, comparing healthy and non-healthy recipes:

<iframe
 src="assets/calorie_vs_protein.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>

The scatter plot reveals an interesting relationship between calories and protein content. While both healthy and non-healthy recipes show a general trend of increasing protein with higher calories, healthy recipes (shown in blue) tend to cluster in the lower-calorie, lower-protein region. This suggests that recipes tagged as healthy do tend not to prioritize protein-to-calorie ratio, further supporting the notion that the idea of "healthy" is very relative to an individual's goals.

Let's also look at how recipe ratings compare between healthy and non-healthy recipes:

<iframe
 src="assets/ratings_vs_health.html"
 width="800"
 height="600"
 frameborder="0"
></iframe>

The box plot comparison shows surprisingly similar rating distributions between healthy and non-healthy recipes, with both categories maintaining high median ratings at 5, however the lower fence of healthy recipes is about ~0.4 lower than others). This suggests that taste satisfaction isn't sacrificed too much in popular healthy recipes, challenging the common perception that more 'healthy' foods are less enjoyable.

### Interesting Aggregates

Here's a comprehensive comparison of nutritional profiles between healthy and non-healthy recipes:

```py
print(nutrition_comparison.to_markdown(index=True))
```

| Recipe Type | Calories | Protein (% DV) | Sugar (% DV) | Total Fat (% DV) | Sodium (% DV) | Carbs (% DV) | Avg Rating | Count |
|-------------|----------|----------------|--------------|------------------|---------------|--------------|------------|--------|
| Non-Healthy | 444.6    | 34.42          | 64.35        | 36.79            | 28.67         | 12.80        | 4.63       | 70,052 |
| Healthy     | 355.1    | 26.58          | 90.70        | 11.39            | 30.32         | 18.82        | 4.59       | 13,730 |

This aggregation reveals several interesting patterns. Healthy recipes tend to have about 90 fewer calories on average and significantly lower fat content (11.4% vs 36.8% DV). However, they surprisingly contain higher sugar and carbohydrate content. The similar average ratings (4.59 vs 4.63) suggest that healthier options don't compromise on taste. It's worth noting that healthy recipes make up only about 16% of the dataset, with 13,730 recipes compared to 70,052 non-healthy recipes.

### Missing Values and Imputation

Our analysis of missing values in the dataset revealed:

```py
print(missing_info.to_markdown(index=True))
```

| Column          | Missing Values | Percentage |
|-----------------|----------------|------------|
| name            | 1             | 0.00%      |
| description     | 70            | 0.08%      |
| average_rating  | 2,609         | 3.11%      |

Given the low percentage of missing values, I chose not to perform imputation for most columns. The missing recipe name (0.00%) and descriptions (0.08%) are negligible and don't impact our analysis of nutritional content and health tags. For average ratings, which has a slightly higher missing rate of 3.11%, I kept the missing values as is since this represents recipes that haven't been rated yet and imputing values could introduce bias in our analysis of recipe popularity. Notably, all nutritional information fields were complete, allowing for robust analysis of our main research question about healthy versus non-healthy recipe characteristics.

## Framing a Prediction Problem
"Can we predict whether a recipe will be tagged as "healthy" based on its nutritional information?"

This is a binary classification problem where we'll predict whether a recipe should be tagged as 'healthy' based on its nutritional information. We'll use F1-score as our evaluation metric since we want to balance precision (avoiding false 'healthy' labels) and recall (not missing truly healthy recipes). At prediction time, we would have access to all nutritional information (calories, fat, protein, etc.) and ingredient counts, but would exclude user ratings and reviews since these wouldn't be available for a new recipe being classified.

## Baseline Model
Our baseline model attempts to predict whether a recipe will be tagged as "healthy" using four quantitative features from the nutritional information:

Calories (total calories in recipe)
Sugar (% daily value)
Sodium (% daily value)
Total Fat (% daily value)

All features were standardized using StandardScaler to ensure they're on the same scale, and a logistic regression classifier was used as our baseline model. The model was implemented as a scikit-learn Pipeline to ensure proper scaling of both training and test data.
The model achieved an F1 score of 0.183 on our test set, which is relatively poor performance (for context, random guessing would achieve around 0.1 F1 score given the class imbalance in our dataset). This suggests that simply looking at these basic nutritional metrics isn't sufficient to identify what recipes are tagged as "healthy" on Food.com.
Looking at the feature coefficients:

1. Calories (3.59): Surprisingly, higher calorie content is positively associated with the "healthy" tag
2. Total Fat (-7.57): Strong negative association with "healthy" tag
3. Sugar (-0.61): Moderate negative association
4. Sodium (-0.01): Very weak negative association

These coefficients reveal some interesting patterns - while high fat and sugar content do reduce the likelihood of a "healthy" tag as we might expect, the positive relationship with calories is counterintuitive. This suggests that the relationship between nutritional content and "healthy" tags is more complex than our baseline model can capture.
The low performance of this model indicates I'll need to consider additional features and potentially more sophisticated modeling approaches to better predict healthy recipe tags. Some potential improvements that I didn't consider for the baseline model:

1. Adding protein and carbohydrate content
2. Considering ingredient counts and types
3. Looking at cooking methods or preparation steps

## Final Model

I improved upon our baseline logistic regression model using a Random Forest Classifier and engineered features designed to capture more complex nutritional relationships.

### Feature Engineering 
I added two new features based on domain knowledge about nutrition:
1. **Healthy Nutrient Ratio**: Measures the balance of protein (a beneficial nutrient) relative to unhealthy components (fats and sugars). This helps capture whether a recipe has a good balance of macronutrients rather than just looking at absolute values.
2. **Unhealthy Nutrient Density**: Calculates the concentration of unhealthy nutrients (fats, sugars, sodium) per calorie. This helps distinguish between recipes that are caloric due to healthy versus unhealthy ingredients.

Our final feature set included:
* Base nutritional metrics (calories, total fat, protein, sugar, sodium, saturated fat)
* The two engineered features above

### Model Selection and Tuning
I chose a Random Forest Classifier because it can capture non-linear relationships between nutritional features and the "healthy" tag, which our baseline logistic regression couldn't do. To find the best model configuration, I performed a grid search over these hyperparameters:
* Number of trees: [50, 100]
* Maximum tree depth: [8, 10]

The best performing model used 100 trees with a maximum depth of 10.

### Performance
Our final model achieved an F1 score of 0.309, a 69% improvement over the baseline model's score of 0.183. This suggests that:
1. Our engineered features helped capture more meaningful nutritional relationships
2. The non-linear capabilities of Random Forests better model the complex relationship between nutrition and "healthy" labels

Looking at feature importances, total fat content (0.30) and saturated fat (0.18) were the strongest predictors of the "healthy" tag, followed by calories (0.17). Interestingly, our engineered features had relatively lower importance, suggesting that while they helped improve model performance, the basic nutritional metrics still carry the most predictive power.

While our final model shows clear improvement, the still-modest F1 score suggests that nutritional content alone isn't sufficient to fully predict what recipes get tagged as "healthy" on Food.com. This indicates that user perception of "healthy" recipes may involve factors beyond pure nutritional metrics and may be quite arbitrary or open to definition!
