# How to Export Plots for Your Website

## Step 1: Run Your Complete Notebook

Make sure you've executed all cells in your `template.ipynb` notebook and all your plots have been created.

## Step 2: Add Export Code to Your Notebook

Add a new cell at the end of your notebook with the following code:

```python
# Export all plots to HTML files for website embedding

# Univariate plots (Step 2)
fig_rating.write_html('../recipe-rating-analysis/assets/rating-distribution.html', include_plotlyjs='cdn')
fig_minutes.write_html('../recipe-rating-analysis/assets/cooking-time-distribution.html', include_plotlyjs='cdn')

# Bivariate plots (Step 2)
fig_rating_minutes.write_html('../recipe-rating-analysis/assets/rating-vs-minutes.html', include_plotlyjs='cdn')
fig_rating_ingredients.write_html('../recipe-rating-analysis/assets/rating-vs-ingredients.html', include_plotlyjs='cdn')

# Hypothesis testing plot (Step 4)
# Find the variable name for your permutation test plot (might be 'fig' or 'fig_hypothesis')
# Uncomment and update the variable name:
# fig.write_html('../recipe-rating-analysis/assets/hypothesis-test.html', include_plotlyjs='cdn')

# Final model visualization (Step 7)
# Find the variable name for your predicted vs actual plot (might be 'fig' or 'fig_model')
# Uncomment and update the variable name:
# fig.write_html('../recipe-rating-analysis/assets/predicted-vs-actual.html', include_plotlyjs='cdn')

# Fairness analysis plot (Step 8)
fig_fairness.write_html('../recipe-rating-analysis/assets/fairness-analysis.html', include_plotlyjs='cdn')

print("All plots exported successfully!")
```

## Step 3: Export DataFrame Tables

For the "Data Cleaning" and "Interesting Aggregates" sections, you'll need to convert DataFrames to markdown.

### For the cleaned DataFrame head:

```python
# In your notebook, after creating _df:
print(_df.head().to_markdown(index=False))
```

Copy the output and paste it into the "Cleaned DataFrame head" section of `index.md`.

### For the aggregates table:

```python
# In your notebook, after creating agg_time:
print(agg_time.to_markdown(index=False))
```

Copy the output and paste it into the "Average Ratings by Cooking Time Quartiles" section of `index.md`.

## Step 4: Commit and Push

After exporting all plots and updating the markdown tables:

```bash
cd /Users/rishvikravi/Desktop/recipe-rating-analysis
git add assets/*.html
git add index.md
git commit -m "Add interactive plots and data tables"
git push
```

Your website will automatically update within a few minutes!

