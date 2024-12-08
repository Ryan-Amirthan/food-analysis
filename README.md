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
The low performance of this model indicates we'll need to consider additional features and potentially more sophisticated modeling approaches to better predict healthy recipe tags. Some potential improvements that I didn't consider for the baseline model:

1. Adding protein and carbohydrate content
2. Considering ingredient counts and types
3. Looking at cooking methods or preparation steps

## Final Model
