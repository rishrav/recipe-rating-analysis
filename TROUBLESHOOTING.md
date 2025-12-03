# GitHub Pages 404 Error - Troubleshooting Steps

## Step 1: Verify GitHub Pages is Enabled

1. Go to your repository: https://github.com/rishrav/recipe-rating-analysis
2. Click on **Settings** (in the repository toolbar)
3. Click on **Pages** in the left sidebar
4. Under **Source**, make sure:
   - Branch is set to **main**
   - Folder is set to **/ (root)**
   - Click **Save**

## Step 2: Check for Build Errors

1. Go to **Actions** tab in your repository
2. Look for any failed workflow runs
3. If there are errors, they'll show what's wrong

## Step 3: Wait for Build

GitHub Pages can take 1-5 minutes to build after you push changes or enable Pages.

## Step 4: Verify Files are Pushed

Make sure all files are committed and pushed:
- `_config.yml`
- `index.md`
- `Gemfile`

## Step 5: Check Repository Visibility

Make sure your repository is **public** (GitHub Pages only works with public repos on free accounts).

