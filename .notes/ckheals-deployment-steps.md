# CK Heals Website Deployment Steps

## Step 1: Export from Webflow
1. Log into Webflow
2. Go to the CK Heals project
3. Navigate to Project Settings → Hosting
4. Click "Export" and download the .zip file

## Step 2: Extract and Add Files
```bash
# Extract the Webflow zip to this project directory
unzip ~/Downloads/ckheals-export.zip -d .

# Make sure we have an index.html at the root
ls -la index.html

# Check that all files are present
ls -la
```

## Step 3: Commit and Push
```bash
# Add all files to git
git add .

# Commit the changes
git commit -m "Add Webflow export files for CK Heals website"

# Push to GitHub
git push
```

## Step 4: Configure GitHub Pages
1. Go to https://github.com/yourusername/ckheals (replace with your actual repo)
2. Click "Settings"
3. Scroll to "Pages" in the left sidebar
4. Set Source to "Deploy from a branch"
5. Select "main" branch and "/ (root)" folder
6. Click "Save"

## Step 5: Set Up Custom Domain
1. In GitHub Pages settings, add "www.ckheals.com" in the Custom domain field
2. Click "Save"
3. Update DNS settings for ckheals.com at your domain registrar:
   - Add a CNAME record for "www" pointing to "yourusername.github.io"
   - Add A records for the apex domain (ckheals.com) pointing to GitHub's IPs:
     ```
     185.199.108.153
     185.199.109.153
     185.199.110.153
     185.199.111.153
     ```
4. Wait for DNS propagation (up to 48 hours)
5. Check "Enforce HTTPS" once domain is verified

## Step 6: Verify the Website
1. Visit www.ckheals.com (or the GitHub Pages URL) to ensure everything works
2. Check all pages, images, and links
3. Test the contact form functionality

## Making Future Updates
```bash
# Extract new Webflow export
unzip ~/Downloads/new-ckheals-export.zip -d .

# Commit and push changes
git add .
git commit -m "Update website content"
git push
```

GitHub Pages will automatically rebuild the site after each push.

## Important Notes
- Keep the CNAME file in your repository if using a custom domain
- Ensure all file paths are correct (case sensitive)
- Test the site thoroughly after each update 