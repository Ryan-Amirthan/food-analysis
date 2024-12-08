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
print(recipes_with_ratings.head().to_markdown(index=True))
```
Our final dataset contains 83,782 recipes with 22 columns, including nutritional information, ratings, and recipe characteristics. Here's a look at the first few rows of our cleaned dataset:

| Name | Minutes | N Ingredients | Average Rating | Calories | Health Score | Is Healthy |
|------|----------|---------------|----------------|-----------|--------------|------------|
| 1 brownies in the world best ever | 40 | 9 | 4.0 | 138.4 | 2 | False |
| 1 in canada chocolate chip cookies | 45 | 11 | 5.0 | 595.1 | 2 | False |
| 412 broccoli casserole | 40 | 9 | 5.0 | 194.8 | 2 | False |
| millionaire pound cake | 120 | 7 | 5.0 | 878.3 | 1 | False |
| 2000 meatloaf | 90 | 13 | 5.0 | 267.0 | 3 | False |
## Framing a Prediction Problem

## Baseline Model

## Final Model
