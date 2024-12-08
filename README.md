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

## Baseline Model

## Final Model
