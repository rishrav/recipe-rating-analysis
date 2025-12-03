---
layout: default
---

# Recipe Rating Analysis

**Name**: Rishvik Ravi

**Website Link**: [https://rishrav.github.io/recipe-rating-analysis/](https://rishrav.github.io/recipe-rating-analysis/)

---

## Step 1: Introduction

### Dataset Overview

This project analyzes two complementary datasets from an online recipe platform:

- **`RAW_recipes.csv`**: Contains **104,592 rows**, each describing a unique recipe with attributes like cooking time, ingredients, steps, tags, and nutritional information.
- **`RAW_interactions.csv`**: Contains **822,681 rows**, each corresponding to a user interaction (rating and/or review) on a recipe.

### Central Question

**What kinds of recipes tend to receive the highest ratings from home cooks?**

This question explores how recipe characteristics (cooking time, number of ingredients, tags, nutritional content, etc.) relate to user satisfaction as measured by ratings.

### Why This Matters

Understanding which features are associated with highly-rated recipes helps:
- **Home cooks** discover better recipes faster
- **Content creators** design recipes that people actually enjoy
- **Platforms** improve recipe ranking and recommendation systems

This analysis addresses practical decisions like "What should I cook tonight?" and makes online recipe platforms more useful and trustworthy.

### Relevant Columns

**From `RAW_recipes.csv`**:
- **`id`**: Unique identifier for each recipe (links to interactions)
- **`name`**: Recipe title
- **`minutes`**: Total time required to prepare and cook the recipe
- **`tags`**: List of tags describing the recipe (course, cuisine, dietary restrictions, time constraints)
- **`nutrition`**: List of nutritional statistics (calories, fat, sugar, etc.)
- **`n_steps`**: Number of steps in the recipe's instructions (complexity measure)
- **`n_ingredients`**: Number of distinct ingredients used (complexity measure)

**From `RAW_interactions.csv`**:
- **`recipe_id`**: Identifier matching `id` in `RAW_recipes.csv`
- **`user_id`**: Unique identifier for each user
- **`rating`**: Numeric rating (1-5 scale) - our main measure of recipe quality
- **`review`**: Free-text written feedback from users
- **`date`**: Date when the interaction occurred

---

## Step 2: Data Cleaning and Exploratory Data Analysis

### Data Cleaning

The data cleaning process involved:

1. **Interactions data**:
   - Replaced rating values of 0 with NaN (0 indicates "no rating")
   - Dropped rows with missing ratings
   - Ensured ratings are integers on the 1-5 scale
   - Converted date column to datetime format

2. **Recipes data**:
   - Filtered to keep recipes with positive, reasonable cook times (0 < minutes ≤ 1440)
   - Filtered to keep recipes with positive counts for steps and ingredients

3. **Merged dataset**:
   - Joined interactions with recipes using recipe ID
   - Created a single analysis DataFrame combining user feedback with recipe attributes

**Cleaned DataFrame head**:

*(After running your notebook, use `_df.head().to_markdown(index=False)` to generate the markdown table and paste it here)*

### Univariate Analysis

**Distribution of Recipe Ratings**:

<iframe
  src="assets/rating-distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The rating distribution shows that most recipes receive relatively high scores (4-5 stars), with very few extremely low ratings. This suggests users tend to be generous with ratings.

**Distribution of Cooking Time**:

<iframe
  src="assets/cooking-time-distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Cooking times are right-skewed; most recipes take under roughly an hour, but there's a long tail of recipes requiring significantly more time. This indicates the platform contains a mix of quick everyday recipes and longer, more involved dishes.

### Bivariate Analysis

**Rating vs. Cooking Time**:

<iframe
  src="assets/rating-vs-minutes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The scatter plot suggests that extremely short and extremely long recipes both tend to have slightly more variable ratings, while many moderately timed recipes cluster at high ratings. The trendline is fairly flat, indicating that cook time alone may not strongly determine user satisfaction.

**Rating vs. Number of Ingredients**:

<iframe
  src="assets/rating-vs-ingredients.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The box plot shows that recipes with a moderate number of ingredients often achieve high median ratings, while very simple or very complex recipes can have more spread in their ratings. This hints that there may be an optimal level of complexity that home cooks favor.

### Interesting Aggregates

**Average Ratings by Cooking Time Quartiles**:

*(After running your notebook, use `agg_time.to_markdown(index=False)` to generate the markdown table and paste it here)*

This table shows how average ratings and engagement vary across different cooking time categories, providing insights into whether recipe duration affects user satisfaction.

---

## Step 3: Assessment of Missingness

*(Add your missingness analysis here when completed)*

---

## Step 4: Hypothesis Testing

### Hypotheses

We test whether recipes that take 30 minutes or less ("quick" recipes) receive different average ratings than recipes that take more than 30 minutes ("long" recipes).

- **Null Hypothesis (H₀)**: The mean rating for quick recipes is equal to the mean rating for long recipes. Any observed difference is due to random chance.
- **Alternative Hypothesis (H₁)**: The mean rating for quick recipes is different from the mean rating for long recipes (two-sided test).

**Test Statistic**: Difference in mean ratings (quick - long)

**Significance Level**: α = 0.05

We use a permutation test because it makes no assumptions about the distribution of ratings and allows us to simulate the null hypothesis by randomly shuffling the "quick" vs "long" labels.

### Results

**Observed difference**: *(Add your observed difference value here)*

**P-value**: *(Add your p-value here)*

**Conclusion**: *(Add your conclusion based on the p-value)*

<iframe
  src="assets/hypothesis-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---

## Step 5: Framing a Prediction Problem

### Prediction Problem Statement

**Problem**: Predict the rating (on a 1-5 scale) that a user will give to a recipe based on the recipe's characteristics.

**Problem Type**: **Regression** — We are predicting a continuous numerical value (rating from 1 to 5).

**Response Variable**: `rating` — The numerical star rating (1-5) that a user assigns to a recipe.

**Why this response variable?** User ratings are a direct measure of recipe quality and user satisfaction. Predicting ratings helps recipe platforms recommend better recipes, helps users discover recipes they're likely to enjoy, and provides insights into what recipe characteristics lead to positive experiences.

**Evaluation Metric**: **Root Mean Squared Error (RMSE)**

**Why RMSE?** 
- RMSE is interpretable in the same units as our target variable (rating points)
- RMSE penalizes large prediction errors more heavily than MAE
- RMSE is a standard metric for regression problems

**Features Available at Prediction Time**: 
- Recipe attributes: cooking time (`minutes`), number of steps (`n_steps`), number of ingredients (`n_ingredients`), recipe tags (`tags`), nutritional information (`nutrition`), recipe description (`description`), recipe name (`name`), and ingredient list (`ingredients`).

**Features NOT Available**: Aggregated statistics about recipe performance, interaction dates, user-specific information.

---

## Step 6: Baseline Model

### Baseline Model Description

For our baseline model, we use a simple linear regression with three features:

1. **`minutes`** (quantitative) - Cooking time in minutes, standardized
2. **`n_ingredients`** (quantitative) - Number of ingredients, standardized  
3. **`tags`** (nominal) - Recipe tags parsed and encoded as binary features for the top 20 most common tags

**Model**: Ridge Regression (Linear Regression with L2 regularization)

**Pipeline**: All feature transformations and model training are combined in a single sklearn Pipeline using ColumnTransformer.

### Performance

**Training RMSE**: *(Add your training RMSE here)*

**Test RMSE**: *(Add your test RMSE here)*

**Assessment**: The baseline model establishes a performance floor. Given that ratings are on a 1-5 scale, the RMSE indicates how many stars off our predictions are on average.

---

## Step 7: Final Model

### Feature Engineering

To improve upon the baseline model, we engineered the following new features:

1. **`n_steps`** (quantitative): Number of steps in the recipe, capturing recipe complexity
2. **Nutritional features** (7 quantitative features): Parsed from `nutrition` column - calories, total_fat, sugar, sodium, protein, saturated_fat, carbs
3. **`minutes_per_step`** (interaction feature): Calculated as `minutes / (n_steps + 1)`, capturing recipe pacing
4. **QuantileTransformer on `minutes`**: Transforms the right-skewed distribution to uniform for better model performance

**Model Algorithm**: RandomForestRegressor

**Hyperparameters Tuned**:
- `n_estimators`: [50, 100, 200]
- `max_depth`: [10, 20, 30, None]
- `min_samples_split`: [2, 5, 10]

**Best Hyperparameters**: *(Add your best hyperparameters from GridSearchCV here)*

### Performance Comparison

**Baseline Test RMSE**: *(Add baseline RMSE here)*

**Final Model Test RMSE**: *(Add final model RMSE here)*

**Improvement**: *(Add improvement value and percentage here)*

<iframe
  src="assets/predicted-vs-actual.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---

## Step 8: Fairness Analysis

### Fairness Analysis: Quick vs Long Recipes

We investigate whether our final model performs differently for different groups of recipes:

- **Group X**: Quick recipes (≤30 minutes cooking time)
- **Group Y**: Long recipes (>30 minutes cooking time)

**Evaluation Metric**: RMSE (Root Mean Squared Error)

**Hypotheses**:
- **Null Hypothesis (H₀)**: Our model is fair. The RMSE for quick recipes and long recipes are roughly the same, and any observed difference is due to random chance.
- **Alternative Hypothesis (H₁)**: Our model is unfair. The RMSE for quick recipes is different from the RMSE for long recipes (two-sided test).

**Test Statistic**: Difference in RMSE (RMSE_quick - RMSE_long)

**Significance Level**: α = 0.05

### Results

**Quick recipes RMSE**: *(Add RMSE for quick recipes here)*

**Long recipes RMSE**: *(Add RMSE for long recipes here)*

**Observed difference**: *(Add observed difference here)*

**P-value**: *(Add p-value from permutation test here)*

**Conclusion**: *(Add your conclusion based on the p-value)*

<iframe
  src="assets/fairness-analysis.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

---

## Conclusion

This analysis explored what characteristics lead to highly-rated recipes. Through hypothesis testing, we examined whether quick and long recipes receive different ratings. Through predictive modeling, we built models to predict user ratings based on recipe features. Through fairness analysis, we assessed whether our model performs equitably across different recipe types.

The insights from this project can help recipe platforms improve their recommendation systems and help home cooks discover recipes they're more likely to enjoy.

---

*Note: This website is a work in progress. Run your notebook to generate the actual plots and results, then embed them in the appropriate sections above.*
